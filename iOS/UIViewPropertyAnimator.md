# UIViewPropertyAnimator

UIViewPropertyAnimator 클래스를 사용해 뷰의 속성에 애니메이션을 적용할 수 있다.

<br />

### 애니메이션을 적용할 수 있는 속성들

```swift
frame/center  //뷰의 위치를 이동시킬 수 있다.
bounds        //뷰의 크기를 움직이게 할 수 있다.
transform     //회전, 크기 변경이 가능하다. bounds를 조정하는 것보다 이걸 이용하는게 더 좋다.
alpha         //불투명도 조절한다.
backgroundColor //배경 색을 조절할 수 있다.
```


<br /><br />

### property animator 만들기
어떤 애니메이션을 원하는지(option), 시간(duration), 속도, 딜레이(delay) 등을 설정한 후에 클로저와 함께 사용하고, 클로저 안에 아래 속성들을 바꾸는 코드를 작성하면 된다.

runningPropertyAnimator는 애니메이션을 생성 즉시 실행한다.

```swift
//runningPropertyAnimator는 즉시 실행되는 애니메이터다.
class func runningPropertyAnimator(
  withDuration: TimeInterval,
  //얼마나 오랫동안 지속될지
  delay: TimeInterval,
  //얼마나 기다린 후에 시작될지. 다른 애니메이션이 진행하고 있을 때 애니메이션을 연결시키는 방법도 가능하다. 
  options: UIViewAnimationOptions,
  //애니메이션 실행 방법
  animations: () -> Void,
  //클로져에 속성을 변경시키는 코드를 작성한다.
  completion: ((position: UIViewAnimatingPosition) -> Void)? = nil
  //애니메이션이 실행을 마치면 호출된다.
  //position이 시작이 될 수도 있다. 왜냐하면 애니메이션이 거꾸로 움직일 수도 있기 때문. 끝이 될 수도 있다. 애니메이션이 끝까지 왔다는 의미.
  //애니메이션이 중간에 중단되면 position이 현재(.current)라고 불리게 될 것.
)
```

<br />그럼 애니메이션은 언제 중간에 중단될까?

동일한 속성을 가진 다른 애니메이션이 시작되면 해당 애니메이션이 우선순위를 가지게 된다. 여러 개의 속성 애니메이터를 나란하게 둘 수도 있다. 모두 서로 다른 속성을 수정하다가 하나가 다른 애니메이터의 속성을 수정하기 시작하면 나중에 수정한 쪽이 이긴다. 그럼 원래 건 멈추고 현재 위치에서 완료하게 된다.

<br /><br />

### 애니메이션 실행하기

Animator를 만들면 해당 애니메이션을 실행할 때 `startAnimation()` 메소드를 호출하면 된다.

아래 예제는 runningPropertyAnimator를 이용했기에 생성 즉시 애니메이션이 실행될 것이다.

```swift
//불튜명한 뷰를 점점 희미하게 만들다가 사라지면 슈퍼 뷰에서 제거하는 애니메이션.
if myView.alpha == 1.0 {
  UIViewPropertyAnimator.runningPropertyAnimator {
    withDuration: 3.0,
    delay: 2.0,
    options: [.allowUserInteraction],
    //.allowUserInteraction은 애니메이션이 실행되는 동안(페이드아웃 되는 동안) 제스처 같은 게 그대로 작동한다는 뜻이다.
    //이걸 지정하지 않으면 이런 종류의 애니메이션이 진행되는 동안 다른 것을 누를 수 없게 된다.
    animations: { myView.alpha = 0.0 },
    completion: { if $0 == .end { myView.removeFromSuperview() } }
  }
  print("alpha = \(myView.alpha)")
  //이 메소드는 속성 애니메이터를 리턴하고 바로 해당 클로저를 실행한다.
  //그 후 이 애니메이션이 중단되지 않고 정상 종료 된다면 슈퍼뷰에서 해당 뷰를 제거하는 completion이 실행된다.
  //print에 의해서 'alpha = 0.0'이 출력될 것이다.
}
```

