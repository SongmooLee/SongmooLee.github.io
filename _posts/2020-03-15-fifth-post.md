---
title: "스터디 2주차"
date: 2020-03-15
categories: Study
---

## 안드로이드와 리눅스의 공통점 및 차이점

안드로이드는 자바를 개발 언어로 차용함으로써 개발자에게 진정한 Rapid Application Development 환경을 제공해주었다.

안드로이드는 리눅스 기반으로 만들어졌지만, 실질적으로 많이 변경되었다. 어떤 부분은 리눅스의 주류와 호환되지 않는다. 안드로이드 커널 소스 트리는 2.6.27 버전 커널의 주류에서 분기되었지만, 3.3까지는 호환될 수 있다.

사용자 모드 측면의 소스는 구글에서 AOSP(안드로이드 오픈소스 프로젝트)의 런타임과 프레임워크를 별도의 레파지토리에서 유지한다. 더 높은 관점에서 두 개의 OS가 얼마나 다른지는 정확히 계량하기 힘들지만, 안드로이드와 리눅스는 커널 수준에서 약 95% 정도가 비슷하고, 사용자 모드 수준에서는 약 65% 정도가 비슷하다고 봐도 무방하다.

이러한 추정치는 몇가지 차이점(ARM플랫폼과 드라이버 포함)을 제외한 커널 수준에서 고려했을 때 평가되는 것이고, 커널 소스의 나머지는 변경되지 않았다. 이러한 차이점들(IPC, 메모리 및 로깅 확장 기능)은 모두 "안드로디즘"이라고 하는데, 이들 중 대부분은 리눅스 주류에 반영되었으며, 비슷한 커널 기능으로 대체되거나 drivers/staging/android 디렉터리에 포함되있다.

사용자 모드 수준에서도 많은 변화가 있었다. glibc을 대체한 Bionic, init의 커스텀 버전 및 시스템 스타트업 데몬 이외에도 완전한 새로운 프레임워크인 달빅(Dalvik)런타임과 하드웨어 추상화 레이어가 있다.
OS 내부적으로는 리눅스 위에서 동작하는 네이티브 바이너리, 프로세스 및 스레드와 같이 여전히 변경되지 않는 부분이 많다.

데스크톱 리눅스 배포판에서는 사용되지 않은 채로 남아있지만, 안드로이디는 리눅스에 있는 기능들을 좀 더 영리하게 사용한다. 그룹 제어, 낮은 메모리 조건(안드로이드에서 Low Memory Killer로 확장한 리눅스 OOM), 보안기능과 같은 것들이다.

안드로이드에서도 리눅스에도 많은 인기를 얻지 못한 소수의 오픈소스를 사용하지만, 소수의 오픈소스는 기능 세트의 핵심을 구성한다. 이프로젝트(AOSP의 external/ 폴더 내에 있다.)는 안드로이드 네트워크 기능을 구현하는데 크게 이바지하였다. 그 예로는 racoon(VPN), mdns(서비스 디스커버리 및 와이파이 다이렉트), dsnmasq와 hostapd(테더링과 와이파이 다이렉트) wpa_supplicant(와이파이) 등을 들 수 있다. 다른 오픈 소스 프로젝트는 라이브러리 수준 지원을 제공한다.

