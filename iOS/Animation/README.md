# Animation

### Animating UIView properties

아주 많이 사용하는 애니메이션 중 하나.

```swift
frame/center
bounds
//transient size, does not conflict with animating center
transform
//translation, rotation and scale
alpha
//opcity
backgroundColor
```

이런 속성을 이용해서 애니메이션을 줄 수 있다!

<br />

### Animating Controller transitions(as in a UINavigatinController)

예시로 네비게이션 컨트롤러에서 뷰를 푸시/팝 할때 뒤집거나, 슬라이드 하거나 등등의 애니메이션을 넣을 수 있다.

<br />

### Core Animation

드로잉을 위해 UI 아래에 CA 레이어가 있는 것처럼 UIView 애니메이션 속성 아래에 코어 애니메이션이 있다. 애니메이션 속성의 기본값이다. 

<br />

### OpenGL and Metal

3D 애니메이션 엔진

<br />

### SpriteKit

2.5D 애니메이션. 이미지를 가지고 있고, 3D 세계에서처럼 이미지를 겹치되 실제 드로잉은 2D로 한다.

<br />

### Dynamic Animation

동적 애니메이션이다. 물리학을 이용해서 애니메이션 효과를 부여한다. 뷰에 질량과 속도, 탄성 등을 주고 실행한다. 질량이나 무엇과 부딪히는지에 따라서 여러가지 애니메이션을 발생하게 한다. 한꺼번에 뷰를 움직이거나 다른 뷰들과 상호작용시킬 때 매우 유용하다.

아이폰을 위로 슬라이드 했을 때 약간 튕기는 것을 볼 수 있다. 다이나믹 애니메이션을 이용해 탄성을 약간 준 것이다.

