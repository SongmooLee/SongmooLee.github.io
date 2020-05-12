---
title: "스터디 9주차"
date: 2020-05-13
categories: Study
---

## WindowManager

### 개념

<b>Surface Flinger 서비스</b>

Surface Flinger는 다양한 애플리케이션에서 사용중인 Surface를 조합해 프레임 버퍼 장치로 렌더링 해주는 서비스다.

<b>Window Manager Service</b> - Surface Flinger 위에 위치하며, 기기 화면에 그릴 내용을 Surface Flinger로 전달.

<br>
![1](https://t1.daumcdn.net/cfile/tistory/213E0850568F5CA234)

시스템 서비스는 안드로이드가 부팅할 때 미디어 서버와 시스템 서버라는 두 시스템 프로세스에 의해 실행된다. 미디어 서버 프로세스는 Surface Flinger를 제외한 Audio Flinger, Media Player Service와 같은 네이티브 서비스를 실행하는 역할을 한다.

<b>시스템 서버</b>는 Zygote에 의해 맨 처음 생성되는 자바 기반의 프로세스로 네이티브 시스템 서비스인 Surface Flinger를 비록한 모든 자바 시스템 서비스를 실행한다.

![2](https://t1.daumcdn.net/cfile/tistory/24052148568F7BD031)

<br>![3](https://t1.daumcdn.net/cfile/tistory/210DE334568F7A6526)

<br>
시스템 서버는 자바 프로세스이므로 Zygote로 부터 생성된다. 시스템 서버가 어떻게 시스템 서비스를 생성하는지 알아보자.

<big><b>Zygote 프로세스로 부터 생성</b></big>

시스템 서버는 Zygote 프로세스 생성 시에 실행되기 때문에 시스템 서버가 어떻게 실행되는지 파악하려면 init.rc에서 Zygote가 실행되는 부분을 먼저 살펴 보아야 한다.

```
service zygote /system/bin/app_process -Xzygote /system/bin --zygote --start-system-server
	socket zygote stream 666
	onrestart write /sys/android_power/request_state wake
    onrestart write /sys/power/state on
```

보다시피 Zygote 실행 시에 -start-system-server라는 옵션이 정의된것을 확인 가능. 이 옵션은 Zygote에서 시스템 서버를 생성하라고 요청하는 역할을 한다.

<br><big><b>android_servers 라이브러리 로드</b></big>

다음은 SystemServer의 main() 메서드를 나타내는 코드다.

1) main() 메서드의 기능은 android_servers 라이브러리(libandroid_servers.so)를 로드하고 init1() 메서드를 호출하는 것이다.

init1() 메서드는 JNI통해 다음 system_init() 네이티브 함수를 호출한다.

```
// 시스템 서버의 mian() 메서드 주요 부분 //
public class SystemServer
{
	native public static void init(String[] args);

    public static void main(String[] args){
    	System.loadLibrary("android_servers");
        init1(args);  <--- 1
    }
}
```

```
// system_init.cpp - system_init() 함수의 주요 부분 //

// SurfaceFlinger 시스템 서비스 초기화

extern "C" status_t system_init()
{
	SurfaceFlinger::instantiate();  <-- 2

	AndroidRuntime* runtime = AndroidRuntime::getRuntime();
    runtime->callStatic("com/android/server/SystemServer", "init2");  <-- 3

    return NO_ERROR:
}
```

System_init() 함수의 주된 기능은 Surface Flinger 네이티브 시스템 서비스를 초기화하는 것이다.

2) Surface Flinger는 C++ 기반의 시스템 서비스이므로 자바 프로세스인 시스템 서버 프로세스가 직접 호출할 수 없다. 따라서 JNI를 이용해 system_init() 네이티브 함수를 호출해서 Surface Flinger 서비스를 실행한다. Surface Flinger 역시 네이티브 시스템 서비스이다.

3) Surface Flinger 서비스를 실행하고 나면
runtime->callStatic("com/android/server/SystemServer", "init2"); 를 호출해서 SysteServer 클래스의 init2() 메서드를 호출한다. 여기서 callStatic() 함수는 C++코드에서 JNI를 이용해 자바 클래스의 정적 메서드를 호출할 수 있게 만든 JNI 래핑 함수이다.

<br><big><b>자바 시스템 서비스 초기화 및 등록</b></big>

System Server에서 Surface Flinger를 초기화하고 나면 init2()메서드가 호출된다. 이 메서드는 Entropy 서비스부터 AppWidget 서비스까지 안드로이드의 모든 자바 시스템 서비스를 생성하고 초기화하는 역할을 한다.

4) init2() 메서드에서는 ServerThread를 생성한 다음 실행한다. ServerThread는 안드로이드의 모든 자바 시스템 서비스를 실행하는 자바 스레드이다.

5) 이렇게 실행된 자바 시스템 서비스 역시 네이티브 시스템 서비스처럼 다른 모듈이 서비스를 활용할 수 있게끔 해당 서비스를 컨텍스트 매니저에 등록해야 한다.
C++ 기반의 네이티브 시스템 서비스 코드와는 다르게 자바 시스템 서비스는 Service Manager 클래스의 addServce() 정적 메서드를 이용해서 컨텍스트 매니저에 자신을 등록한다.

Service Manager 클래스는 자바 레이어에서 컨텍스트 매니저와 통신하는 서비스 매니저라고 보면 된다.

