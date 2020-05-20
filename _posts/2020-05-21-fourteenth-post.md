---
title: "스터디 10주차"
date: 2020-05-21
categories: Study
---

### AIDL 관련

AIDL = 안드로이드 인터페이스 정의 언어

클라이언트와 서비스가 모두 동의한 프로그래밍 인터페이스를 정의하여 프로세스 간 통신(IPC)으로 서로 통신하게 할 수 있습니다. Android에서는 일반적으로 한 프로세스가 다른 프로세스의 메모리에 액세스할 수 없습니다. 따라서 객체들을 운영체제가 이해할 수 있는 원시 유형으로 해체하고 해당 경계에 걸쳐 마샬링해야 합니다. 이 마샬링을 위해 코드를 작성하는 것은 상당히 지루한 작업인데, Android는 AIDL을 이용해 그 작업을 대신 처리합니다.

* 마샬링 : 프로그래밍에서 마샬링은 RPC, RMI 등에서 클라이언트가 원격지(서로 다른 프로세스)의 메서드를 호출 시 서버에 넘겨지는 인자, 원격지 함수의 리턴 값들을 프로그래밍 인터페이스에 맞도록 그 데이터를 조직화하고, 미리 정해진 다른 형식으로 변환하는 과정을 말한다. XML로 마샬링, Byte 스트림으로 마샬링 등 데이터 교환시 어떠한 정해진 표준에 맞게 해당 데이터를 가공하는 것을 마샬링, 언마샬링 이라고 한다. 클라이언트에서 마샬링된 데이터를 서버에 전달하게 되면, 서버에서는 그 데이터를 언마샬링하여 사용함으로써 원격지(다른 프로세스)간의 데이터 사용이 가능하게 된다.

AIDL은 Android에서 사용되는 IPC (Inter process communication), 안드로이드 구조를 이야기할때 Binder라고 일컬어지는 부분을 사용하기 위해서 정의합니다. Remote Service 라는 것은 같은 프로세스가 아니라 다른 프로세스에서 오는 함수 호출을 처리하는 Service이고 그것을 연결해주는데 Binder가 사용되고 있습니다. 그 Binder를 사용하는 코드를 자동으로 생성시켜주는 것이 AIDL입니다.

* stub : 스텁은 원격 객체에 대한 메소드 호출을 실제 구현된 원격 객체가 있는 서버로 전송하는 일을 담당하는 대리자 역할을 하는 객체이다. 클라이언트 객체가 가지게 되는 원격 객체에 대한 참조는 실제로는 지역의 스텁에 대한 참조이다. 스텁 파일은 서버 쪽과 클라이언트 쪽 모두에 필요하다.

_ _ _

### AIDL 구성

AIDL은 Java의 primitive type에 대해서는 import 없이 쓸 수 있습니다. String, CharSequence, List류, Map류도 import 없이 사용 할 수 있습니다. 앞에서 제시한 primitive type과 기본 object 류를 제외하고는 parameter 값으로 사용할 때, in, out, inout 을 설정해줘야 합니다. ( in 이 default 입니다 )

<b><big>aidl의 default format</big></b>

```
package [PACKAGE NAME]
import [위에 정의되지 않은 TYPE들]

interface [INTERFACE NAME]{
  int [FUNCTION명]( [PARAMETERS] );
}

```

<br> 비교

```
// IRemoteService.aidl
package com.example.android;

// Declare any non-default types here with import statements

/** Example service interface */
interface IRemoteService {
    /** Request the process ID of this service, to do evil things with it. */
    int getPid();

    /** Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
}

```

.aidl 파일을 프로젝트의 src/ 디렉토리 아래의 적당한 경로에 저장하고 빌드를 하면, SDK툴이 IBinder 인터페이스 파일을 gen/ 디렉토리 아래의 대응되는 경로에 생성해 줍니다. 생성되는 IBinder 파일의 이름은 .aidl 파일의 이름과 같지만 확장자만 .java로 바뀝니다(예를 들어, IRemoteService.aidl에 의해서 생성된 파일은 IRemoteService.java입니다).

_ _ _


<b><big>Interface method 구현</big></b>

AIDL이 만든 인터페이스 파일은 Stub 이라는 내부 추상 클래스를 가지는데, 이 녀석을 구현해주어야 합니다.
이것은 부모인 인터페이스를 구현하는 추상 클래스입니다. 그리고 인터페이스 안에는 .aidl 파일 안에 있던 메소드들이 모두 선언되어 있습니다.

deafult format
```
// @ Service
private final [AIDL Interface 명].Stub mBinder = new [AIDL Interface 명].Stub(){
    // implementation
    public [return value] [.aidl에 정의한 함수명]{
        // to sth..
    }
}
```

