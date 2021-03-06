# View Controller Lifecycle

생명주기는 `UIViewController`가 호출한 메시지나 메소드의 순서로 표시된다. 왜 뷰 컨트롤러의 생명주기가 중요할까? 왜냐하면 뷰 컨트롤러 생명주기의 각 단계에 우리가 개입하고 싶을 수 있기 때문이다. 각 과정에서 특정 작업을 하게 할 수 있다.

<br />

### ViewController 생성

`UIViewController`가 생성되면 생명주기가 시작된다. 우리가 만든 뷰 컨트롤러는 거의 항상 스토리보드에서 만들어진다. 그러니 생명주기는 앱이 켜지게 되면 어떤 곳으로 세그웨이 하는 것, 혹은 스토리보드의 첫번째 뷰 컨트롤러 등이 되겠지? iOS는 어떤 API를 가지고있는데 그 API가 뷰 컨트롤러를 건네주기도 한다. 그럼 우리는 세그웨이를 하든 원하는 것을 할 수 있다.

<br />

### 생성 후엔 어떻게 될까?

물론 생성된 후 가장 먼저 하는 일은 세그웨이 하도록 준비(prepare)하는 것이다. 세그웨이로 인해 화면에 보이게 되면 준비하는 단계가 실행된다. 그 다음에는 아울렛을 설정한다. 그 다음 뷰 컨트롤러가 화면에 나타나고 사라질 수도 있다. `splitView`의 세로 모드를 상상해보면 마스터는 사라질 수 있다. 밀어서 나타나게 했다가 사라지게 할 수도 있다.

이 모든 일들이 진행되는 동안 기하학적인 변동이 일어날 수도 있다. 가장 흔한 건 기기를 회전시키는 것이다. 원래는 길고 날씬한 직사각형이었다가 돌려서 짧고 널찍한 직사각형으로 바뀐다. 다른 이유로도 기하 변동이 일어날 수 있다. `splitView`를 생각해보면 마스터가 왼쪽에 길게 배치되어 있을 수도 있지만 어떤 때에는 왼쪽에 짧게 있을 수도 있고, 오른쪽에 있을 수도 있고, 가로모드일 경우 화면의 반만 차지할 수도 있다. 이렇게 뷰 컨트롤러의 상황에 따라서 기하학적인 변동이 일어난다.

그리고 메모리가 부족한 상황에서 뷰 컨트롤러에게 메모리를 좀 비우라고 요청할 수 있다.

<br /><br />

<br />

## View Controller Lifecycle 메소드

그럼 이런 일들이 일어날 때 뷰 컨트롤러에 보내는 메소드에 대해서 이야기해보자.

참고로 모든 메소드에는 항상 super를 앞에 붙여서 불러야 하는 것 잊지말자~!!!!! 슈퍼 클래스도 Appear와 Load되었는지 확인할 수 있게끔 해야한다.

<br />

<br />

### viewDidLoad

```swift
override func viewDidLoad() {
  super.viewDidLoad()
  ...
}
```

초기화하기에 딱 좋은 메소드다. 모든 것이 준비되어있고, 아울렛도 연결돼서 바로 시작할 수 있다. 거의 모든 초기화 작업을 여기서 한다. 하지만 한 가지 예외 사항이 있다. 바로 기하이다. (geometry-related setup XXX) `viewDidLoad`가 실행될 때 `bounds`는 아직 지정되지 않은 상태다. 그러니 화면의 크기와 관련된 것을 이 메소드 안에 넣으면 안된다. 아직 어떤 기기인지 확인하고 맞춰놓기 전이기 때문이다.
lifetime에서 단 한 번만 불리게 된다. 모든 것이 준비되고 아울렛이 설정되면 단 한 번만 호출한다.

<br /><br />

### viewWillAppear

```swift
override func viewWillAppear(_ animated: Bool) {
  super.viewWillAppear(animated)
  ...
}
```