```
// frameworks/base/services/java/com/android/server/SystemServer.java
public static final void init2() {
	Thread thr = new ServerThread();
    thr.start();  <-- 4
}

class ServerThread extends Thread {
...

public void run() {  <-- 5
	...
    Log.i(TAG, "Entropy Service");
    ServiceManager.addService("entropy", new EntropyService());

    Log.i(TAG, "Power Manager");
    power = new PowerManagerService();
    ServiceManager.addService(Context.POWER_SERVICE, power);
    ...
}
}

```

_ _ _

### 코드 분석

### WindowManager.java

변수들 말고 메소드 위주로 분석을 하려고 한다.

```
// *WindowManager.java*
// WindowManager 는 어플과 WindowManager Service를 이어주는 인터페이스

...
 public void writeToParcel(Parcel out, int parcelableFlags) {
            out.writeInt(width); //parcel 객체에 프리미티브 데이터를 쓴다
            out.writeInt(height);
            out.writeInt(x);
            ....
            }

//Parcel 클래스란 IPC 전용 데이터로 사용하기 위해 만들어진 클래스. 따라서 프로세스간 데이터 전달에 최적화 되어 속도가 매우 빠르다. Parcel이 소포라는 뜻이고, 여러데이터가 하나의 꾸러미(Class) 안에 담겨 있다는 것을 의미를 가짐.
즉, 한꺼번에 프리미티브 데이터를 전달한다는 의의.

//대부분 Parcel 클래스는 단독으로 사용되지 않고, Parcelable 클래스를 함께 쓴다. Parcelable 클래스 내부에 Parcel 데이터를 읽고 쓰는 함수들을 구현한다.

//writeToParcel은 객체가 직렬화되어 보내지기 이전에 데이터를 직렬화시켜주는 메소드로, out 에 순차적으로 Class 내부에 있는 데이터들을 저장시켜놓는다.

// * 직렬화 : Android 시스템에서 동작하는 방식으로 이야기 하자면, A Activity에서 B Activity로 데이터를 전달할 때, 데이터를 묶어서 전달한다. 즉, 하나의 Class화 된 여러 데이터들이 순서대로 Byte형식으로 변환되어 A -> B로 전달된다.


...
 public static final @android.annotation.NonNull Parcelable.Creator<LayoutParams> CREATOR
                    = new Parcelable.Creator<LayoutParams>() {
            public LayoutParams createFromParcel(Parcel in) {
                return new LayoutParams(in);
            }

            public LayoutParams[] newArray(int size) {
                return new LayoutParams[size];
            }
        };


        public LayoutParams(Parcel in) {
            width = in.readInt();
            height = in.readInt();
            x = in.readInt();
            y = in.readInt();
            type = in.readInt();
            ...

// 직렬화를 풀어주는 로직

// Parcel을 이용하여 보내게 되면 받는 쪽에서도 Parcel을 통해서 데이터를 받기 때문에, 생성자를 통해 Parcel을 전달하게 된다. 그래서 Parcel형이 들어오는 생성자를 만들게 되었고, Default 생성자도 함께 추가된다.

// Creator가 없다면 데이터를 넘기게 되더라고 Creator가 없다는 Exception을 뿌려주기 때문에 필히 작성을 해줘야 하는 부분이라고 한다..

...
 public final int copyFrom(LayoutParams o) {
            int changes = 0;

            if (width != o.width) {
                width = o.width;
                changes |= LAYOUT_CHANGED;
            }
...


// 바뀌면 재설정해주는 메소드.

```

<br>
```
// *참고 Parcel.java
...
 /*
     * Write an integer value into the parcel at the current dataPosition(),
     * growing dataCapacity() if needed.
     */
    public final void writeInt(int val) {
        nativeWriteInt(mNativePtr, val);
    }
    ...
    // 네이티브 코드.
     @UnsupportedAppUsage
    @SuppressWarnings({"UnusedDeclaration"})
    private long mNativePtr; // used by native code

// android_view_MotionEvent.cpp 에서 mNativePtr 확인 가능하다.

```

_ _ _

### WindowManagerImpl.java

WindowManagerImpl.java 역할 :

ViewManager 구현 : 화면의 Top-Level Window에 View Class를 추가 할 수 있도록 해줌
WindowManager 구현 : Device의 화면을 제어 할 수 있도록 해줌
addView(), updateViewLayout(), removeView()

<br>
```
// *WindowManagerImpl.java*

public final class WindowManagerImpl implements WindowManager {

    @UnsupportedAppUsage
    private final WindowManagerGlobal mGlobal = WindowManagerGlobal.getInstance(); // Global 할때 확인

    private final Context mContext;
    // 안드로이드 플랫폼상에서의 관점으로 살펴보면, Context 는 다음과 같은 두가지 역할을 수행하기 때문에 꼭 필요한 존재.
	// 1. 자신이 어떤 어플리케이션을 나타내고 있는지 알려주는 ID 역할
	// 2. ActivityManagerService 에 접근할 수 있도록 하는 통로 역할

    private final Window mParentWindow; // Window 객체 관련 변수..

    private IBinder mDefaultToken;

    public WindowManagerImpl(Context context) {
        this(context, null);
    }

    private WindowManagerImpl(Context context, Window parentWindow) {
        mContext = context;
        mParentWindow = parentWindow;
    }

    public WindowManagerImpl createLocalWindowManager(Window parentWindow) {
        return new WindowManagerImpl(mContext, parentWindow);
    }

    public WindowManagerImpl createPresentationWindowManager(Context displayContext) {
        return new WindowManagerImpl(displayContext, mParentWindow);
    }

```

// 진행중