![비교를 위한 사진](https://blogfiles.pstatic.net/MjAyMDAzMTVfMjc1/MDAxNTg0Mjc4MjUzMDUx.iQkpeQRNCNIY4wlSE1g-npNXcJHRPumAaKA5gQR5mwwg.MeRIRvR-vUhwIw57WTGCm_GX3SylRCvxEABUA0Rj0egg.PNG.goonta96/%EC%BA%A1%EC%B2%98.PNG)
출처 : android internals::power user's view by Jonathan Levin

_ _ _

## 달빅 가상 머신

안드로이드는 달빅 VM(DVM) 덕분에 256MB 메모리를 가진 모바일 디바이스에서 제대로 동작할 수 있게 되었다. 달빅은 모바일 디바이스에서 시도된 첫 번째 가상 머신은 아니다.
썬마이크로시스템즈에서는 자바2 모바일에디션을 밀었지만, 거의 성공하지 못했다.

달빅 VM은 외관상 자바의 형태를 띠고 있지만, 정확히 말해서 자바 가상머신은 아니다.
달빅은 자바 가상머신과 동떨어져 있지는 않지만, 다른 형태의 바이트코드(DEX, DalvikExecutable)로 실행되고, 오라클에서 디자인된 JVM의 제약 조건에도 불구하고 안드로이드가 널리 퍼질 수 있었다.

달빅은 안드로이드 런타임(ART)으로 대체된다. 달빅을 대체하는 이 프로세스 가상머신은 원래 안드로이드에서 사용되었으며 ART는 애플리케이션의 바이트코드를 네이티브 명령어로 변환을 수행한 다음 나중에 장치의 런타임 환경에 의해 실행된다.

출처 : [안드로이드 런타임 위키백과](https://ko.wikipedia.org/wiki/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C_%EB%9F%B0%ED%83%80%EC%9E%84)

_ _ _

## 안드로이드 런타임

Android Runtime(ART) : 안드로이드에서 사용되던 DVM의 한계점을 극복하기 위해 새로 개발된 런타임이다.


## JVM, Dalvik VM, ART 비교

1. JVM

ByteCode → interpret → 각 플랫폼에 맞는 기계어로 번역 → 프로그램 실행
장점 : WORA(write once read anywhere), OS에 구애 받지 않고 해당 OS에 맞는 기계어로 번역됨
단점 : Native 언어들에 비해 속도가 느리다.

2. Dalvik VM

dex file을 Dalvik machine 위에 올리는 방식
라이선스 문제로 인해 JVM 대신 Java 코드를 이용할 수 있도록 개발됨
Bytecode → interpret → 실행 → 개선 → 네이티브 코드로 변환(JIT, Just in TIME 컴파일 방식)
장점 : 다양한 하드웨어나 아키텍쳐에서 실행 할 수 있는 장점(기존 JVM의 특징)
단점 : 성능과 배터리에 악영향, JIT compile 된 코드가 올라가 메모리가 늘어남

3. ART 방식(Android 4.4 Kitkat 이후)

machine 위에서 OAT file을 돌리는 방식, VM이 아닌 런타임 시 사용되는 라이브러리
앱을 설치할 때 완전히 네이티브 코드로 변환되어 설치됨(AOT 컴파일, Ahead-Of-Time 컴파일)
장점 : 코드 Interpret 및 JIT compile 시간을 제거하여 performance가 향상됨
단점 : 설치시점에 소스 코드를 번역하여 설치가 느리고, 파일을 따로 저장하기 때문에 용량이 커짐

## 컴파일 방식 변화 과정 (DVM -> ART)

초창기(Android 4.4, Kitkat)

호환성이 제대로 보장되지 않았다.
Dalvik에서 작동하던 앱들이 ART에서 죽는 현상이 발생함

Android 5.0, Lolipop 이후

ART가 기본 런타임이 됨

Android 7.0, Nougat 이후

AOT 방식과 JIT 방식을 조합

Android 8.0, Oreo 이후

ART 방식의 개선이 이뤄져 많은 문제점이 해결

## JIT Compiler vs AOT Compiler //컴파일러 비교

JIT Compiler

중간 언어(bytecode)를 읽지 않고, 프로그램이 실행될 때 한꺼번에 읽어서 번역을 진행

장점 : 인터프리터 방식에 비해 성능 개선(약 10배 ~ 20배)
단점 : 배터리 소모가 많아지고, 한번에 읽는 방식이기 때문에 화면 전환 시 속도도 느리다.

AOT Compiler

프로그램 설치 시 컴파일러를 제외한 소스코드를 로드
추가 컴파일 작업 없이 바로 실행

장점 : 사용자의 대기 시간이 거의 없음
단점 : 설치 시 컴파일된 파일을 따로 저장하기 때문에 프로그램 크기가 증가

출처 : [Hudson Park](https://medium.com/@logishudson0218/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-%EC%BB%B4%ED%8C%8C%EC%9D%BC-%EB%B0%A9%EC%8B%9D-dalvikvm-art-b5d64350489f)

_ _ _

init, zygote 추가 예정. 힘들다
