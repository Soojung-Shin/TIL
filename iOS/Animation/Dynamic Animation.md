# Dynamic Animation

뷰에 물리적 속성을 설정하고 무엇을 할지 지시한다. 물리는 밀도, 마찰, 중력과 같은 것을 의미한다.

이렇게 속성들을 설정해주면, 그 속성에 맞게 애니메이션이 자동으로 실행된다.

<br />

### 1. UIDynamicAnimator 만들기

```swift
var animator = UIDynamicAnimator(referenceView: UIView)
```

첫번째로 애니메이터를 만들어야한다. UIDynamicAnimator라는 인스턴스 클래스를 이용한다. 이 클래스는 이니셜라이저에서 인수 하나만을 사용하며 진행 중인 모든 애니메이션이 참고할 좌표계가 될 뷰이다.

<br />

### 2. 동작 추가하기 (Animator에 UIDynamicBehavior 인스턴스 추가)

```swift
let gravity = UIGravityBehavior()
animator.addBehavior(gravity)
let collider = UICollisionBehavior()
animator.addBehavior(collider)
```

뷰 안의 것들이 어떻게 동작하는지 설정한다. 뷰 안의 것들이 중력, 서로 충돌하는지 아닌지와 같은 것들을 설정해준다.

동작을 만든 후 `addBehavior()` 를 통해서 애니메이터에 추가해준다. 

<br />

### 3. 아이템 추가하기 (Behavior에 UIDynamicItems 추가)

```swift
let item1: UIDynamicItem = ... //보통은 UIView
let item2: UIDynamicItem = ...
gravity.addItem(item1)
collider.addItem(item1)
gravity.addItem(item2)
```

behavior에 item을 추가한다. behavior에 아이템을 추가하는 순간 해당 behavior의 영향을 받기 시작한다.

item은 UIView가 아니라 UIDynamicItem 프로토콜로 구현되는 모든 객체가 항목이 될 수 있다. 대부분 UIView에 사용한다. 

<br />

```swift
//뷰가 구현한 UIDynamicItem 프로토콜. 
protocol UIDynamicItem {
  var bounds: CGRect { get }
  //크기. read only. 뷰를 위한 것으로 뷰가 bounds를 보고 경계를 정한다. 애니메이터는 실제로 bounds를 변경하지 않는다.
  var center: CGPoint { get set }
  //위치
  var transform: CGAffineTransform { get set }
  //회전, 크기 조정...
  var collisionBoundsType: UIDynamicItemCollisionBoundsType { get set }
  var collisionBoundingPath: UIBezierPath { get set }
}

//객체에 동작을 만듦으로서 애니메이터에 추가하면 애니메이터가 그것을 소유하게 되는 것이다.
//애니메이터가 center와 transform을 소유하여 여러 형태로 변경하는 것이다.
//그래서 만약 이미 동작을 지정해준 뷰의 center나 transform을 바꾸고 싶다면 Dynamic Animator에서 아래의 메소드를 불러야한다.
//즉, 내가 센터가 변형을 바꿨어요. 그러니 애니메이터님 받아주세요~!!라고 알리는 것.
//그러면 애니메이터가 그 상태를 받고 객체를 움직이고 transform을 변경하면 객체가 그렇게 작동하게 된다. 그 부분부터 계속 작동한다.
func updateItemUsingCurrentState(item: UIDynamicItem)
```

<br /><br /><br />

## Behaviors

### UIGravityBehavior

중력 동작이다. angle을 통해 방향을 설정할 수 있다.

```swift
UIGravityBehavior {
  var angle: CGFloat
  //기본값으로 아래쪽(홈버튼쪽)을 향한다.
  var magnitude: CGFloat
  //1.0 = 1000 point/s/s. 
}
```

<br />

### UIAttachmentBehavior

두 객체를 막대기로 연결시켜놓은 것처럼 행동하게 할 수 있다. 만약 여기서 중력 동작 등을 추가한다면 두 객체가 함께 움직일 것이다. 만약 연결되어있는 한 객체에 충돌 동작이 발생한다면 나머지 하나는 진자처럼 흔들릴 것이다.
한 객체를 한 점에 붙인다고 생각하면, 만약 중력 동작을 주면 그 객체가 낙하하다가 최대 길이에 도달하면 진자처럼 좌우로 흔들릴 것이다(시계추처럼..!). 그리고 결국에 중력이 객체를 잡아당겨 수직으로 밑을 향하게 멈출 것이다.