<br>
아래 예제 코드는, 위의 IRemoteService.aidl 파일로부터 생성된 IRemoteService 인터페이스를 구현하는 것을 보여줍니다:

```
private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
    public int getPid(){
        return Process.myPid();
    }
    public void basicTypes(int anInt, long aLong, boolean aBoolean,
        float aFloat, double aDouble, String aString) {
        // Does nothing
    }
};

```

위의 코드에서 mBinder는 (Binder 클래스를 확장한) Stub 클래스의 객체이며, RPC(원격 프로시저 호출) 인터페이스를 정의하고 있습니다. 이 객체가 클라이언트에게 전달되며, 서비스와 상호작용할 수 있게 해주는 것입니다.

_ _ _

<b><big>클라이언트에 인터페이스 전달1</big></b>

서비스에 바인더 인터페이스를 구현했다면, 그것을 클라이언트가 바인드하여 가져갈 수 있도록 해줘야 합니다. 그러기 위해서는, 서비스의 onBind() 콜백 메소드를 구현하여, Stub을 구현한 객체를 리턴해줘야 합니다.


default format
```
@ Service
// several interface can be provided
public IBinder onBind( Intent intent ){
    if ( [AIDL 인터페이스 이름1].class.getName().equals( intent.getAction() ) ){
       return mBinder;
    } 
    if ( [AIDL 인터페이스 이름2].class.getName().equals( intent.getAction() ) ){
       return mSecondaryBinder;
    }
}

private final [AIDL 인터페이스 이름1].Stub mBinder = new  [AIDL 인터페이스 이름1] .Stub(){
  // impleement.
}
 
private final  [AIDL 인터페이스 이름2] .Stub mSecondaryBinder = new  [AIDL 인터페이스 이름2] .Stub(){
  // Implement.
}

```

<br>아래 예제 코드는 IRemoteService에 대한 구현 객체를 클라이언트에게 리턴해주는 것을 보여줍니다.

```
public class RemoteService extends Service {
    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return mBinder;
    }

    private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
        public int getPid(){
            return Process.myPid();
        }
        public void basicTypes(int anInt, long aLong, boolean aBoolean,
            float aFloat, double aDouble, String aString) {
            // Does nothing
        }
    };
}

```

_ _ _

<b><big>클라이언트에 인터페이스 전달2</big></b>

default format
```
[AIDL 인터페이스 이름] mService;

@ServiceConnection 의 onServiceConnected callback function
public void onServiceConnected( ComponentName name, IBinder service ){
     mService = [AIDL 인터페이스 이름].Stub.asInterface( service );

}

// 사용
try{
     mService.[aidl function]; 
}
catch( DeadObjectException e ){
     //...
} 

```

- - -

이제 (액티비티와 같은) 클라이언트가 서비스를 바인드하기 위해 bindService()를 호출하면, 서비스의 onBind()에서 리턴해주는 mBinder를, 클라이언트의 onServiceConnected() 콜백 메소드에서 받을 수 있습니다.

클라이언트는 넘겨받은 mBinder를 사용해야 하는데, 만약 클라이언트와 서비스가 서로 다른 앱에 있다면, 클라이언트 앱의 src/ 디렉토리 아래에는 서비스 앱의 .aidl 파일을 복사하여 저장해야 합니다(그래야 AIDL 메소드들을 호출할 수 있는 android.os.Binder 인터페이스에 대한 구현체가 클라이언트에 생성됩니다).

클라이언트가 onServiceConnected()에서 IBinder를 전달 받았다면, 이제 YourServiceInterface.Stub.asInterface(service)를 호출하여 YourServiceInterface 타입의 인터페이스를 받아서 그것을 사용하면 됩니다. 아래는 예제 코드입니다:

```
IRemoteService mIRemoteService;
private ServiceConnection mConnection = new ServiceConnection() {
    // Called when the connection with the service is established
    public void onServiceConnected(ComponentName className, IBinder service) {
        // Following the example above for an AIDL interface,
        // this gets an instance of the IRemoteInterface, which we can use to call on the service
        mIRemoteService = IRemoteService.Stub.asInterface(service);
    }

    // Called when the connection with the service disconnects unexpectedly
    public void onServiceDisconnected(ComponentName className) {
        Log.e(TAG, "Service has unexpectedly disconnected");
        mIRemoteService = null;
    }
};

```

_ _ _

<b><big>IPC로 객체 전달하기</big></b>