뷰 컨트롤러의 뷰가 곧 화면에 나타날 것이다. 화면에 보이지 않던 시간 동안 발생한 일에 대해 여기서 따라잡을 수 있다. 만약 처음으로 화면에 나타나는 거라면 그때까지 진행된 사항을 이곳에서 따라잡을 수 있다.
이곳에서 모델의 모든 정보를 뷰에 로드한다. 특히 모델이 변할 수 있는 상태라면 더욱 중요하다. 예를 들어 모델이 네트워크 데이터베이스라면 다른 사람들이 계속해서 수정하고 데이터베이스는 계속 변경될 것이다. 바로 여기서 변경 사항을 최신으로 갱신한다. 왜냐면 뷰가 아직 화면에 나타나지도 않았는데 뷰 컨트롤러에게 일을 시키는 것은 좀 이상하기 때문이다. 차라리 아무것도 하지 않고 있다가 뷰가 나타나기 직전에 설정하는 게 더 자연스러울 것이다.
여러 번 호출할 수 있다. 왜냐면 뷰 컨트롤러는 언제든지 사라졌다 다시 돌아오는 것을 반복할 수 있기 때문에 화면에 다시 돌아올 때마다 호출될 수 있다.

<br /><br />

### viewDidAppear

```swift
override func viewDidAppear(_ animated: Bool) {
  super.viewDidAppear(animated)
  ...
}
```

화면에 나타났을 때 호출된다. 여기서는 무엇을 할까? 이미 화면에 표시된 후이기 때문에 뷰를 모델로부터 업데이트하기에는 이미 늦었다. 하지만 여기는 애니메이션 같은 것을 시작하기에 아주 좋은 곳이다. 왜냐하면 `viewWillAppear`에서는 아직 화면에 표시되기 전이기 때문에 이런 것을 할 수 없다. 화면에 표시되는 타이머를 시작한다던지, 무언가를 관찰하기 시작한다든지, 예를들면 GPS 위치나 핸드폰의 자이로 위치를 관찰할 수 있다. 이런 것은 화면에 표시된 후에 할 수 있다.

여기서 할 수 있는 다른 것은 **비용이 큰 작업**(시간, 배터리, 저장용량이 많이 드는 작업)을 시작하는 것이다. 이런 작업은 보통 `viewDidLoad`에서 하지 않는다. `viewDidLoad`가 진행중일 때 뷰 컨트롤러는 아직 화면에 표시되지 않았을 수도 있다. 뷰 컨트롤러가 화면에 반드시 나타날 거란 보장도 없다. 심지어 `viewWillAppear`를 호출했다 하더라도 화면에 반드시 표시된다고 할 수는 없다. `viewDidAppear`은 화면에 표시되었다는 것을 확실하게 알고 있으니 고비용의 작업을 하기 좋다.

그럼 고비용의 작업은 뭘까? 네트워크상에서 대용량 이미지를 가져오고 싶다고 해보자. 우선 네트워크 작업은 대부분 비용이 꽤 높다. 또 핸드폰은 셀룰러 데이터 연결만 가능할 수도 있기 때문에 신호가 좋지 않을 수 있다. 이미지를 거의 불러오지 못하는 상황일 수도 있다는 의미다. 이런 작업이 보통 고비용 작업이다. 같은 이유로 고비용 작업은 보통 백그라운드에서 실행한다. 이렇게 하는 이유는 **iOS 앱을 다룰 때 바로 사용자의 인터페이스 경험을 절대 방해하지 않아야 한다는 것**이 아주아주 중요하기 때문이다. 항상 사용자가 무언가를 터치하거나 스와이프할 수 있게 해야한다. 만약 무언가를 터치하거나 스와이프할 때 앱이 멈춘다면 절대 그 앱을 쓰지 않을 것이다. 이런 문제를 해결하는 방법은 오래 걸리는 작업, 예를 들어 네트워크에서 무언가를 기다리는 작업 같은 건 메인 큐가 아닌 다른 큐에서 실행하는 것이다. 이것을 메인 스레드에서 벗어난 백그라운드 프로세스라고 한다. `viewDidAppear`는 이런 작업을 하기 좋은 타이밍이다. 셀룰러 데이터 용량을 아직 나타날지 확실하지도 않은 이미지를 불러오는데 낭비하고 싶지 않을 것이다.

