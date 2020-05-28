---
title: "스터디 11주차"
date: 2020-05-26
categories: Study
---

### 코루틴이란

코루틴은 코틀린만의 것이 아니다.
이름이 비슷해서 코틀린의 것이라고 생각할 수 있지만 파이썬, C#, Go, Javascript 등 여러 언어에서 지원하고 있는 개념이다. Javascript를 사용하고 있으면서 async await를 사용하고 있다면 이미 코루틴을 사용해본 경험이 있는것이다. 아무튼 코루틴은 새로운 개념, 새로운 기술이 아니라 프로그래밍이 세상에 나온 초창기 부터 존재하던 개념이다.

앱이든 웹이든 비동기 처리가 핵심인 클라이언트 프로그래밍에서 지금까지 가장 핫한 키워드는 rx programming일 것이다.
그러나 구글이 안드로이드 공식 언어를 자바에서 코틀린으로 변경한 이후, 최근 들어 대표적인 샘플 예제들인 bluprint와 sunflower 앱의 비동기처리를 coroutine으로 바꾸었다(아직 rx로 짠 코드를 특정 브랜치로 남겨놓고 있긴하다).
Rx 라이브러리를 걷어내고 코루틴으로 새로 작성한 것이다. 이와 더불어 상당히 많은 외국 자료들이 올라오고있다. <b>그 이유는 코루틴을 사용하면 비동기 처리가 너무나도 쉽게 이루어 질 수 있기 때문이라고 생각한다. 이런 이유 만으로 코루틴을 공부해 볼 가치는 충분하다.</b>

<br>
코루틴을 3가지 키워드 정도로 알아보려고 한다.

- 협력형 멀티 태스킹
- 동시성 프로그래밍 지원
- 비동기 처리를 쉽게 도와줌

가장 중요한 개념은 1번, 협력형 멀티 태스킹이다. 사실 협력형 멀티태스킹에 대한 내용을 이해한다면 코루틴이란 것을 다 알게되는 것이다. 그러나 코루틴을 내 것으로 만들기 위해서는 동시성 프로그래밍과, 비동기 처리에 대한 관점에서 이해하는 것도 중요하다.

#### 협력형 멀티태스킹

Routine은 하나의 태스크, 함수 정도로 생각하면 된다. 즉 협력하는 함수다. 더 진도를 나가기에 앞서 Routine에 대해서 좀 더 알아보자.

Routine에는 우리가 흔히 알고있는 main routine과 sub routine이 존재한다. 이런 단어들이 생소할 수도 있지만, 우리가 늘 작성하고 있는 코드들이다.

