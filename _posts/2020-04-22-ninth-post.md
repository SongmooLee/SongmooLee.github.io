---
title: "스터디 6주차"
date: 2020-04-22
categories: Study
---

## 안드로이드 서비스

안드로이드는 시스템의 안정성을 위하여 하드웨어 제어 등의 기능을 다음과 같이 두 부분으로 분리하였다.
- Server: Service(하드웨어제어 등)을 제공하는 부분
- Client: 하드웨어에 접근하기 위해 Server에 요청한다. (상호간 양방향 통신도 가능하다.)

서비스 개발 요점
- 핵심 서비스는 일반적으로 별도의 프로세스에서 실행된다. IBinder 인터페이스를 제공 해야 한다. 즉, 바인터 클래스인 IBinder를 사용하여 프로세스간에 통신을 할 수 있다. 또한 원활한 서비스 제공을 위해 쓰레드를 유지할 필요가 있다. 핵심 서비스를 사용하기 전에 Binder Driver에 추가 되어야 한다. Binder Driver를 위하여 Service Manager에서 핵심 서비스를 추가할 수 있다. 또한, Binder Driver에서 Service Manager가 서비스를 받을 수 있다.

서비스의 구현
서비스를 ServiceManager에 등록하고 트랜잭션을 처리해주는 서비스를 만든다.
다음 그림과 같이 ServiceManager에 등록한 서비스가 추가된다. [리스트1] 소스에서는 단지 AddService 추가 작업을 한다. AddService는 Binder Driver에 추가될 핵심 서비스이다. C++을 사용하여 AddService 클래스 정의를 제공하고 있다.