즉, 화면에 이미 UI가 나타났을 때 백그라운드에서 작업을 시작하고, 화면에 이미 나타난 UI는 백그라운드에서 돌고 있는 고비용의 작업이 아직 완료되지 않았더라도 다른 기능들이 항상 잘 작동하도록 설계해야 한다. 고비용의 작업은 몇십분이 걸릴 수도 있고, 네트워크 상황에 따라 끝나지 않을 수도 있다. 이럴 때에는 플레이스 홀더나 스피닝 휠 또는 '로딩중...' 같은 애니메이션으로 사용자에게 작업 중이라는 것을 알려주는 것이 좋다. 하지만 작업중이지만 UI는 완전히 반응적이어야 한다. `navigationController`의 경우 back 버튼을 눌러 고비용의 작업을 포기할 수 있게 해야하고 탭인 경우에는 아래의 다른 탭을 눌러 작업을 취소할 수 있게 해야한다. UI를 디자인한다는 것은 이런 것을 의미한다. 고민을 해봐야하는 것...!

<br /><br />

### viewWillDisappear

```swift
override func viewWillDisappear(_animated: Bool) {
  super.viewWillDisappear(animated)
  ...
}
```

뷰가 사라지기 직전에 이 메소드를 실행한다.
`viewDidAppear`에서 했던 것을 그대로 되돌리기에 딱 좋은 메소드다. 예를 들면 타이머를 시작했거나, 애니메이션을 시작했거나, GPS를 관찰하기 시작했다면 여기서 그것들을 멈추면 된다. 곧 사라질 것을 확실히 알고 있기 때문에 이 메소드에서 할 수 있다. 그러다가 화면에 다시 나타난다면 `viewDidAppear`에서 다시 설정하면 된다. 두 메소드가 연관돼서 작동한다고 보면 된다.

<br /><br />

### viewDidDisappear

```swift
override func viewDidDisappear(_ animated: Bool) {
  super.viewDidDisappear(animated)
  ...
}
```

뷰가 완전히 사라졌을 때 호출한다.
보통 여기서 많은 작업을 하지는 않는다. 여기서 MVC를 정리(clean up)하기 좋다. 아마 여러 가지 상태를 저장하기에 좋겠지? 하지만 자주 사용되지는 않는다.

<br /><br />

### Geometry(기하) - LayoutSubviews

위에 설명한 대로 기하학적인 부분은 `viewDidLoad`에서 설정할 수 없다. 그럼 어디서 설정해야 할까? `viewWillAppear`에서 하면 되지 않을까? 생각할 수 있다. 뷰가 나타나기 바로 직전이니까 기하를 설정해도 될 것 같지? 하지만 NO.......안된다! 물론 `viewDidAppear`에서 하면 더더욱 안되겠지? 이미 화면에 나타났으니까 넘 늦어버렸다. 그럼 어디서 하면 좋을까?

```swift
override func viewWillLayoutSubviews()
override func viewDidLayoutSubviews()
```

이 두 메소드는 컨트롤러로 보내진다. 컨트롤러의 최상위 뷰, 바로 `self.view` 다! 그 변수 `view`로 `layoutSubviews`가 보내지기 전후에 메소드를 호출한다.
왜 최상위 뷰에 `layoutSubviews`가 보내질까? 다른 뷰도 마찬가지다. 예를 들면 주로 서브 뷰가 들어왔다 나갔다 할 때 보내진다. 혹은 경계가 바뀔 때도 보내진다. 이곳에 기하 변경을 넣는 이유가 바로 이것 때문~!! 최상위 뷰의 `bounds`가 바뀔 때마다 호출되니 아주 딱맞는 위치다!
요즘에는 대체로 이 메소드를 구현할 필요가 없다. 왜냐면 `autolayout`을 이용하기 때문이지...! `autolayout`의 모서리 맞추기, 가로세로 축 맞추기, 비율 조정 등을 최상위 뷰의 `layoutSubviews`에서 자동으로 불러오기 때문이다. 그래서 컨트롤러에서 작업할 필요가 없다. 하지만 만약 컨트롤러에서 조정하고 싶다면 이 메소드에서 작업하면 된다.

