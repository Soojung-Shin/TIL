# NSAttributedString(속성 문자열)

스트링의 모든 문자가 각각 딕셔너리를 가지고있는 문자열이다. 딕셔너리의 여러 키와 값들이 그 문자를 어떻게 화면에 표현할지 나타낸다. 그 딕셔너리는 폰트나 색깔 등등 여러가지가 될 수 있다. 즉, 각각의 캐릭터가 속성을 가지고있는 스트링!

각 문자가 가질 수 있는 딕셔너리는 키는 문서에서 쉽게 알 수 있고, 값은 그것의 타입과 관련이 있다. 때로는 값이 UIFont, UIColor, Double...

대체로 여러 문자의 범위 내에서 하나의 딕셔너리를 사용한다. 만약 사용자가 화면에 글자를 선택해서 주황색을 만드려면 모든 문자들이 하나의 딕셔너리를 가지면 된다. 각각의 문자들이 다른 딕셔너리를 가질 필요는 없다.

속성 문자열로 여러 폰트나 문자의 색깔 등을 가지면 UILabel의 글자를 설정하거나, UIButton의 타이틀을 설정하는 등에 사용할 수 있다.

<br />

```swift
let attributes: [NSAttributedString.Key : Any] = {
  //여기에서는 타입 추론을 할 수 없다. 값이 Any로 지정되어있기 때문!
  //그러니까 여러 속성들을 선언할 때 각 문자에 해당하는 딕셔너리에는 타입을 꼭 지정해주어야 한다!
  //여기서는 옵젝씨를 가져오느라 Any를 사용했지만 스위프트에서는 절대!!! Any 사용하는 것이 좋은 것이 아니다. enum의 associated value 같은 것을 사용해서 Any를 처리해주자.
  //타입은 아래 문서에 가면 볼 수 있음 폰트, 밑줄, 취소선....
  
  .strokeColor: UIColor.oragne,
  //strokeColor = 문자의 테두리 색깔, foregroundColor = 문자의 안쪽 색깔, backgroundColor = 문자의 배경색
  .strokeWidth: 5.0
  //양수일 때는 겉을 칠하게 되고, 음수라면 꽉 찬 색이 된다.
}

let attribtext = NSAttributedString(string: "Flips: 0", attributes: attributes)
//속성 문자열 생성하고 각 문자에 딕셔너리를 주는 것을 NSAttributedString 생성자로 스트링과 그 속성을 받기만 하면 된다.
flipCountLabel. attributedText = attribtext
//지금 가지고 있는 문자열은 양수의 strokeWidth를 가지기 때문에 윤곽있는 글자가 될 것이다.
```

[NSAttributedString 문서](https://developer.apple.com/documentation/foundation/nsattributedstring)

<br />

#### NS?

좀 오래된 API. 이건 스트링이 아닌 완전히 다른 하나의 클래스이다. 그래서 스트링과는 다르게 작동한다. 하지만 구조체가 아닌 클래스이기 때문에 var를 이용해서 가변 변수를 만들수는 없다. 다른 클래스를 사용해야 한다. 바로 가변 속성 문자열이다. 특정 문자에 해당하는 딕셔너리를 설정하기 위해 가변 속성 문자열을 사용해야한다. 완전히 다른 클래스다.

또 NSAttributedString은 NSString으로 만들어진 것이다. NSString은 오래된 옵젝씨의 문자열인데 일반 스트링과는 조금 다른 유니코드와 코드를 가지고 있다. 그래서 이모티콘이나 악센트e같은 좀 까다로운 문자가 있을 때는 인덱스가 서로 맞지 않을 수 있다. String과 NSString 사이에는 자동적인 연결이 있어서 NSString을 받는 iOS API 일반 스트링을 넣어도 된다. 하지만 예외적으로 이 인코딩 작업이 잘 되지 않을 때는 인덱스 작업에 문제가 있을 수 있으니 조심해야한다.