![그림1](https://www.oss.kr/oss/images/news/000000005626-0001.jpg)

![그림2](https://www.oss.kr/oss/images/news/000000005626-0002.jpg)

리스트1
```
#include <sys/types.h>
#include <unistd.h>
#include <grp.h>

#include <binder/IPCThreadState.h>
#include <binder/ProcessState.h>
#include <binder/IServiceManager.h>
#include <utils/Log.h>

#include <private/android_filesystem_config.h>

#include "../core_service/addservice.h"
using namespace android;

int main (int argc, char ** argv)
{
sp <ProcessState> proc(ProcessState::self());
sp <IServiceManager> sm = defaultServiceManager ();
LOGE ( "ServiceManager : %p", sm.get());
AddService::instantiate();
ProcessState::self()->startThreadPool();
IPCThreadState::self()->joinThreadPool();
}
```


클라이언트의 구현

등록되어 있는 서비스에 접근하여 데이터를 주고 받는다. setN() 메소드는 ServiceManager 인터페이스를 얻기 위하여 getAddService ()를 호출한다. ServiceManager 인터페이스를 사용하여 AddService 서비스를 할당해 Service Manager 묶기에 성공하면 ServiceManager는 BpBinder의 IBinder 인터페이스를 제공한다. 인터페이스 참조 binder 변수에 저장된다.


### 바인더 전체 구조

![그림3](https://www.oss.kr/oss/images/news/000000005626-0003.jpg)

바인더의 RPC 구조

● IInterface.h - Binder Interface관련 템플릿이 지정된 header
● BnInterface
● BpInterface
● BnInterface 템플릿
● Native Interface작성시 사용
● 바인더의 서버파트 코드에서 사용됨
● BpInterface 템플릿
● Proxy Interface 작성시 사용
● 바인더의 클라이언트 파트 코드에서 사용됨

사실상 두 템플릿이 사용 될 때는 템플릿으로부터 상속받아서 새로운 클래스를 생성하여 사용하게 되고, 각각 서로 각 프로세스의 다른 편 접속 창구의 역할을 하게 된다.


_ _ _

바인딩된 서비스란 클라이언트-서버 인터페이스 안의 서버를 말합니다. 구성 요소(예: 액티비티)를 서비스에 바인딩하고, 요청을 보내고, 응답을 수신하며 심지어는 프로세스 간 통신(IPC)까지 수행할 수 있게 됩니다. 일반적으로 바인딩된 서비스는 다른 애플리케이션 구성 요소를 도울 때까지만 유지되고 백그라운드에서 무한히 실행되지 않습니다.

바인딩된 서비스란 일종의 Service 클래스 구현으로, 이를 통해 다른 애플리케이션이 이 서비스에 바인딩하여 상호작용할 수 있도록 합니다. 서비스에 대한 바인드를 제공하려면 onBind() 콜백 메서드를 구현해야 합니다. 이 메서드는 클라이언트가 서비스와 상호작용하는 데 사용할 수 있는 프로그래밍 인터페이스를 정의하는 IBinder 객체를 반환합니다.

### 시작된 서비스에 바인드

시작되었으면서도 바인딩된 서비스를 만들 수 있습니다. 다시 말해, startService()를 호출하여 서비스를 시작하고 이를 통해 서비스가 무한히 실행되도록 할 수 있으며, bindService()를 호출하면 클라이언트가 해당 서비스에 바인딩되도록 할 수 있다는 의미입니다.

서비스가 시작되고 바인딩되도록 허용한 다음, 서비스가 실제로 시작되면 시스템은 클라이언트가 모두 바인딩을 해제해도 서비스를 소멸시키지 않습니다. 그 대신 서비스를 직접 확실히 중단해야 합니다. 그러려면 stopSelf() 또는 stopService()를 호출하면 됩니다.

보통은 onBind() 또는 onStartCommand() 중 한 가지만 구현하지만, 둘 모두 구현해야 할 때도 있습니다. 예를 들어 음악 플레이어의 경우 서비스를 무한히 실행하면서 바인딩도 제공하는 것이 유용할 수 있습니다. 이 경우 어떤 액티비티에서 서비스를 시작해 음악을 재생하면, 사용자가 애플리케이션을 닫아도 음악이 계속 재생됩니다. 그런 다음, 사용자가 애플리케이션으로 다시 돌아오면 이 액티비티가 서비스를 바인딩하여 재생에 대한 제어권을 다시 획득할 수 있습니다.

클라이언트는 bindService()를 호출하여 서비스에 바인딩됩니다. 이때 반드시 서비스와의 연결을 모니터링하는 ServiceConnection의 구현을 제공해야 합니다. bindService()의 반환 값은 요청된 서비스가 존재하는지, 클라이언트에 서비스 액세스 권한이 있는지 나타냅니다. Android 시스템이 클라이언트와 서비스 사이에 연결을 설정하면 ServiceConnection에서 onServiceConnected()을 호출합니다. onServiceConnected() 메서드에는 IBinder 인수가 포함되고 클라이언트는 이를 사용하여 바인딩된 서비스와 통신합니다.

여러 클라이언트를 하나의 서비스와 동시에 연결할 수 있습니다. 그러나 시스템이 IBinder 서비스 통신 채널을 캐싱합니다. 다시 말해, 첫 번째 클라이언트가 바인딩될 때만 시스템이 서비스의 onBind() 메서드를 호출해 IBinder를 생성합니다. 그러면 시스템이 동일한 서비스에 바인딩되는 모든 추가 클라이언트에게 동일한 IBinder를 전달합니다. onBind()는 다시 호출하지 않습니다.

마지막 클라이언트가 서비스에서 바인딩을 해제하면 시스템은 서비스를 소멸시킵니다. startService()가 서비스를 시작했을 경우 제외합니다.

바인딩된 서비스를 구현할 때 가장 중요한 부분은 onBind() 콜백 메서드가 반환하는 인터페이스를 정의하는 것입니다. 다음 섹션에서는 서비스의 IBinder 인터페이스를 정의할 수 있는 여러 가지 다양한 방법을 설명합니다.

### 바인딩된 서비스 생성

바인딩을 제공하는 서비스를 생성할 때는 클라이언트가 서비스와 상호작용하는 데 사용할 수 있는 프로그래밍 인터페이스를 제공하는 IBinder를 제공해야 합니다. 인터페이스를 정의하는 방법은 세 가지가 있습니다.

* 바인더 클래스 확장

서비스가 애플리케이션 전용이고 클라이언트와 같은 과정으로 실행되는 경우(이런 경우가 일반적), 인터페이스를 생성할 때 Binder 클래스를 확장하고 그 인스턴스를 onBind()에서 반환하는 방식을 사용해야 합니다. 클라이언트는 Binder를 받고, 이를 사용하여 Binder 구현이나 Service에서 제공되는 공개 메서드에 직접 액세스할 수 있습니다.

서비스가 애플리케이션을 위해 단순히 백그라운드에서 작동하는 요소에 그치는 경우 선호되는 방법입니다. 인터페이스를 생성할 때 이 방식을 사용하지 않을 이유가 있다면, 서비스를 다른 애플리케이션이나 별도의 프로세스에서 사용하는 경우뿐입니다.

* 메신저 사용

인터페이스가 여러 프로세스에서 작동해야 하는 경우, Messenger로 서비스에 대한 인터페이스를 생성할 수 있습니다. 이 방식을 사용하면 서비스가 여러 가지 유형의 Message 객체에 응답하는 Handler를 정의합니다. 이 Handler가 Messenger의 기초가 되어 클라이언트와 IBinder를 공유하고, 클라이언트는 Message 객체를 사용해 서비스에 명령을 보낼 수 있게 됩니다. 그 외에도 클라이언트가 자체적으로 Messenger를 정의하여 서비스가 메시지를 돌려보내도록 할 수 있습니다.

이것이 프로세스 간 통신(IPC)을 수행하는 가장 간단한 방법입니다. Messenger가 모든 요청을 단일 스레드로 큐에 저장하므로 서비스를 스레드로부터 안전하게 설계할 필요가 없기 때문입니다.

*  AIDL 사용

AIDL(Android Interface Definition Language)은 객체를 운영체제가 이해할 수 있는 원시 유형으로 해체한 다음, 여러 프로세스에서 마샬링하여 IPC를 수행합니다. Messenger를 사용하는 이전 방법은 실제로 AIDL을 기본 구조로 하고 있습니다. 위에서 언급한 바와 같이 Messenger는 단일 스레드에 모든 클라이언트 요청 큐를 생성하므로 서비스는 한 번에 하나씩 요청을 수신합니다. 그러나 서비스가 동시에 여러 요청을 처리하게 하려면 AIDL을 직접 사용해도 됩니다. 이 경우에 서비스는 스레드로부터 안전해야 하고 다중 스레딩 처리가 가능해야 합니다.

AIDL을 직접 사용하려면 프로그래밍 인터페이스를 정의하는 .aidl 파일을 생성해야 합니다. Android SDK 도구는 이 파일을 사용하여 인터페이스를 구현하고 IPC를 처리하는 추상 클래스를 생성합니다. 그 후에는 개발자가 서비스 내에서 이 추상 클래스를 확장하면 됩니다.

### 바인더 클래스 확장

서비스가 로컬 애플리케이션에서만 사용되고 여러 프로세스에서 작동할 필요가 없는 경우, 자체적인 Binder 클래스를 구현하여 클라이언트가 서비스 내의 공개 메서드에 직접 액세스하도록 할 수도 있습니다.

이를 설정하는 방법은 다음과 같습니다.

1. 서비스에서 다음 중 한 가지 기능을 하는 Binder의 인스턴스를 생성합니다.
- 클라이언트가 호출할 수 있는 공개 메서드가 있습니다.
- 클라이언트가 호출할 수 있는 공개 메서드가 있는 현재 Service 인스턴스를 반환합니다.
- 클라이언트가 호출할 수 있는 공개 메서드가 포함된 서비스가 호스팅하는 다른 클래스의 인스턴스를 반환합니다.
2. Binder의 인스턴스를 onBind() 콜백 메서드에서 반환합니다.
3. 클라이언트의 경우, Binder를 onServiceConnected() 콜백 메서드에서 받아서 제공된 메서드로 바인딩된 서비스를 호출합니다.

예컨대 어떤 서비스가 Binder 구현을 통해 클라이언트에게 서비스 내의 메서드에 액세스할 수 있는 권한을 제공할 수 있습니다.

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

LocalBinder는 LocalService의 현재 인스턴스를 검색하기 위한 getService() 메서드를 클라이언트에 제공합니다. 이렇게 하면 클라이언트가 서비스 내의 공개 메서드를 호출할 수 있습니다. 예를 들어 클라이언트는 서비스에서 getRandomNumber()를 호출할 수 있습니다.

출처 : OSS, developer.android.com