<br />

위의 두 메소드를 호출할 때 주의해야할 점!!은 **해당 메소드가 여러 번 호출 될 수 있다는 것**이다. 가끔 예상하지 못한 곳에서 변화도 없고 서브 뷰에 관해 바뀐 점도 없는데도 호출될 때가 있을 수 있다. 서브 뷰의 레이아웃을 시스템이 관리할 수 있기 때문이다. 호출되면 어느 순간이든 서브 뷰의 레이아웃을 확실하게 알고 있게 된다. 뷰 뒤집기 애니메이션을 하기 위해 시작과 끝 단계의 레이아웃을 조정하기 위해 호출할 수도 있다. 경계는 바뀌지 않았지만 뷰가 잘 놓여있는지 확인하려고 호출할 수도 있다. 또는 목표 지점에서 확인할 수도 있고 애니메이션의 끝에서 확인할 수도 있어 이유는 중요하지 않다. 시스템이 원할 때마다 호출할 수 있는 메소드다. layoutSubviews는 아무때나 호출될 수 있으니 경계의 변화에 반응하도록 구현할 때 적절하고 효율적으로 작동하도록 해야한다. 똑같은 경계를 갖고 있더라도 두 번 연속으로 호출될 수도 있다. 따라서 매우 복잡하고 비용이 많이 드는 작업을 두 번 연속으로 하는 것은 좋지 않다.

<br /><br />

### Autorotation

기하의 특수 상황이다. 기기를 가로모드에서 세로모드로 혹은 그 반대로 돌릴 때 사용된다. 이런 경우엔 당연히 `layoutSubviews`가 호출된다. 경계가 바뀌었으니 `viewWill/DidLayoutSubviews`를 호출한다. 하지만 자유롭게 설정할 수 있는 애니메이션도 있다. iOS는 자동으로 모든 서브 뷰를 세로 `layoutSubviews`에서 가로 `layoutSubviews`로 애니메이션과 함께 전환시킨다.

하지만 어떤 상황에서는 전환 애니메이션을 바꾸고 싶을 수도 있다. 언제 그럴까? 아이폰 계산기 어플을 생각해보면 세로모드일 때 버튼 크기가 크고 계산용 버튼 개수가 많지 않다. 하지만 가로모드로 바꾸면 버튼 크기가 작아지고, 루트, 알고리즘 등 각종 버튼들이 생긴다. 화면이 회전하면 계산기 앱이 애니메이션에 개입하고 싶어한다. 왜냐하면 새로운 버튼이 화면에 나타나는 것을 애니메이션으로 표현해야 한다. 레이아웃도 변해야 한다. 이런 상황...!

그럼 이렇게 애니메이션을 커스텀하고싶을 때 어떤 메소드를 사용하면 좋을까? `viewWillTransition(to size, with coordinator)`를 사용하면 된다. `coordinator`는 애니메이터의 좌표다. `animate(alongsideTransition)` 메소드를 여기에 사용할 수 있다. 여기에 애니메이션 블록 클로져를 추가해 애니메이션을 직접 만들 수 있다. 그럼 그 애니메이션은 시스템의 회전 애니메이션과 함께 진행된다.

