---
title: "스터디 8주차"
date: 2020-04-29
categories: Study
---

## 자바 서비스 프레임워크

### 서비스

<br>
![1](https://t1.daumcdn.net/cfile/tistory/244C033F56A715AD09)

<br>
서비스 프레임워크

: 안드로이드 플랫폼에서 동작하는 서비스를 개발하기 위한 클래스의 집합
: 서비스의 설계와 구현을 재사용 가능한 형태로 제공

<br>
안드로이드는 시스템의 안정성을 위하여 하드웨어 제어 등의 기능을 다음과 같이 두 부분으로 분리하였다.

● Server: Service(하드웨어제어 등)을 제공하는 부분
● Client: 하드웨어에 접근하기 위해 Server에 요청한다. (상호간 양방향 통신도 가능하다.)

<br>
<big><b>서비스 개발 요점</b></big>

핵심 서비스는 일반적으로 별도의 프로세스에서 실행된다.
IBinder 인터페이스를 제공 해야 한다. 즉, 바인터 클래스인 IBinder를 사용하여 프로세스간에 통신을 할 수 있다.
또한 원활한 서비스 제공을 위해 쓰레드를 유지할 필요가 있다.
핵심 서비스를 사용하기 전에 Binder Driver에 추가 되어야 한다.
Binder Driver를 위하여 Service Manager에서 핵심 서비스를 추가할 수 있다.
또한, Binder Driver에서 Service Manager가 서비스를 받을 수 있다.

### 자바 서비스 프레임워크

안드로이드 서비스 프레임워크가 자바 레이어와 C플플 레이어별로 존재하며 JNI를 통해 서로 연결돼 있음, JNI를 통해 네티이브 서비스 프레임워크를 재사용함으로써 자바 레이어의 서비스 사용자가 자바로 작성된 서비스뿐만 아니라 C플플 로 작성된 서비스도 이용할 수 있게 된다.

<br>![2](https://t1.daumcdn.net/cfile/tistory/2218CC4D56A731D517)

<br><br>
자바 서비스 프레임워크는 네이티브 서비스 프레임워크와 다음과 같은 차이점이 있다.

1) 서비스 생성: 자바 서비스 프레임워크에서 자바 서비스를 개발하는 방법은 두 가지다.

- 첫 번째 방법은 Binder 클래스를 상속받아 개발하는 것으로, 서비스를 정밀하게 제어해야 할 때 적절한 방식으로 자바 시스템 서비스를 작성할 때도 쓰이는 방법이다. 다행인 점은 안드로이드 개발자 도구에서 서비스 인터페이스와 서비스 생성코드를 자동으로 생성해 주는 AIDL 언어와 컴퍼일러를 제공하고 있어 네이티브 시스템 서비스를 제작하는 것보다 쉽게 자바 시스템 서비스를 작성할 수 있다.

- 두 번째 방법은 Service 클래스를 상속받아 개발하는 것인데 일반적으로 특정 작업을 주기적으로 백그라운드에서 수행하는 프로세스를 구현하는 데 사용된다.

2) 바인더 IPC 처리: 자바 서비스 프레임워크에서는 바인더 IPC를 지원하기 위해 JNI를 통해 연결된 네이티브 서비스 프레임워크의 구성요소를 재사용한다.

<br> <b>■ 자바 서비스 관리</b>

1. 자바 시스템 서비스는 네이티브 시스템 서비스와 마찬가지로 컨텍스트 매니저에 서비스를 등록한 후 서비스 매니저를 통해 서비스를 사용할 수 있다.

2. 액티비티 매니저 서비스에서 관리.

<br><big><b>자바 서비스 프레임워크의 계층별 요소</b></big>

<br>![3](https://t1.daumcdn.net/cfile/tistory/247DF64A56A733A434)

각 레이어 별로 네이티브 서비스 프레임워크와 차이점은 다음과 같다

첫째 : 서비스 사용자의 서비스 레이어에 매니저 클래스가 위치한다.

둘째 : RPC 레이어에 AIDL 도구로 자동 생성된 스텁(Stub)과 프록시(Proxy) 클래스가 위치한다.

셋째 : IPC 레이어에 위치한 구성요소가 JNI를 통해 네이티브 서비스 프레임워크의 구성요소와 연결돼 있다.

_ _ _

### Intent란 무엇인가?

Intent는 안드로이드 앱 내부 혹은 외부 컴포넌트간의 호출및 정보 전달을 하는 역할을 합니다.

앱 구성요소(컴포넌트)
* Activity
* Service
* BroadCast Receiver
* Content Provider

앱의 구성요소는 위의 4가지 이며 해당 구성요소간의 호출및 정보를 전달하는데 사용하는 것이 Intent 입니다. 명시적 인텐트와 암시적 인텐트 2가지로 분류 할수 있습니다.

1) 명시적 인텐트
- 호출할 대상을 지정 하여 사용

```
Intent intent = new Intent(context, CallActivity.class);
startActivity(intent);
```
다른 Acitivity를 시작 하는 소스입니다.
시작할 Acitivity를 지정하여 Intent를 생성 하여 사용합니다.

<br>
```
Intent intent = new Intent(context, MyService.class);
startService(intent);
```
로컬서비스를 시작하는 소스입니다. (참고로 원격서비스는 바인딩하는 방식으로 구현됩니다.)
Acitivity 시작하는 것과 크게 다르지 않게 Service를 지정하여 Intent를 생성하여 사용 하는 것을 알수있습니다.

<br>2) 암시적 인텐트
 - 컴포넌트외의 속성(컴포넌트를 지정하면 명시적 인텐트가 된다)들로 구성하여 속성에 부합하는 컴포넌트를 실행

```
Intent intent = new Intent(
              "android.intent.action.CALL"
              , Uri.parse(tel));
startActivity(intent);
```
전화걸기를 요청 하는 소스입니다.
Action값을 android.intent.action.CALL을 지정해서 Acitivity를 실행하면 디바이스내에 해당 Action값 속성을 가지고 있는 Activity를 선택할수 있는 창이 노출 됩니다.


_ _ _

### 액티비티 매니저

<br>자바 시스템 서비스의 일종인 코어 플랫폼 서비스로서 안드로이드 애플리케이션 컴포넌트인 액티비티, 서비스, 브로드캐스트 리서버 등을 생성하고, 이들의 생명주기를 관리하는 역할

![4](https://t1.daumcdn.net/cfile/tistory/2272CD4556A73D2E05)

<br>![5](https://t1.daumcdn.net/cfile/tistory/267BC33F56A73A9A26)


출처: https://dev-ahn.tistory.com/92?category=613307
http://devstory.ibksplatform.com/2017/10/intent.html