```swift
UIAttachmentBehavior {
  init(item: UIDynamicItem, attachedToAnchor: CGPoint)
  init(item: UIDynamicItem, attachedTo: UIDynamicItem)
  init(item: UIDynamicItem, offsetFromCenter: CGPoint, attachedTo[Anchor]...)
  var length: CGFloat
  var anchorPoint: CGPoint
}
```

애니메이션이 진행 중일 때도 막대기의 길이를 조절할 수 있다. 길이도 막대기의 길이를 조절할 수 있다. 길이도 애니메이션화 되는 것이다. 객체들이 떨어지다가, 튕기다가, 충돌하면 객체들이 이어져있으면 그 사이의 막대기를 조절해 더 가깝게하거나 더 멀어지게 할 수 있다.
막대기에 제동을 일정량 추가해 탄력있게 만들 수도 있다. 어딘가에 부딪히게 되면 객체들의 사이가 가까워지다가 원래의 길이로 돌아가게 할 수 있다.

<br />

### UICollisionBehavior

충돌 동작으로, 가장 흔한 동작 중 하나다. 객체나 UIView가 서로 튕기거나 BezierPath로 튀는 것이다. 기본적으로 백그라운드에서 진행된다. 고정 경계를 BezierPath로 지정하고 아이템을 추가하면 이런 작동을 설정할 수 있다. 아이템끼리 튀거나 BezierPath로 지정된 경계에서 튀기게 설정할 수도 있다.
BezierPath에 관한 모든 것은 레퍼런스 뷰 좌표계에 있다. 그 선은 그려지는 것이 아니라 공간의 개념적인 경계선 같은 것이다.

```swift
var collisionMode: UICollisionBehaviorMode
//.items, .boundaries, .everything

UICollisionBehavior {
  func addBoundary(withIdentifier: NSCopying, for: UIBezierPath)
  func addBoundary(withIdentifier: NSCopying, from: CGPoint, to: CGPoint)
  func removeBoundary(withIdentifier: NSCopying)
  
  var traslatesReferenceBoundsIntoBoundary: Bool
  //충돌 동작에서 이걸 true로 설정하면 레퍼런스 뷰의 바깥 모서리가 경계선이 된다.
  //객체는 보통 레퍼런스 뷰 안에서 움직인다. 보통 레퍼런스 뷰를 충돌 경계선이라고 지정하면 어떤 객체도 밖으로 나가지 않을 것이라고 생각하는데 아니다.
  //예를 들어 너무 빨리 움직여서 한 애니메이션 프레임 동안 경계선 안쪽에서 경계선 바깥쪽으로 움직이게 되면 그냥 밖으로 나가 버린다. (영원히 없어져버림.....)
  //충돌 경계선은 충돌 여부를 한 프레임에 한 번씩 확인한다. 그래서 객체를 경계선 안쪽에 넣는다고 해도 가두를 것을 보장할 수 없다.
  
  NSCopying
  //옵젝씨에 관한 것으로, NSString이나 NSNumber를 의미하지만 as를 이용해 String, Int, Double...같은 것으로 형변환 할 수 있다.
}

var collisionDelegate: UICollisionBehaviorDelegate
//Delegate는 언제 충돌이 발생했는지 알려준다.
//만약 어떤 걸 collisionDelegate로 지정한다면 이런 CollisionBehavior 메소드를 사용할 수 있다.

func collisionBehavior(
  	behavior: UICollisionBehavior,
    began/endedContactFor: UIDynamicItem,
  	withBoundaryIdentifier: NSCopying //with: UIDynamicItem too
  	at: CGPoint
)
```

<br />

### UISnapBehavior

동적 애니메이션 시스템을 사용하게 될 때, 즉 어떻게 움직일지 결정할 때 사용한다. 어떤 객체를 어느 곳에서 다른 곳으로 옮길 때 사용한다. 뷰 속성 애니메이션을 사용하는 것과는 다르다.