IPC(프로세스간 통신) 인터페이스를 통해 어떤 프로세스에서 다른 프로세스로 전송할 수 있는 클래스를 만들기 위해서는, 그 클래스가 Parcelable 인터페이스를 구현하고 있어야 합니다. 그것은 객체를 원시타입의 데이터로 분리하는 마샬링 과정을 포함하는데, 이는 안드로이드 시스템이 서로 다른 프로세스 간에 객체를 전달하는데에 있어서 중요한 과정입니다.

Parcelable 프로토콜을 지원하는 클래스를 만드는 과정은 아래와 같습니다: 

1. 클래스가 Parcelable 인터페이스를 구현하도록 선언합니다(클래스 선언부에 implements Parcelable 을 추가합니다).

2. writeToParcel 메소드를 구현하여, 매개변수로 받은 Parcel 객체에 본 객체의 현재 상태들을 저장합니다(writes).

3. Parcelable.Creator 인터페이스의 구현객체를 CREATOR라는 이름의 정적 변수에 저장합니다.

4. 마지막으로, parcelable 클래스를 선언하는 .aidl 파일을 생성합니다(아래에 Rect.aidl 파일이 있습니다). 만약 자체적인 빌드 프로세스를 사용한다면, .aidl 파일을 빌드에 추가하지 않아도 됩니다. C 언어에서의 헤더 파일처럼, .aidl 파일도 컴파일 되지는 않기 때문입니다.

AIDL은 위에서 설명한 메소드 및 변수를 이용하여 객체를 마샬링 및 언마샬링 합니다.

아래 코드는 Rect 클래스가 parcelable 하다고 선언해주는 Rect.aidl 파일의 코드입니다:

```
package android.graphics;

// Declare Rect so AIDL can find it and knows that it implements
// the parcelable protocol.
parcelable Rect;

```

<br>
그리고 아래 코드는 Rect 클래스가 Parcelable 프로토콜을 구현하는 방법을 보여줍니다:

```
import android.os.Parcel;
import android.os.Parcelable;

public final class Rect implements Parcelable {
    public int left;
    public int top;
    public int right;
    public int bottom;

    public static final Parcelable.Creator<Rect> CREATOR = new
Parcelable.Creator<Rect>() {
        public Rect createFromParcel(Parcel in) {
            return new Rect(in);
        }

        public Rect[] newArray(int size) {
            return new Rect[size];
        }
    };

    public Rect() {
    }

    private Rect(Parcel in) {
        readFromParcel(in);
    }

    public void writeToParcel(Parcel out) {
        out.writeInt(left);
        out.writeInt(top);
        out.writeInt(right);
        out.writeInt(bottom);
    }

    public void readFromParcel(Parcel in) {
        left = in.readInt();
        top = in.readInt();
        right = in.readInt();
        bottom = in.readInt();
    }
}

```

_ _ _

<b><big>IPC 메소드 호출하기</big></b>

AIDL로 정의된 인터페이스를 구현한 클래스를 클라이언트에서 호출하기까지의 과정은 아래와 같습니다:

1. 프로젝트의 src/ 디렉토리에 .aidl 파일을 추가합니다.

2. AIDL에 의해 생성된 IBinder 객체를 선언합니다.

3. ServiceConnection을 구현합니다.

4. Context.bindService() 메소드를 호출하는데, 이때 ServiceConnection의 구현객체를 인자로 넘깁니다.

5. ServiceConnection의 onServiceConnected() 콜백 메소드에서 IBinder 객체를 (service라는 매개변수로) 전달 받는데, 여기서 YourInterface.Stub.asInterface((IBinder)service) 를 호출하여 YourInterface 타입의 결과값을 받아 멤버변수로 저장합니다.

6. 이제 인터페이스에 정의한 메소드들을 호출하면 됩니다. 이때 DeadObjectException에 대한 예외처리를 해줘야 하는데, 이것은 원격 메소드 호출시에만 발생하는 예외로서, 이 경우에는 예상치 못하게 연결이 끊어진 상태에서 메소드를 호출했을 때 발생합니다.

7. 연결을 종료하기 위해서는 Context.unbindService()를 호출하는데, 이때 ServiceConnection의 구현객체를 인자로 넘깁니다.

<br>아래 예제 코드는 AIDL로 생성된 인터페이스를 호출하는 샘플 코드이며, ApiDemos 샘플앱의 RemoteService를 바인드하는 액티비티입니다.

