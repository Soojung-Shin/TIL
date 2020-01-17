# Any, AnyObject

어떤 타입도 될 수 있는 타입. 옵젝씨와 호환을 위해 존재한다. 옵젝씨에 id라는 타입이 있는데(Any와 같은 의미) 거의 모든 API에 사용된다. 하지만 스위프트는 Strong type 언어이기 때문에 사용하지 되도록 사용하지 않는 것이 좋다~!! 하위호환 API를 사용할 때는 이용하지만 그 외의 데이터 구조에서는 사용하지 않는다.

<br />

<br />

# Type Casting

Any 타입 변수는 구조체, 열거형, 클래스 등 다양한 타입이 될 수 있다.

문제는 Any 타입에게 메세지를 보낼 수 없다는 것이다. 코드에서는 그 변수가 뭔지 알 수 없기 때문이다.

```swift
let attributes: [NSAttributedString.Key : Any] = ...

func prepare(for segue: UIStroryboardSegue, sender: Any?)
//UIViewController 메소드. MVC에서 다른 MVC로 이동할 때 사용된다.
//segueWay : 한 MVC에서 다른 것으로의 변환을 의미. 변환이 일어날 때 뷰 컨트롤러에서 호출된다.
//sender : 어떤 버튼이 이 MVC를 새로운 MVC로 변환시키는지를 의미. 버튼, 테이블뷰 cell..., 코드에 의해 일어나면 nil
```

<br />따라서 Any는 반드시 내가 아는 어떤 값으로 변경해주어야 한다. 타입을 변경해주기 위해서  `as` 를 사용한다. 영어처럼 읽을 수 있다는 것이 장점이다.

```swift
as?
//Any를 명시한 타입으로 변환하고, 불가능할 경우 nil을 리턴한다.

let unknown: Any = ...
if let foo = unknown as? MyType {
  //여기서는 foo에게 MyType이 알아들을 수 있는 메세지를 보낼 수 있다.
  //만약 unknwon이 MyType이 아니라면 이 괄호 안의 코드는 실행되지 않는다.
}
```

<br />

<br />

`as` 를 이용한 타입 캐스트는 Any가 아닌 다른 타입에서도 사용 가능하다. 대표적인 예시로 어떤 변수 타입을 서브클래스 중 하나로 바꿔줄 수 있다.

```swift
let vc: UIViewController = ConcentrationViewController()
//ConcentrationViewController는 UIViewController의 서브 클래스다.(상속받은 것)
//ConcentrationViewController를 대입했지만 여전히 vc의 타입은 UIViewController다. ConcentrationViewController가 아니다...!!

vc.flipCard() //에러
//ConcentrationViewController의 메소드인 flipCard()를 vc에 보내는 작업은 할 수 없다.
//왜냐면 vc는 UIViewController이고, UIViewController에는 flipCard 메소드가 없음 ㅠㅠ

if let cvc = vc as? ConcentrationViewController {
  cvc.flipCard() //가능!
  //서브클래스로 형이 바뀌었다 = 하위 형변환(DownCasted)
  //이런 방식으로 서브클래스와 상호작용할 수 있다.
}
```

<br /><br />

### as? VS as!

```swift
as? //타입 캐스팅을 할 수 없다면 nil을 반환한다.
as! //강제 타입 캐스팅이다. 강제로 타입 캐스팅을 했기 때문에, 타입캐스팅을 할 수 없다면 앱이 죽어버린다.
```

