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


//추후추가