<br />우리가 인자로 전달한 클로져는 즉시 처리된다. 5초, 10초 지속시간을 어떻게 설정했던 간에 클로져는 바로 실행되어 적용된다. 애니메이션은 사용자에게만 보이게 된다. 사용자는 애니메이션이 설정한 duration동안 발생하는 것으로 보이지만 실제로 실행되는 건 애니메이션을 시작한 그 순간이다. 코드로는 바로 변경되지만, 사용자에게만 해당 애니메이션이 보인다. 이런 시간 차이를 잘 이해해야 한다~!!
<br />

<br />

### Animator Option

option을 사용하면 앞뒤로 재생, 자동 반복, 반전 애니메이션 등을 쉽게 사용할 수 있다.

애니메이션에 쓸 수 있는 옵션들에는 어떤게 있을까?

```swift
UIViewAnimationOptions {
  beginFromCurrentState
  //한 애니메이션이 실행 중일 때, 같은 속성의 다른 애니메이션을 시작하려고 할 때 사용된다.
  //새로운 애니메이션의 속성값이 특정 값에서 시작되는지(위 예시의 alpha = 0.0) 그 속성값이 바로 적용되는지 아니면 진행 중이었 애니메이션의 값을 이어서 실행하게 되는지 설정한다.
  //애니메이트 되고 있는 알파의 값을 가져올지 아니면 코드에 써있는 0인 알파값을 가지고올지에 대한 것.
  allowUserInteraction
  layoutSubviews
  repeat
  autoreverse
  overrideInheritedDuration
  overrideInheritedCurve
  allowAnimatedContent
  curveEaseInEaseOut
  //애니메이션이 천천히 움직이다가 속도가 빨라지고, 나중에 느려지게 한다. 자연스럽고 부드러운 애니메이션.
  curveEaseIn
  curveLinear
  //애니메이션이 일정한 속도로 움직이게 한다.
}
```

<br />

<br />

<br />

<br />

## 📖 UIViewPropertyAnimator를 이용해 자연스럽게 뷰의 배경색 변경하기

애니메이션을 이용해 뷰의 배경색을 바꿔볼 것이다. UIViewPropertyAnimator 클래스에서 애니메이션을 즉시 실행하는 runningPropertyAnimator를 이용할 것이다.

스토리보드에서 배경색을 확인할 뷰를 만들고, 두 개의 버튼을 만들어 각각 다른 backgroundColor를 지정했다.
<br />


```swift
import UIKit

class ViewController: UIViewController {

    @IBOutlet weak var colorView: UIView!

    @IBAction func changeViewColor(_ sender: UIButton) {
        
        UIViewPropertyAnimator.runningPropertyAnimator(
            withDuration: 1,
            delay: 0,
            options: [.allowUserInteraction],
            animations: { self.colorView.backgroundColor = sender.backgroundColor },
            completion: nil)
    }
    
}
```

<br />

이때 중요한 것은 사용자에게는 애니메이션이 실행되는 동안 값이 서서히 변경되는 것처럼 보이지만, 실제로 클로져는 애니메이션이 실행된 그 순간 바로 실행된다. 애니메이션이 실행하자마자 클로져의 코드는 바로 반영되는 것! 이런 시간 차이를 잘 이해해야 한다.
<br />
<br />

<p align="center">
  <img src="https://user-images.githubusercontent.com/16719527/72581985-9b941300-3924-11ea-8153-563eb219a26c.gif">
</p>

<br />

```swift
class ViewController: UIViewController {
    
    let backgroundColors: [UIColor] = [ .red, .orange, .yellow, .green, .blue, .purple ]
    var currentColorIndex = 0

    @IBOutlet weak var colorView: UIView!
    
    @IBAction func changeViewColor(_ sender: UIButton) {
        UIViewPropertyAnimator.runningPropertyAnimator(
            withDuration: 0.4,
            delay: 0,
            options: [.allowUserInteraction],
            animations: {
                self.colorView.backgroundColor = self.backgroundColors[self.currentColorIndex]
            },
            completion: {
                if $0 == .end {
                    self.currentColorIndex += 1
                    if self.currentColorIndex == self.backgroundColors.count { self.currentColorIndex = 0 }
                }
            }
        )
    }
}

```

<br />

애니메이션이 정상 종료되었을 때(position이 .end일 때), 인덱스 값을 1씩 증가시킨다. 

<br />
<br />

<p align="center">
  <img src="https://user-images.githubusercontent.com/16719527/72582008-b36b9700-3924-11ea-8c92-9e7cef573d6a.gif">
</p>

<br />
