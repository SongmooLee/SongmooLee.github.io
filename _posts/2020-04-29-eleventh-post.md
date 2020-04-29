---
title: "스터디 7주차-2"
date: 2020-04-29
categories: Study
---

## 바인더 코드 분석

### 바인더 드라이버 소스 구조

<br>.drivers/staging/android/binder.c 파일이 실제 바인더 드라이버 파일.

```
static const struct file_operations binder_fops = {
.owner = THIS_MODULE,
.poll = binder_poll,
.unlocked_ioctl = binder_ioctl,
.mmap = binder_mmap,
.open = binder_open,
.flush = binder_flush,
.release = binder_release,
};


static struct miscdevice binder_miscdev = {
.minor = MISC_DYNAMIC_MINOR,
.name = "binder",
.fops = &binder_fops
};

binder_init() {
 ...
 ret = misc_register(&binder_miscdev);
}
```

위 코드는 실제 바인더 드라이버의 초기화 과정이다. 즉, "/dev/binder" 는 miscdevice 로서 초기화되고, binder_fops의 함수들이 통신에서 사용된다.

<br>또한 binder 는 아래와 같은 proc 파일을 통해 개발자에게 디버깅용 정보를 제공한다.

```
/proc/binder/state
/proc/binder/stats
/proc/binder/transactions
/proc/binder/transactions_log
```

prelinked 방식 (프로그램 컴파일 시 링크 단계에서 배치될 코드와 데이터 미리 정해두는 것. 어디에 배치될지) 에 의해서 mmap 을 별도의 메모리 영역에 배치할 수 있다.

실제 바인더 드라이버를 open 하고, 바인더 드라이버를 사용하여 통신하는 서비스 프레임워크(서비스 매니저)의 코드는 frameworks/base/cmds/servicemanager/binder.c 에 존재한다.

_ _ _

<br>
```
void binder_loop(struct binder_state *bs, binder_handler func)
{
    int res;
    struct binder_write_read bwr;
    uint32_t readbuf[32];
    bwr.write_size = 0;
    bwr.write_consumed = 0;
    bwr.write_buffer = 0;
    readbuf[0] = BC_ENTER_LOOPER;
    binder_write(bs, readbuf, sizeof(uint32_t));
    for (;;) {
        bwr.read_size = sizeof(readbuf);
        bwr.read_consumed = 0;
        bwr.read_buffer = (uintptr_t) readbuf;
        res = ioctl(bs->fd, BINDER_WRITE_READ, &bwr);
        if (res < 0) {
            ALOGE("binder_loop: ioctl failed (%s)\n", strerror(errno));
            break;
        }
        res = binder_parse(bs, 0, (uintptr_t) readbuf, bwr.read_consumed, func);
        if (res == 0) {
            ALOGE("binder_loop: unexpected reply?!\n");
            break;
        }
        if (res < 0) {
            ALOGE("binder_loop: io error %d %s\n", res, strerror(errno));
            break;
        }
    }
}
```

<br>실제 service_manager.c (frameworks/native/cmds/servicemanager 내 존재) 파일을 보면 main 함수에서 binder를 위한 메모리를 할당한 다음 selinux 가 활성화 되어 있으면 관련 확인 작업을 한 뒤 바로 binder_loop 함수를 호출하는데 그 함수 내부에는 무한루프를 돌면서 지속적으로 ioctl을 보내는 루틴을 확인할 수 있다. ioctl 명령이 있는것으로 보아 여기서 커널에 존재하는 binder driver와 통신을 하고 있다는 것을 알 수 있다.

_ _ _

<br>
예를 들어,

<br>
```
public class LocalService extends Service {
    // Binder given to clients
    private final IBinder binder = new LocalBinder();
    // Random number generator
    private final Random mGenerator = new Random();

    /**
     * Class used for the client Binder.  Because we know this service always
     * runs in the same process as its clients, we don't need to deal with IPC.
     */
    public class LocalBinder extends Binder {
        LocalService getService() {
            // Return this instance of LocalService so clients can call public methods
            return LocalService.this;
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        return binder;
    }

    /** method for clients */
    public int getRandomNumber() {
      return mGenerator.nextInt(100);
    }
}
```

<br>LocalBinder는 LocalService의 현재 인스턴스를 검색하기 위한 getService() 메서드를 클라이언트에 제공합니다. 이렇게 하면 클라이언트가 서비스 내의 공개 메서드를 호출할 수 있습니다. 예를 들어 클라이언트는 서비스에서 getRandomNumber()를 호출할 수 있습니다.

다음은 버튼을 클릭했을 때 LocalService에 바인딩되어 getRandomNumber()를 호출하는 액티비티를 나타냅니다.

_ _ _

