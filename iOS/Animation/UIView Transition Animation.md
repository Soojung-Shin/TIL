# UIView Transition Animation

뷰 전체가 바뀌는 애니메이션이다. 트랜지션을 이용해 다른 뷰로 변경될 때 부드럽게 전환하거나 애니메이션을 사용할 수 있다. 예를 들어 카드를 실제로 뒤집는 것 처럼 3D 애니메이션을 넣을 수 있다. 위 아래를 뒤집으면서 뷰의 뒷 화면을 보는 애니메이션도 있다. 그런 애니메이션은 옵션을 통해서 지정할 수 있다. 

트랜지션 애니메이션에는 두 가지 종류가 있다. 

<br />

### 기존 뷰의 서브 뷰 바꾸기

```swift
class func transition(
            with view: UIView, 
             duration: TimeInterval, 
              options: UIView.AnimationOptions = [], 
           animations: (() -> Void)?, 
           completion: ((Bool) -> Void)? = nil
)
```

여기서 view는 트랜지션이 발생하는 컨테이너 뷰로, 해당 뷰의 서브 뷰들이 전환된다.

<br />

<br />

### 뷰를 새로운 뷰로 대체하기

```swift
class func transition(
        from fromView: UIView, 
            to toView: UIView, 
             duration: TimeInterval, 
              options: UIView.AnimationOptions = [], 
           completion: ((Bool) -> Void)? = nil
)
```

`fromView`를 `toView`로 전환한다.

<br />

<br />

애니메이션 블록에서 뷰 속성만 변경할 수 있는게 아니라 원하는 모든 것을 변경할 수 있다. 애니메이션 시스템이 하는 일은 클로저 전에 뷰를 그린다. 그런 다음 클로저를 실행하고 다음 뷰를 그리고 뒤집거나 크로스 디졸브를 한다.

<br />

❗️뷰 트랜지션은 뷰 컨트롤러에 의한 트랜지션과는 다르다! 뷰 컨트롤러 트랜지션은 window에 표시되는 전체 뷰가 바뀌는 것이지만, 여기서 말하는 뷰 트랜지션은 현재 뷰의 계층 구조 내에서의 변화가 일어나는 것을 의미한다. 즉, 네비게이션 컨트롤러 상의 뷰 스택에는 변경이 없다.

<br />

<br />

### 사용할 수 있는 트랜지션 애니메이션 옵션들

```swift
static var transitionFlipFromLeft: UIView.AnimationOptions
static var transitionFlipFromRight: UIView.AnimationOptions
static var transitionCurlUp: UIView.AnimationOptions
static var transitionCurlDown: UIView.AnimationOptions
static var transitionCrossDissolve: UIView.AnimationOptions
static var transitionFlipFromTop: UIView.AnimationOptions
static var transitionFlipFromBottom: UIView.AnimationOptions
```

<br />

<br />

<br />

## ✍️ UIView Transition 예시

```swift
//이 메소드는 UIView에서 사용한다.
UIView.transition(
    with: chosenCardView,
    //애니매이션이 적용될 뷰
    duration: 0.75,
    //애니메이션 동작 시간
    options: [.transitionFlipFromLeft],
    //카드를 왼쪽에서 오른쪽으로 뒤집는 애니메이션 옵션
    animations: { cardIsFaceUp = !cardIsFaceUp }
    completion: nil
)
```

<br />
<br />

<p align="center">
  <img src="https://user-images.githubusercontent.com/16719527/72584535-cb93e400-392d-11ea-9e24-004c2661195a.gif">
</p>

카드를 클릭했을 때, 뷰를 카드 뒤집듯 오른쪽으로 뒤집는 애니메이션에 UIView transition을 사용했다. 카드를 확인하고, 맞는 카드가 아니라면 다시 뒤집는데에도 UIView transition이 사용되었다.

참고로 같은 카드를 선택했을 때 확대되는 애니메이션, 투명해지면서 없어지는 애니메이션에는 `UIViewPropertyAnimator` 클래스를 사용했다.

<br />
<br />

------

https://developer.apple.com/documentation/uikit/uiview/1622574-transition

https://developer.apple.com/documentation/uikit/uiview/1622562-transition

https://developer.apple.com/documentation/uikit/uiview/animationoptions

https://soooprmx.com/archives/4391
