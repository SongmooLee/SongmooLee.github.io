---
title: "스터디 12주차"
date: 2020-06-01
categories: Study
---

### SurfaceFlinger 간략히

사용자의 프로세스 혹은 응용 프로그램에서 생성한 화면(원래는 하나의 Surface)을 합성하여 LCD 화면에 그림을 출력할 수 있도록 관장하는 역할을 한다.

![1](https://www.oss.kr/oss/images/news/000000005680-0000.jpg)

따라서 SurfaceFlinger의 역할은 크게 2가지로 볼 수 있다.

(1) 사용자 프로세스나 앱에서 생성한 화면을 관리 하는 역할·화면의 위치, 표시 순서(Display Order), 색상 등을 관리하는 역할

(2) 커널에 위치한 프레임 버퍼 드라이버와 연동하여 생성된 최종 이미지를 프레임 버퍼 드라이버를 통해 LCD에 출력할 수 있도록 프레임 버퍼와 연동하는 역할

이와 같이 SurfaceFlinger의 역할을 분류해 볼 수 있다.
하지만 게임과 같은 응용 프로그램이나 카메라를 통한 프리뷰 동작을 위해서는 고속의 화면 처리가 필요한 응용 프로그램에서는 SurfaceFlinger를 사용하여 처리하기에는 속도 면에서 부적절 하기 때문에 하드웨어 오버레이(Overlay) 사용하여 출력하는 방법이 사용된다.

#### 프레임버퍼 드라이버

![2](https://www.oss.kr/oss/images/news/000000005680-0001.jpg)

프레임 버퍼 드라이버의 동작 모습을 사용자의 응용 프로그램 관점에서 보여준다. 사용자의 응용 프로그램에서 작성한 화면은 프레임 버퍼 드라이버를 거쳐, 프로세서 내부에 있는 LCD 컨트롤러를 통해 LCD에 표시되게 된다. 물론 안드로이드 시스템에서는 프로그램을 작성하여 운영할 수 있지만, 앱이나 사용자의 프로세스에서 만들어진 모든 동작들은 SurfaceFlinger를 통해 운영이 된다. 즉, 사용자의 응용 프로그램과 프레임 버퍼 드라이버상에 위치하여 관리하게 된다. 따라서 프레임 버퍼에 대한 윈리의 이해, SurfaceFlinger에 대한 정확한 동작의 이해가 필요한 이유다.

#### 안드로이드 시스템에서 프레임버퍼 드라이버의 특이사항

● 기존 리눅스 프레임 버퍼 드라이버에 struct fp_ops와 fb_ pan_display 함수가 추가로 구현되어야 함

● 실제 프레임 버퍼 크기보다 두 배의 메모리 할당 필요 해당 함수는 프레임 버퍼의 디스플레이 포인터를 옮기는 기능을 제공한다. 즉, 실제 화면보다 넓은 가상 디스플레이를 구현 가능하도록 함

● 안드로이드 시스템의 프레임 버퍼 드라이버는 고속 처리를 위해 반드시 더블 버퍼링(Double Buffering)을 사용하도록 구성됨

#### SurfaceFlinger

SurfaceFlinger의 역할은 앞에서 설명 했듯이 앱이나 사용자의 응용 프로그램에서 생성한 화면(여기에서는 Surface라는 용어를 사용함) 합성 관리하는 역할이다. "Surface+Flinger"의 합성이다. 즉, 생성된 여러 개의 Surface를 하나의 Surface로 만들고 이 만든 화면을 프레임 버퍼 드라이버와 연계하여 프레임 버퍼로 만들고, LCD 화면에 표시하는 역할을 하는 것이다.

![3](https://www.oss.kr/oss/images/news/000000005680-0002.jpg)

#### SurfaceFlinger 전체적 구성

![3](https://www.oss.kr/oss/images/news/000000005680-0003.jpg)

통상적인 앱은 android.view.Surface 클래스를 통해 화면 제어를 하게 된다. 당연히 앱과 내부간의 통신은 JNI 인터페이스를 통해 UI와 통신하고, SurfaceFlinger와는 바인더 통신을 통해 접근하게 된다. 안드로이드 시스템에서 생성되는

- 일반적인 그래픽들의 처리는 gralloc와 프레임 버퍼 드라이버를 통하여 그려지게 되고,
- 카메라나 멀티미디어와 같은 하드웨어 가속을 쓰는 경우에는 오버레이(overlay)를 통해 관리되게 된다.

#### SurfaceFlinger의 Layer와 LayerBuffer

SurfaceFlinger 는 createSurface() 가 호출되면 Surface 클래스를 바로 생성 하는 것이 아니고 Layer 클래스를 생성한다. createSurface() 함수에 넘어온 인자에 따라 어떤 Layer 클래스를 생성할지를 선택한다. Layer 클래스는 각각 다른 형태로 출력을 하며 Surface 클래스를 포함하고 있다. SurfaceFlinger 클래스는 createSurface() 함수 요청이 있을 때마다 LayerXXX 형태의 클래스를 만들지만 리턴은 LayerXXX 클래스 내부의 getSurface() 함수를 통해서 Surface 형을 돌려주게 된다.

### View 간략히

뷰는 안드로이드 기본 화면을 구성하는 모든 기본 화면의 구성요소이다.
우리가 어플리케이션에서 보는 버튼, 테이블, id/pw 입력 칸 등등 모든 것이 뷰가 된다.

뷰는 뷰를 포함 할 수 있고, 중첩적으로 사용 할 수 있다.

이때 뷰 중에서 눈에 보이는 것들은 <b>위젯</b>이라 부르고, 눈에 보이지 않는 것들은 <b>레이아웃</b>이라고 부른다.

레이아웃은 그 안에 다른 뷰들을 담아둘 수 있는데 레이아웃도 뷰를 상속하여 정의되었기 때문에 레이아웃 안에 레이아웃도 담을 수 있습니다.

레이아웃 안에 레이아웃, 다시 그 레이아웃 안에 레이아웃을 넣는 방식을 사용하면 복잡하지만 다이나믹한 화면을 연출 할 수 있게 된다.

크기순서 : Window > Surface > Canvas > View

![4](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fk.kakaocdn.net%2Fdn%2F8meYQ%2FbtqwBXNiVvA%2F5WWUalKRPLt73lXob4PTs0%2Fimg.png)

#### Window

![5](https://miro.medium.com/max/2160/1*GqBKZVXpY-0dqwoY24PzyQ.png)

색이 있는 네모 3개가 Window이다. 위와 같이 하나의 화면 안에는 여러개의 Window를 가질 수 있고 이러한 Window들은 WindowManager가 관리를 합니다.

Window는 뭔가를 그릴 수 있는 창이라고 생각하면 됩니다. 보통 하나의 Surface를 가지고 있으며, Application은 Window Manager와 상호작용하여 Window를 만듭니다. Window Manager는 각각의 Window 표면에 Component를 그리기 위해 Surface를 만듭니다.

#### Surface

각각의 Window는 Surface를 가집니다.

Surface는 화면에 합성되는 픽셀을 보유한 객체입니다. 화면에 표시되는 모든 Window는 자신만의 Surface가 포함되어 있으며, Surface Flinger가 여러 소스로부터 그래픽 데이터 버퍼를 받고, 그것들을 합성해서 Display로 보냅니다.

개별 Surface는 이중 버퍼 렌더링을 위한 1개 이상(보통 2개)의 버퍼를 가집니다.

#### Canvas

실제 UI를 그리기 위한 공간으로 비트맵이 그려지는 공간이라고 생각합니다.

#### View

View는 Window 내부의 대화식 UI 요소입니다. 창에는 단일 뷰 계층 구조가 연결되어 있으며 모든 창의 동작을 제공합니다.

Window가 다시 뭔가를 그려야할 때마다 Window의 Surface에서 작업이 수행됩니다. 만약 Surface가 잠기면 그리는데 사용할 수 있는 Canvas가 반환이 됩니다. 그럼 Draw Traversal가 계층적으로 각 뷰를 Canvas에 전달하여 UI를 그리기 시작합니다.

완료가 되면 Surface가 잠기고 방금 그린 Buffer가 포그라운드 상태로 바뀌고 Surface Flinger에 의해서 화면에 합성됩니다.

#### SurfaceView

일반적인 View는 Main Thread에서 캔버스를 그리기 때문에, 그리기를 하는 동안에는 사용자의 입력을 받을 수 없고 그로 인해 반응성이 좋지 못합니다. 그렇다고 그리는 작업을 별도의 작업 스레드에서 처리하고 싶어도 안드로이드 정책 상 Main Thread가 아닌 별도의 Thread에서는 UI 관련 작업을 할 수도 없습니다. 이럴때 사용할 수 있는게 SurfaceView입니다.

SurfaceView는 Canvas 아닌 Surface(=가상 메모리 화면)에 그리고 그려진 Surface를 화면에 뿌리기 때문에 게임이나, 카메라 같은 높은 반응성이 필요한 UI 작업이 필요한 경우 사용할 수 있습니다.

#### View와 ViewGroup

![6](http://cdn.learncomputer.com/wp-content/uploads/2012/01/image0033.png)

안드로이드에서 UI요소들은 크게 View와 ViewGroup으로 이루어져 있으며 아래와 같은 특징들을 가지고 있습니다.

View는 화면상의 UI를 구성합니다.
ViewGroup은 ViewGroup과 View를 포함합니다.
TextView, Button등 기본적인 화면 구성 요소들은 View에 상속되어 구현됩니다.
개별적으로 속성을 가지며 개별 속성은 Parent로부터 상속을 받을 수 있습니다.
View는 Window의 Surface를 이용하여 화면을 그리고, 이벤트를 어떻게 처리할지에 대한 구현을 하는 객체입니다.

_ _ _

출처: https://www.crocus.co.kr/1545 [Crocus], https://www.oss.kr/, https://jungwoon.github.io/android/2019/10/02/How-to-draw-View/