애니메이션을 실행하고 있는 중간에 기기를 회전시킨다면 애니메이션이 어떻게 될까? 화면 전체를 가로지르도록 뷰를 움직이고있었는데, 이 메소드가 같은 뷰의 같은 속성을 움직이기 시작하면 잘못된 위치에서 애니메이션이 시작될 수 있다. 새로운 곳으로 움직였는데 `AnimationBeginsFromCurrentState()`를 사용하게 된다면 완전히 잘못된 위치로 뷰가 이동할 수도 있다. 애니메이션을 잘 설정해 다른 것이 그것을 똑같이 바꾸려 하더라도 정확한 지점에 있을 수 있도록 해야한다. 약간의 조율이 필요하다. 애니메이션 조율 작업이라고 할 수 있다.

<br /><br />

### Low Memory

앱이 대용량 아이템을 사용할 경우에만 신경 쓰면 된다. 예를 들면 비디오, 이미지, 대용량 음악 파일 같은거...iOS 기기에서는 대용량 사진 작업 앱이 많을 수도 있다. 이런 앱때문에 메모리가 부족할 수도 있다. 요즘 최신 기기는 용량이 커서 부족할 일이 거의 없다. 하지만 메모리 누수가 심한 앱...대용량 이미지 메모리가 누수된다면 충분히 가능하다.

```swift
override func didReceiveMemoryWarning() {
  super.didReceiveMemoryWarning()
  ...
}
```

여기서 뷰 컨트롤러에게 나중에 다시 필요할 때 다시 쉽게 할당할 수 있는 메모리는 힙에서 해제하라고 요청한다. 메모리 청소 같은 느낌~!
앱이 메모리 누수가 심해 앱 용량이 점점 증가해 이 단계로 넘어가게 되었는데 코드를 제대로 짜지 못해 누수를 처리할 수 없어 메모리 누수가 지속된다면 iOS가 앱을 강제종료 시킬 수 있다. 대용량 메모리를 사용하고 메모리 누수가 심한 앱은 강제종료 당할 수도 있으니 주의.

<br /><br />

### Waking up from an storyboard

엄격히 말하자면 뷰 컨트롤러 생명주기의 일부는 아니지만 스토리보드에서 나온 모든 객체 모든 UI 뷰와 뷰 컨트롤러는 awakeFromNib()로 호출되기 때문에 추가했다.

```swift
override func awakeFromNib() {
  super.awakeFromNib()
  ...
}
```

맨 처음 단계인 초기화 다음, 세그웨이 준비 전 아울렛 연결 전에 호출된다.
보통 awakeFromNib에는 아무것도 적지 않는다. 우선은 다른 부분에서 시도해보고 안되면...정말 일찍 무언가를 해야 하는 경우에만 여기에 구현하는 것을 추천한답니다...
스토리보드에서 나온 MVC에만 적용된다. 스토리보드에서 나온 것은 거의 우리 마음대로 할 수 있다. 코드로 만들어진 카메라 같은 건 시스템이 관리한다.

<br /><br /><br />

## 📖 정리

1. **Instantiated**

먼저 인스턴스화한다. 보통 스토리보드에서 한다. 가끔 iOS로부터 뷰 컨트롤러를 요청할 수 있다.

2. **awakeFromNib**

스토리보드에서 시작할 때만 해당된다.

3. **Segue preparation happens**

세그웨이 준비 단계이다. 다른 MVC가 이것을 준비한다. 참고로 아울렛은 아직이다.

4. **Outlets get set**

아울렛이 설정된다. iOS가 연결한다.

5. **viewDidLoad**
6. **viewWillAppear - viewDidAppear** / **viewWillDisappear - viewDidDisappear**

화면에 나타나거나 사라진다.

7. *Geometry changed* -> **viewWillLayoutSubviews - viewDidLayoutSubviews**

화면이 변화하면 호출된다. autolayout이 그 사이에 호출될 수도 있다. LayoutSubviews가 autolayout이 나타나도록 한다.

8. *memory gets low* -> **didReceiveMemoryWarning**

앱의 메모리 사용이 과해지거나 기기 전체가 메모리를 과하게 사용할 때 호출된다. 여기서 메모리를 정리하지만 이게 발생할만큼 대용량을 쓰는 경우는 많지 않다.