<br>
![1](https://user-images.githubusercontent.com/18481078/63651600-6a5a5100-c791-11e9-87d1-3f81dc9b415d.png)

<br>
![2](https://user-images.githubusercontent.com/18481078/63651648-f8ced280-c791-11e9-9917-1b034b855e84.png)

자바코드이다. 메인 루틴이 서브루틴을 호출하면, 서브루틴의 맨 처음 부분에 진입하여 return문을 만나거나 서브루틴의 닫는 괄호를 만나면 해당 서브루틴을 빠져나오게 된다.

<br>
![3](https://user-images.githubusercontent.com/18481078/63651659-303d7f00-c792-11e9-9aae-0b756bb5e8a8.png)

<br>
![4](https://user-images.githubusercontent.com/18481078/63651705-a0e49b80-c792-11e9-9924-eb737b813065.png)

코루틴도 routine이기 때문에 하나의 함수로 생각하자.
그런데 이 함수에 진입할 수 있는 진입점도 여러개고, 함수를 빠져나갈 수 있는 탈출점도 여러개다.

즉, 코루틴 함수는 꼭 return문이나 마지막 닫는 괄호를 만나지 않더라도 언제든지 중간에 나갈 수 있고, 언제든지 다시 나갔던 그 지점으로 들어올 수 있다.

#### 동시성 프로그래밍

동시성 프로그래밍의 개념을 잡고가자면, 병행성 프로그래밍과 완전히 다른 개념이다.

동시성 프로그래밍은 왼쪽 오른쪽 도화지에다 양손으로 그림을 그릴 때, 내가 쥔 펜은 한 순간에 한 가지 도화지에만 닿는다. 그러나 이 행위를 멀리서 본다면 마치 동시에 그림이 그려지고 있는 것 처럼 보일 것이다. 이것이 동시성 프로그래밍이다.

병행성 프로그래밍은 이 것과 다르다. 병행성은 실제로 양쪽손에 펜을 하나씩 들고서 왼쪽과 오른쪽에 실제로 그림을 동시에 그리는 것이다. 같은 시간동안 병행해서 그림을 그리는 것이다.

코루틴은 개념자체로만 보면 병행성이아니라 동시성을 지원하는 개념이다.

![5](https://user-images.githubusercontent.com/18481078/63693907-2d08c880-c850-11e9-8160-9198c9a755d1.png)


// 코틀린 자바 성능 비교 자료 링크(비동기 처리에 대한 것은 없다, 참고자료)
[https://ko.bccrwp.org/compare/kotlin-vs-java-compilation-speed-f357b9/](https://ko.bccrwp.org/compare/kotlin-vs-java-compilation-speed-f357b9/)
[https://aroundck.tistory.com/4881](https://aroundck.tistory.com/4881)

_ _ _

### 코드 분석

WindowManagerShellCommand.java

```
 @Override
    public int onCommand(String cmd) {
        if (cmd == null) {
            return handleDefaultCommands(cmd);
        }
        final PrintWriter pw = getOutPrintWriter();
        try {
            switch (cmd) {
                case "size":
                    return runDisplaySize(pw);
                case "density":
                    return runDisplayDensity(pw);
                case "folded-area":
                    return runDisplayFoldedArea(pw);
                case "overscan":
                    return runDisplayOverscan(pw);
                case "scaling":
                    return runDisplayScaling(pw);
                case "dismiss-keyguard":
                    return runDismissKeyguard(pw);
                case "tracing":
                    // XXX this should probably be changed to use openFileForSystem() to create
                    // the output trace file, so the shell gets the correct semantics for where
                    // trace files can be written.
                    return mInternal.mWindowTracing.onShellCommand(this);
                case "set-user-rotation":
                    return runSetDisplayUserRotation(pw);
                case "set-fix-to-user-rotation":
                    return runSetFixToUserRotation(pw);
                default:
                    return handleDefaultCommands(cmd);
            }
        } catch (RemoteException e) {
            pw.println("Remote exception: " + e);
        }
        return -1;
    }
```


<br>
WindowProcessController.java
```
 public WindowProcessController(ActivityTaskManagerService atm, ApplicationInfo info,
            String name, int uid, int userId, Object owner, WindowProcessListener listener) {
        mInfo = info;
        mName = name;
        mUid = uid;
        mUserId = userId;
        mOwner = owner;
        mListener = listener;
        mAtm = atm;
        mLastReportedConfiguration = new Configuration();
        mDisplayId = INVALID_DISPLAY;
        if (atm != null) {
            onConfigurationChanged(atm.getGlobalConfiguration());
        }
    }
```

<br>
WindowTracing.java
```
int onShellCommand(ShellCommand shell) {
        PrintWriter pw = shell.getOutPrintWriter();
        String cmd = shell.getNextArgRequired();
        switch (cmd) {
            case "start":
                startTrace(pw);
                return 0;
            case "stop":
                stopTrace(pw);
                return 0;
            case "status":
                logAndPrintln(pw, getStatus());
                return 0;
            case "frame":
                setLogFrequency(true /* onFrame */, pw);
                mBuffer.resetBuffer();
                return 0;
            case "transaction":
                setLogFrequency(false /* onFrame */, pw);
                mBuffer.resetBuffer();
                return 0;
            case "level":
                String logLevelStr = shell.getNextArgRequired().toLowerCase();
                switch (logLevelStr) {
                    case "all": {
                        setLogLevel(WindowTraceLogLevel.ALL, pw);
                        break;
                    }
                    case "trim": {
                        setLogLevel(WindowTraceLogLevel.TRIM, pw);
                        break;
                    }
                    case "critical": {
                        setLogLevel(WindowTraceLogLevel.CRITICAL, pw);
                        break;
                    }
                    default: {
                        setLogLevel(WindowTraceLogLevel.TRIM, pw);
                        break;
                    }
                }
                mBuffer.resetBuffer();
                return 0;
            case "size":
                setBufferCapacity(Integer.parseInt(shell.getNextArgRequired()) * 1024, pw);
                mBuffer.resetBuffer();
                return 0;
            default:
                pw.println("Unknown command: " + cmd);
                pw.println("Window manager trace options:");
                pw.println("  start: Start logging");
                pw.println("  stop: Stop logging");
                pw.println("  frame: Log trace once per frame");
                pw.println("  transaction: Log each transaction");
                pw.println("  size: Set the maximum log size (in KB)");
                pw.println("  status: Print trace status");
                pw.println("  level [lvl]: Set the log level between");
                pw.println("    lvl may be one of:");
                pw.println("      critical: Only visible windows with reduced information");
                pw.println("      trim: All windows with reduced");
                pw.println("      all: All window and information");
                return -1;
        }
    }
```


참고자료로 가져왔다 일단





출처 : https://developer.android.com/, https://wooooooak.github.io/kotlin/2019/08/25/