한 점에 스냅하고 싶을 때 도착지에 도착하면 마치 네 모서리에 스프링이 달린 것 처럼 진동하며 멈춘다. 그래서 화면상에서 움직이다가 멈추는 애니메이션을 좀 더 자연스럽게 연출할 수 있다.

```swift
UISnapBehavior {
  init(item: UIDynamicItem, snapTo: CGPoint)
  var damping: CGFloat
}
```

<br />

### UIPushBehavior

객체를 미는 동작이다. 지속적으로 밀 수도 있고 마치 펀치를 하는 것 처럼 한 번만 밀 수도 있다. 미는 동작의 각도와 크기를 지정해줄 수 있다.

```swift
UIPushBehavior {
  var mode: UIPushBehaviorMode
  var pushDirection: CGVector
  ...
  var angle: CGFloat
  var magnitude: CGFloat
}
```

이 동작은 애니메이터에 추가된다. 그리고 일회성 동작이기 때문에 한 번 하면 그 후 아무것도 하지않고 가만히 있는다. 그러니 PushBehavior를 추가할 때 한번 작동하고 나면 할 일을 다 했으니 지워주는 기능이 있으면 좋다. (메모리 사이클이 발생할 수 있음 [관련 문서](https://github.com/Soojung-Shin/TIL/blob/master/Swift/Closure%20Capturing%20%26%20Memory%20Cycle.md))

<br />

### UIDynamicItemBehavior

메타 동작이다. 뷰가 움직이는 동안 뷰의 마찰, 탄성, 회전 등의 동작을 지정해줄 수 있다. 이건 다른 동작에도 영향을 준다. 만약 마찰력을 더 추가하면 당연히 중력이 잡아당겨도 마찰력때문에 천천히 움직일 것이다.

또한 UIDynamicItem에게 현재 속력이 어떻게 되는지, 회전하고있다면 얼마나 빠르게 회전하는지 등을 물어볼 수 있다.

```swift
UIDynamicItemBehavior {
  var allowsRotation: Bool
  var friction: CGFloat
  var elasticity: CGFloat
  
  func linearVelocity(for: UIDynamicItem) -> CGPoint
  func addLinearVelocity(CGpoint, for: UIDynamicItem)
  func angularVelocity(for: UIDynamicItem) -> CGFloat
}
```

<br />

### UIDynamicBehavior

위의 모든 동작의 슈퍼 클래스다. 모든 동작을 모아 한 동작으로 만들 때 사용한다. 그렇게 하면 여러 가지 아이템이 추가된 한 동작이 있고, 그 밑으로 아이템의 동작을 지정하는 자식 클래스들이 존재한다.

```swift
UIDynamicBehavior {
  func addChildBehavior(UIDynamicBehavior)
  //UIDynamicBehavior의 서브 클래스에서 이 메소드를 이용해 호출하면 그 동작이 추가된다.
  
  var dynamicAnimator: UIDynamicAnimator? { get }
  //이 애니메이터는 지금 애니메이션을 실행하고 있는 애니메이터가 있다면 그 애니메이터를 의미한다.
  //지금 이 애니메이터에 의해서 이 동작이 되는 건가? 아니면 애니메이션이 안되는 건가? 만약 그렇다면 누가 하는건가?에 대해서 알 수 있음.
  
  func willMove(to: UIDynamicAnimator?)
  //다른 애니메이터로 전환시키는 메세지.
  //이런 메세지는 보통 애니메이션이 안되는 상태에서 되게 하거나, 그 반대의 경우에 나타난다.
  
  var action: (() -> Void)?
  //UIDynamicBehavior에는 새로운 서브 클래스를 만들 때 상속되는 멋진 변수가 하나 있다. 이 클로져는 UIDynamicBehavior가 작동(act)할 때 항상 실행된다.
  //예를 들면 순간적인 PushBehavior은 이 클로져가 한 번만 실행된다. 오브젝트에 딱 한 번만 진행되기 때문이다.
  //반면에 중력이나 충돌 동작에서는 객체에 항상 작동되기 때문에 클로져가 많이 실행된다.
  //그러니 절대로 실행 시간이 긴 코드를 클로져에 작성하면 안된다! 왜냐면 애니메이션이 느려질수도 있다.
  //이걸 이용해서 동작이 어떤 항목에 적용되는지 볼 수 있고, 그 동작으로 인해 한 항목이 레퍼런스 경계를 벗어났는지 확인할 수 있다.
  //그럼 그걸 돌려놓은 것인지, 삭제해야 하는지 뭘 어떻게 하든 결정할 수 있다.
}	
```

<br /><br /><br />

## Stasis(정지)

실제로 대부분 우리가 애니메이션 메커니즘을 디자인 할 때 애니메이션이 언젠간 멈추기를 바랄 것이다. 언제 정지 상태로 멈추게 되는지 UIDynamicAnimator의 delegate로 알아낼 수 있다.

```swift
var delegate: UIDynamicAnimatorDelegate

func dynamicAnimatorDidPause(UIDynamicAnimator)
//애니메이션이 정지 상태가 됐다고 알려주는 메소드.
func dynamicAnimatorWillResume(UIDynamicAnimator)
//다시 움직이기 시작한다고 알려주는 메소드.
```

<br /><br /><br />

## ✍️ Dynamic Animation 예시

애니메이터에 충돌, 탄력성, 저항, 푸시 동작 애니메이션을 추가하고, 모든 카드를 아이템으로 넣어주었다.

푸시 동작은 카드가 뒷면으로 다시 뒤집혔을 때 실행되도록 했다.

```swift
lazy var collisionBehavior: UICollisionBehavior = {
  let behavior = UICollisionBehavior()
  behavior.translatesReferenceBoundsIntoBoundary = true
  return behavior
}()

lazy var itemBehavior: UIDynamicItemBehavior = {
  let behavior = UIDynamicItemBehavior()
  behavior.allowsRotation = false
  behavior.elasticity = 1.0
  behavior.resistance = 0
  return behavior
}()

func push(_ item: UIDynamicItem) {
  let pushBehavior = UIPushBehavior(items: [item], mode: .instantaneous)
  //카드 뷰가 화면의 모서리에 모여있지 않도록 push 애니메이션의 angle을 중앙을 향하도록 한다.
  if let referenceBounds = dynamicAnimator?.referenceView?.bounds {
    let center = CGPoint(x: referenceBounds.midX, y: referenceBounds.midY)
    switch (item.center.x, item.center.y) {
      case let (x, y) where x < center.x && y < center.y:
      pushBehavior.angle = (CGFloat.pi / 2).arc4random
      case let (x, y) where x > center.x && y < center.y:
      pushBehavior.angle = CGFloat.pi - (CGFloat.pi / 2).arc4random
      case let (x, y) where x < center.x && y > center.y:
      pushBehavior.angle = (-CGFloat.pi / 2).arc4random
      case let (x, y) where x < center.x && y < center.y:
      pushBehavior.angle = CGFloat.pi + (CGFloat.pi / 2).arc4random
      default:
      pushBehavior.angle = (CGFloat.pi * 2).arc4random
    }
  }
  pushBehavior.magnitude = 1 + CGFloat(drand48())
  //pushBehavior는 일회성 애니메이션이기 때문에 사용되면 삭제해주는 것이 좋다.
  //액션에서 처리해주는데, 이때 애니메이터와 behavior 클로져가 서로를 가리키고 있기 때문에 메모리 사이클이 발생한다.
  //unowned와 weak를 사용해 클로져에 변수를 선언하여 힙에 존재하지않도록 설정하여 해결할 수 있다.
  pushBehavior.action = { [unowned pushBehavior, weak self] in
                         self?.removeChildBehavior(pushBehavior)
                        }
  addChildBehavior(pushBehavior)
}
```

<br /><br />

<p align="center">
  <img src="https://user-images.githubusercontent.com/16719527/72584535-cb93e400-392d-11ea-9e24-004c2661195a.gif">
</p>

카드들이 서로 부딪히면 충돌하고, 뒷면으로 뒤집혀졌을 때 중앙을 향해 밀려나는 것을 볼 수 있다.

