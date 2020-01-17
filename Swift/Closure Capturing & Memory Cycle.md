# 📖 Closure Capturing

클로져 안에 지역 변수를 정의할 수 있다. 클로져 안으로 들어가기 직전에 대괄호 안에 지역 변수를 넣을 수 있다. 어떤 변수도 다 정의할 수 있고, 초기값도 지정해줄 수 있다.

```swift
var foo = { [x = someInstanceOfaClass, y = "hello"] in
  // use x and y here
}
```

<br />

굳이 변수로 선언 하지않고 그냥 클로져 안에 써도 될 것 같은데 이게 왜 중요할까? 왜냐면~~~ 바로바로 `weak`로 선언할 수 있기 때문이다!

```swift
var foo = { [weak x = someInstanceOFaClass, y = "hello"] in
  //use x and y here, but x is now an Optional because it's weak
  //weak로 선언한 변수는 옵셔널이 되고, 힙에 저장되지 않는다. 그리고 그 변수들은 weak여야 한다. 왜냐면 그래야지 그 변수가 가리키고 있는 것이 힙에서 벗어나게 되면 nil로 변할거니깐.
  //x는 이제 옵셔널이고 weak다.
}
```

<br />

또한 지역 변수를 `unowned`로 선언할 수도 있다.

```swift
var foo = { [unowned x = someInstanceOfaClass, y = "hello"] in
  //use x and y here, x is not!!! an Optional
  //if you use x here and it is not in the heap, you will crash
}
```
`unowned`는 레퍼런스에 속하지 않는다는 것을 의미한다. 하지만 만약 힙에 존재하지 않는 것을 접근하려고 하면 앱이 죽을 것..!

<br />



## Memory Cycle

그럼 왜 `weak`나 `unowned`를 사용하는 것이 중요할까? `weak`나 `unowned` 변수는 지역 변수가 아니더라도 힙에 저장되지 않는다.

이런 점을 이용해서 메모리 사이클을 해결할 수 있다~!! 아래 예시를 보자.

```swift
//메모리 사이클이 발생하는 예시
class Zerg {
  private var foo = {
    self.var()
  }
  private func bar() {...}
}
```
클로져 안에서 클로져의 값을 정하기 위해 Zerg의 다른 함수인 bar를 호출했다. 이것은 메모리 사이클이다. 왜냐~!! foo가 클로져인데 self를 접근하기 때문이다(referencing self). self는 Zerg 클래스를 가지고 있다. 클래스 Zerg의 인스턴스를 가지고 있다. 메모리에서!

Zerg는 클로져를 메모리 안에 두고 가지고 있다. 그것을 가리키는 변수를 가지고 있기 때문이다. 그러니 각자 서로를 메모리에서 가리키고 있다.

<br />

이 메모리 사이클 문제를 해결해보자!

```swift
class Zerg {
  private var foo = { [weak weakSelf = self] in
    weakSelf?.bar()
    //클로져 안의 그 어떤 변수도 strong 포인터로 자기 자신을 가리키지 않는다. 자기 자신을 힙에 가두지 않는다. 더이상 메모리 사이클이 발생하지 않는다.
    //클로져 안의 지역 변수를 바깥 범위의 변수와 같은 이름으로 지정할 수 있다. -> [weak self = self]
    //등호를 생략하면 자동으로 바깥 코드에 쓰이는 동일한 이름의 변수와 같게 된다. -> [weak self]
  }
  private func bar() {...}
}
```

<br />

<br />

<br />

## 📖 Memory Cycle Avoidance 예시

만약에 아래와 같은 pushBehavior가 있다고 하자.

```swift
if let pushBehavior = UIPushBehavior(items: [...], mode: .instantaneous) {
  //instantaneous : 순간적인 동작
  pushBehavior.magnitude = ...
  pushBehavior.angle = ...
  pushBehavior.action = { 
    //pushBehavior는 일회성 동작이기 때문에 push 동작을 하자마자 삭제되었음 좋겠다. 바로 삭제해야 힙 메모리 낭비하지 않겠지...이제 쓸모없어지는 동작이니까!
    pushBehavior.dynamicAnimator!.removeBehavior(pushBehavior)
  }
  animator.addBehavior(pushBehavior)
}
```

하지만 위 같은 방식으로 behavior를 삭제하면 메모리 사이클이 발생한다..! pushBehavior가 클로저를 가리키고있고, 클로저가 pushBehavior를 가리키고있다. 둘 다 힙에 위치한 것을 가리키고 있기 때문에 둘다 힙에 머무르게 될 것... 그렇다면 어떻게 해결해야할까?

<br />

<br />

```swift
if let pushBehavior = UIPushBehavior(items: [...], mode: .instantaneous) {
  pushBehavior.magnitude = ...
  pushBehavior.angle = ...
  pushBehavior.action = { [unowned pushBehavior] in
    pushBehavior.dynamicAnimator!.removeBehavior(pushBehavior)
  }
  animator.addBehavior(pushBehavior)
}
```
pushBehavior를 `unowned`인 지역 변수로 선언한다면 더이상 레퍼런스 횟수 계산 시스템이 관리하지 않기 때문에 메모리 사이클을 끊을 수 있다.
`unowned`이기 때문에 힙에 존재하지 않으면 앱에 crash가 날 것이다. 하지만 당연하게도 pushBehavior는 힙에 있을 것이기 때문에 문제가 없다.
pushBehavior가 힙에 없다면 애초에 작동되지도 않았을 것이고, 힙에 있지 않으면 action이 실행되지 않았을 것이다. pushBehavior가 힙에 있다는 것을 보장할 수 있다.
`weak`를 사용하거나, nil 여부를 확인하는 것도 추가할 수 있지만 모두 불필요하다. 절대로 nil이 될 수 없고 절대로 힙 안에 없을리가 없기 때문이다.