```
public static class Binding extends Activity {
    /** The primary interface we will be calling on the service. */
    IRemoteService mService = null;
    /** Another interface we use on the service. */
    ISecondary mSecondaryService = null;

    Button mKillButton;
    TextView mCallbackText;

    private boolean mIsBound;

    /**
     * Standard initialization of this activity.  Set up the UI, then wait
     * for the user to poke it before doing anything.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.remote_service_binding);

        // Watch for button clicks.
        Button button = (Button)findViewById(R.id.bind);
        button.setOnClickListener(mBindListener);
        button = (Button)findViewById(R.id.unbind);
        button.setOnClickListener(mUnbindListener);
        mKillButton = (Button)findViewById(R.id.kill);
        mKillButton.setOnClickListener(mKillListener);
        mKillButton.setEnabled(false);

        mCallbackText = (TextView)findViewById(R.id.callback);
        mCallbackText.setText("Not attached.");
    }

    /**
     * Class for interacting with the main interface of the service.
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

    /**
     * Class for interacting with the secondary interface of the service.
     */
    private ServiceConnection mSecondaryConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // Connecting to a secondary interface is the same as any
            // other interface.
            mSecondaryService = ISecondary.Stub.asInterface(service);
            mKillButton.setEnabled(true);
        }

        public void onServiceDisconnected(ComponentName className) {
            mSecondaryService = null;
            mKillButton.setEnabled(false);
        }
    };

    private OnClickListener mBindListener = new OnClickListener() {
        public void onClick(View v) {
            // Establish a couple connections with the service, binding
            // by interface names.  This allows other applications to be
            // installed that replace the remote service by implementing
            // the same interface.
            bindService(new Intent(IRemoteService.class.getName()),
                    mConnection, Context.BIND_AUTO_CREATE);
            bindService(new Intent(ISecondary.class.getName()),
                    mSecondaryConnection, Context.BIND_AUTO_CREATE);
            mIsBound = true;
            mCallbackText.setText("Binding.");
        }
    };

    private OnClickListener mUnbindListener = new OnClickListener() {
        public void onClick(View v) {
            if (mIsBound) {
                // If we have received the service, and hence registered with
                // it, then now is the time to unregister.
                if (mService != null) {
                    try {
                        mService.unregisterCallback(mCallback);
                    } catch (RemoteException e) {
                        // There is nothing special we need to do if the service
                        // has crashed.
                    }
                }

                // Detach our existing connection.
                unbindService(mConnection);
                unbindService(mSecondaryConnection);
                mKillButton.setEnabled(false);
                mIsBound = false;
                mCallbackText.setText("Unbinding.");
            }
        }
    };

    private OnClickListener mKillListener = new OnClickListener() {
        public void onClick(View v) {
            // To kill the process hosting our service, we need to know its
            // PID.  Conveniently our service has a call that will return
            // to us that information.
            if (mSecondaryService != null) {
                try {
                    int pid = mSecondaryService.getPid();
                    // Note that, though this API allows us to request to
                    // kill any process based on its PID, the kernel will
                    // still impose standard restrictions on which PIDs you
                    // are actually able to kill.  Typically this means only
                    // the process running your application and any additional
                    // processes created by that app as shown here; packages
                    // sharing a common UID will also be able to kill each
                    // other's processes.
                    Process.killProcess(pid);
                    mCallbackText.setText("Killed service process.");
                } catch (RemoteException ex) {
                    // Recover gracefully from the process hosting the
                    // server dying.
                    // Just for purposes of the sample, put up a notification.
                    Toast.makeText(Binding.this,
                            R.string.remote_call_failed,
                            Toast.LENGTH_SHORT).show();
                }
            }
        }
    };

    // ----------------------------------------------------------------------
    // Code showing how to deal with callbacks.
    // ----------------------------------------------------------------------

    /**
     * This implementation is used to receive callbacks from the remote
     * service.
     */
    private IRemoteServiceCallback mCallback = new IRemoteServiceCallback.Stub() {
        /**
         * This is called by the remote service regularly to tell us about
         * new values.  Note that IPC calls are dispatched through a thread
         * pool running in each process, so the code executing here will
         * NOT be running in our main thread like most other things -- so,
         * to update the UI, we need to use a Handler to hop over there.
         */
        public void valueChanged(int value) {
            mHandler.sendMessage(mHandler.obtainMessage(BUMP_MSG, value, 0));
        }
    };

    private static final int BUMP_MSG = 1;

    private Handler mHandler = new Handler() {
        @Override public void handleMessage(Message msg) {
            switch (msg.what) {
                case BUMP_MSG:
                    mCallbackText.setText("Received from service: " + msg.arg1);
                    break;
                default:
                    super.handleMessage(msg);
            }
        }

    };
}

```



출처: https://aroundck.tistory.com/131,
https://android-kr.tistory.com/284 [안드로이드-KR] (훌륭하다)