<br>
전에 정리한 "스터디 7주차-1"에서 "원격 서비스란, 사용자(액티비티) 와 서비스가 서로 다른 프로세스에서 동작하는 것이다. 그렇기 때문에 IPC 가 필요하다. <b>따라서 원격 서비스의 동작원리를 살펴보는 것이, 안드로이드 서비스에서 실제 바인더가 사용되는 부분이 어디인지를 확인할 수 있다.</b>" 라고 했기에 한번 remoteService쪽 소스코드를 살펴보자.

일단, <b>바인딩을 제공하는 서비스를 생성할 때는 클라이언트가 서비스와 상호작용하는 데 사용할 수 있는 프로그래밍 인터페이스를 제공하는 IBinder를 제공해야 합니다.</b> 라고 명시되있다.

/android/apis/app/RemoteService.java 소스 코드 내에서

```
// BEGIN_INCLUDE(exposing_a_service)
    @Override
    public IBinder onBind(Intent intent) {
        // Select the interface to return.  If your service only implements
        // a single interface, you can just return it here without checking
        // the Intent.
        if (IRemoteService.class.getName().equals(intent.getAction())) {
            return mBinder;
        }
        if (ISecondary.class.getName().equals(intent.getAction())) {
            return mSecondaryBinder;
        }
        return null;
    }
```

메소드를 확인 할 수 있다. 조건문이 있긴 하지만 전에 본 구조 처럼 IBinder 오버라이드를 해주는 것을 확인가능

<br>
```
         /* Class for interacting with the main interface of the service.
         */
        private ServiceConnection mConnection = new ServiceConnection() {
            public void onServiceConnected(ComponentName className,
                    IBinder service) {
                // This is called when the connection with the service has been
                // established, giving us the service object we can use to
                // interact with the service.  We are communicating with our
                // service through an IDL interface, so get a client-side
                // representation of that from the raw service object.
                mService = IRemoteService.Stub.asInterface(service);
                mKillButton.setEnabled(true);
                mCallbackText.setText("Attached.");
                // We want to monitor the service for as long as we are
                // connected to it.
                try {
                    mService.registerCallback(mCallback);
                } catch (RemoteException e) {
                    // In this case the service has crashed before we could even
                    // do anything with it; we can count on soon being
                    // disconnected (and then reconnected if it can be restarted)
                    // so there is no need to do anything here.
                }
                // As part of the sample, tell the user what happened.
                Toast.makeText(Binding.this, R.string.remote_service_connected,
                        Toast.LENGTH_SHORT).show();
            }
            public void onServiceDisconnected(ComponentName className) {
                // This is called when the connection with the service has been
                // unexpectedly disconnected -- that is, its process crashed.
                mService = null;
                mKillButton.setEnabled(false);
                mCallbackText.setText("Disconnected.");
                // As part of the sample, tell the user what happened.
                Toast.makeText(Binding.this, R.string.remote_service_disconnected,
                        Toast.LENGTH_SHORT).show();
            }
        };
```

Android 시스템이 클라이언트와 서비스 사이에 연결을 설정하면 ServiceConnection에서 onServiceConnected()을 호출합니다. onServiceConnected() 메서드에는 IBinder 인수가 포함되고 클라이언트는 이를 사용하여 바인딩된 서비스와 통신합니다.// 위소스 코드에서 확인 가능.

_ _ _

<br><b>메신저 사용</b>

인터페이스가 여러 프로세스에서 작동해야 하는 경우, Messenger로 서비스에 대한 인터페이스를 생성할 수 있습니다. 이 방식을 사용하면 서비스가 여러 가지 유형의 Message 객체에 응답하는 Handler를 정의합니다. 이 Handler가 Messenger의 기초가 되어 클라이언트와 IBinder를 공유하고, 클라이언트는 Message 객체를 사용해 서비스에 명령을 보낼 수 있게 됩니다. 그 외에도 클라이언트가 자체적으로 Messenger를 정의하여 서비스가 메시지를 돌려보내도록 할 수 있습니다.
이것이 프로세스 간 통신(IPC)을 수행하는 가장 간단한 방법입니다. Messenger가 모든 요청을 단일 스레드로 큐에 저장하므로 서비스를 스레드로부터 안전하게 설계할 필요가 없기 때문입니다.

```
/*
     * Our Handler used to execute operations on the main thread.  This is used
     * to schedule increments of our value.
     */
    private final Handler mHandler = new Handler() {
        @Override public void handleMessage(Message msg) {
            switch (msg.what) {.....

```

<br>이렇게 하면 클라이언트가 서비스에서 호출할 메서드가 없습니다. 대신 클라이언트는 메시지(Message 객체)를 전달하여 서비스가 Handler로 받을 수 있도록 합니다.
//즉 Handler가 msg 객체를 받아서 switch하는 소스코드를 확인 가능.




출처: https://lastyouth.tistory.com/19, https://developer.android.com/guide/components/bound-services#java