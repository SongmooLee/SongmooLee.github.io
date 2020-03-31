---
title: "스터디 4주차"
date: 2020-04-01
categories: Study
---

## 프레임워크 서비스 아키텍처

### 서비스 매니저 다시 살펴보기

init에 의해 핵심 클래스로 분류된 서비스들 중 하나는 servicemanager이다.
다른 핵심 서비스는 servicemanager에 의존하고, servicemanager가 중단되면 모두 다시 시작해야 한다. 만약 servicemanager가 실패하는 경우 init는 servicemanager를 공격적으로 재시작하고 복구를 위해 부팅한다.

servicemanager가 중요한 이유는 그 자체가 가지고 있는 기능 때문이다.
servicemanager는 운영체제상의 다른 시스템을 위해 로케이터 또는 디렉터리로 제공된다. 애플리케이션이나 시스템컴포넌트가 다른 서비스를 사용하는 경우에는 핸들을 얻기 위해 맨 처음 servicemanager를 찾는다. servicemanager가 동작하지 않는다면 IPC도 동작하지 않는다.

servicemanager는 단순한 동작만을 수행하는 매우 작은 바이너리이다.
binder_open을 호출해 /dev/binder 디스크립터를 얻고 위치를 설정하기 위해
binder_become_context_manager를 호출한다.
servicemanager는 그 뒤에 무한 루프인 binder_loop로 들어간다.
이 루프는 트랜젝션(예를 들면 클라이언트에서의 호출)이 발생할 때까지 디스크립터를 블록시킨다. 트랜잭션이 발생하면 프로세스를 깨우고 svcmger_handler 콜백을 호출해 트랜잭션을 처리한다.

Servicemanager는 위와 같이 서비스의 핸들을 부여 및 관리하는 기능을 수행하기 때문에 다른 서비스 서버나 서비스 클라이언트 이전에 미리 실행이 되어 있어야 한다.
이는 다른 서비스 서버나 서비스 클라이언트가 서비스 관련 요청을 수행하기 위해서는 IPC 데이터를 수신 대기하는 상태에 있어야 하기 때문이다.

Binder 문서에서 설명하였듯이 Binder Driver에 접근하는 Process들은 고유의 Handle을 가지고 있는데 android에서 Servicemanager의 Binder Driver에 접근하기 위한 Handle 값은 0으로 정의되어 있다.

Servicemanager는 ioctl(“dev/binder”, BINDER_SET_CONTEXT_MGR)을 통해 binder_node를 생성하고 전역 변수인 binder_context_mgr_node로 등록되므로
서비스 Client들은 서비스 사용을 위한 binder_node를 획득하는 절차 없이 Handle을 0으로 지정하면 Servicemanager 서비스 노드를 사용할 수 있다.

### 서비스 호출 패턴

안드로이드 프레임워크 서비스들은 system_server 스레드 내에 구현되어 있다.
애플리케이션은 자신들을 호출하기 위해 IPC에 응답해야 한다.
안드로이드 전용 IPC(Inter Process Communication) 메커니즘은 바인더를 통해 이루어진다.
애플리케이션은 엔드포인트 디스크립터를 얻고, 원격 서비스에 연결하기 위해 자신의 프로세스 내에서 바인더를 호출해야 한다. 메서드는 IPC 메시지를 통해 호출될 수 있다. 이는 RPC(Remote Procedure Call)라는 메커니즘으로도 알려져 있다.

### 장단점들

안드로이드 시스템 서비스 아키텍처는 iOS등과 같은 OS에서 일반적으로 사용되는 로컬 클라이언트/서버 패턴을 따르고 있다. iOS의 경우 바인더는 없지만 마하 메시지 라고 하는 메시지 전달 아키텍쳐를 자체적으로 구현해 사용하고 있다.

눈에 띄는 단점은 IPC의 오버헤드이다. 특히 메시지를 직렬화 및 역직렬화할 때 외에도 오버헤드가 프로세스 간에 상호 교환할 때, 필요한 컨텍스트를 스위치할 때, 오버헤드가 발생한다.

장점으로는 클라이언트/서버 아키텍쳐는 깔끔한 설계 및 권한의 분리 이외에도 보안이 강하다.

_ _ _

### 바인더

하이레벨 관점에서 보면 바인더를 서비스에 연결되는 특별한 형태의 파일 디스크립터라고 생각해도 된다. 시스템상의 거의 모든 프로세스는 가상으로 /dev/binder의 핸들을 연다.

바인더는 RPC(Remote Procedure Call) 메커니즘이다. 이는 애플리케이션이 프로그램적으로 통신할 수 있게 도와준다. 그러면서도 프로그램에서는 메시지를 주고받을 때 사용하는 방법에 신경쓰지 않아도 된다. 애플리케이션의 관점에서 이를 위해 필요한 것은 메서드를 호출하거나 메서드를 제공하는 것 뿐이다.

_ _ _

## system_server

안드로이드 디바이스들은 다수의 서비스, 다수의 벤더 및 사용자 설치 앱을 가지고 있고, 이조합들은 대략 100개가 넘는다. 다행스럽게도 대부분의 프레임워크 서비스들은 자신만의 전용 프로세스를 요청할 수 없는 대신, 스레드로 실행될 수 있다. 이 스레드들이 실행되기 위해서는 호스트 프로세스가 필요한데, system_server에서 이 기능을 제공한다.

system_server는 윈도우의 svchost.exe와 비슷하게 컨테이너 프로세스가 있는 셸에 불과하다. svchost.exee는 DLL을 서비스로 적재하고, system_server는 자바 클래스 파일을 서비스로 적재한다.


(더 자세한 내용들 추가 예정)
참고 : https://m.blog.naver.com/PostList.nhn?blogId=bl2019

_ _ _

## WindowManager.java

### windowmanager란

WindowManager는 view 객체의 컨테이너인 window 객체를 제어합니다. window 객체는 항상 surface 객체에 의해 지원됩니다. WindowManager는 수명 주기, 입력 및 포커스 이벤트, 화면 방향, 전환, 애니메이션, 위치, 변환, Z-order 및 창의 기타 여러 측면을 관리합니다. WindowManager는 모든 창 메타데이터를 SurfaceFlinger로 전송하므로 SurfaceFlinger는 이 데이터를 사용하여 디스플레이의 표면을 조합할 수 있습니다.

// 코드는 나중에 다시 살펴봐야겠다.

출처 : https://source.android.google.cn/?hl=ko


