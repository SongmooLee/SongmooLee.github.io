---
title: "스터디 3주차"
date: 2020-03-25
categories: Study
---

## 코드 분석
![1](https://blogfiles.pstatic.net/MjAyMDAzMjVfMjE5/MDAxNTg1MDcwNDIwNjA4.OVYC0qycDHjrSfKXyNpdBD5n2EiRGhOoN9V7Ch2mPH0g.EmmNmjQQbVAGdRcCN3Tj08do7RzUkxsoX6m_lGKRpH0g.PNG.goonta96/1111111111.PNG)

Android 오픈소스 프로젝트에서 코드를 분석해보는 과정을 가질것이다.
일단 이해하기 쉬울거 같은 camera를 살펴보자.

경로는 android/platform/packages/apps/Camera/ refs/heads/marshmallow-cts-release /./src/com/android/camera 인것을 확인 가능하다.

위의 사진처럼 카메라 디렉터리 내에서도 엄청나게 많은 java 소스파일들이 많은 것을 확인했다.

1주차 공부에서 확인했듯이 packages 는 안드로이드 기본 애플리케이션, 컨텐트 프로바이더가 있다고 배웠다.

그래서 내부 경로에 apps 디렉터리 확인이 가능하고, refs/heads/marshmallow-cts-release 디렉터리는 jni/ , res/ , src/ , tests/ 디렉터리들이 있는데 확인이 어려워서 그 이전 디렉터리인 Camera 디렉터리를 확인해보니 많은 안드로이드 버전들의 카메라 버전들이 있었다. 이 디렉터리에서 안드로이드 버전에 해당하는것을 쓰는것 같았다.

cts가 안드로이드 호환성 테스트 관련 소스라는 것을 1주차에서 공부한바 있지만, 더욱 상세히 알아보니

```
CTS(호환성 테스트 도구 모음)는 상용 등급의 무료 테스트 도구 모음으로 다운로드할 수 있습니다. CTS는 호환성의 '메커니즘'을 나타냅니다.
CTS는 데스크톱 컴퓨터에서 실행되며 연결된 기기나 에뮬레이터에서 직접 테스트 사례를 실행합니다. CTS는 연속 빌드 시스템을 통해 기기를 빌드하는 엔지니어의 일상 워크플로에 통합할 수 있도록 설계된 단위 테스트 세트입니다. CTS의 목적은 비호환성을 조기에 발견하고 개발 과정 내내 소프트웨어의 호환성을 유지하는 것입니다.
CTS는 주요 소프트웨어 구성요소 두 개를 사용하는 자동화된 테스트 도구 모음입니다.

출처 : https://source.android.com/
```

이 글을 보아하니 cts가 release된것을 의미하니, android marshmallow로 release된 도구모음으로 접근하는 것을 알 수 있었다. //이게 맞는지 틀린지도 모른다.

이 후의 경로로 camera에 접근하는 것을 알 수 있었다.

camera 디렉터리 안에 있는 소스파일들과 디렉터리들이다.


```
ActivityBase.java
CameraActivity.java
CameraBackupAgent.java
CameraButtonIntentReceiver.java
CameraDisabledException.java
CameraErrorCallback.java
CameraHardwareException.java
CameraHolder.java
CameraManager.java
CameraModule.java
CameraPreference.java
CameraScreenNail.java
CameraSettings.java
CaptureAnimManager.java
ComboPreferences.java
CountDownTimerPreference.java
DisableCameraReceiver.java
EffectsRecorder.java
Exif.java
FocusOverlayManager.java
IconListPreference.java
IntArray.java
ListPreference.java
LocationManager.java
MediaSaver.java
Mosaic.java
MosaicFrameProcessor.java
MosaicPreviewRenderer.java
MosaicRenderer.java
OnClickAttr.java
OnScreenHint.java
PanoProgressBar.java
PanoUtil.java
PanoramaModule.java
PhotoController.java
PhotoModule.java
PieController.java
PreferenceGroup.java
PreferenceInflater.java
PreviewFrameLayout.java
PreviewGestures.java
ProxyLauncher.java
RecordLocationPreference.java
RotateDialogController.java
SecureCameraActivity.java
ShutterButton.java
SoundClips.java
StaticBitmapScreenNail.java
Storage.java
SwitchAnimManager.java
Thumbnail.java
Util.java
VideoController.java
VideoModule.java
drawable/
ui/
```


먼저 카메라를 작동시킬 것 같은 2번째 소스파일 CameraActivity.java 소스를 보자.

_ _ _

### CameraActivity.java

[코드확인](https://android.googlesource.com/platform/packages/apps/Camera/+/refs/heads/marshmallow-cts-release/src/com/android/camera/CameraActivity.java)

일단 코드가 437 줄이어서 담기엔 너무 많아 주석을 생략하며 코드를 나눠서 보겠다.
링크는 실제 소스파일이다.

```
public class CameraActivity extends ActivityBase
        implements CameraSwitcher.CameraSwitchListener {
    public static final int PHOTO_MODULE_INDEX = 0;
    public static final int VIDEO_MODULE_INDEX = 1;
    public static final int PANORAMA_MODULE_INDEX = 2;
    public static final int LIGHTCYCLE_MODULE_INDEX = 3;

```

ActivityBase와 CameraSwitcher를 상속받는 것을 확인 가능하다.

예로 상속받는 클래스 2개를 각각 살펴보자.

_ _ _

### ActivityBase.java


```
/**
 * Superclass of camera activity.
 */
public abstract class ActivityBase extends AbstractGalleryActivity
        implements LayoutChangeNotifier.Listener {
    private static final String TAG = "ActivityBase";
    private static final int CAMERA_APP_VIEW_TOGGLE_TIME = 100;  // milliseconds
    private static final String INTENT_ACTION_STILL_IMAGE_CAMERA_SECURE =
            "android.media.action.STILL_IMAGE_CAMERA_SECURE";
    public static final String ACTION_IMAGE_CAPTURE_SECURE =
            "android.media.action.IMAGE_CAPTURE_SECURE";
             // The intent extra for camera from secure lock screen. True if the gallery
    // should only show newly captured pictures. sSecureAlbumId does not
    // increment. This is used when switching between camera, camcorder, and
    // panorama. If the extra is not set, it is in the normal camera mode.
```

주석으로 카메라 활동의 슈퍼클래스라는 것을 확인했다.
이 추상 클래스 또한 상속을 받는다.
이외의 메소드 등은 대부분 카메라 동작 함수다.


_ _ _

### CameraSwitcher.java

경로를 보니 com.android.camera.ui.CameraSwitcher 이다.

```
import android.animation.Animator;
import android.animation.Animator.AnimatorListener;
import android.animation.AnimatorListenerAdapter;
import android.content.Context;
import android.content.res.Configuration;
import android.graphics.Canvas;
import android.graphics.drawable.Drawable;
import android.util.AttributeSet;
import android.view.LayoutInflater;
import android.view.MotionEvent;
import android.view.View;
import android.view.View.OnClickListener;
import android.view.View.OnTouchListener;
import android.view.ViewGroup;
import android.widget.LinearLayout;
```

임포트 된것과 메소드 등을 보면 모션과 그래픽 등을 표현하는 클래스임을 유추할 수 있다. 이 외에도 비슷하게 흘러간다.

_ _ _


일단 package 경로에서 앱들을 살펴봤는데 이제 다른 디렉터리들도 살펴보면서 1주차에 공부한 소스 구조를 심화적으로 파악할 예정이다.