# String

String 구조체와 별개로 Character 구조체가 존재한다. String은 Character들의 Sequence로 이루어진게 아니고 유니코드로 되어있다. 유니코드란 바이트 단위의 데이터로 거의 모든 언어를 나타낼 수 있는 것이다. 아스키처럼 국제적으로 통용되는 것인데 아스키는 영어만 표현할 수 있는 반면 유니코드는 세계 모든 언어를 표현할 수 있다.

그래서 이 유니코드와 캐릭터를 연결(bridge)해주어야 한다. 만약 cafe에 라는 단어가 있으면, 우리는 4개의 문자를 인식할 수 있다(c, a, f, é). 하지만 유니코드로는 5개로 표현된다(c, a, f, e, ´). 이런 차이로 생기는 가장 큰 문제는 문자열을 정수로 색인할 수 없다는 것이다. 그래서 문자열을 정수로 색인하지 않고, 대신 특수한 타입`String.Index`으로 문자열을 색인해야한다.

<br />

우선 스트링의 인덱스를 받아와야 한다. `startIndex`, `endIndex`, `index(of:)`를 사용해서 문자의 첫 인덱스를 찾을 수도 있다. 스트링의 인덱스를 얻는 여러 메소드와 변수들이 있다.

일단 인덱스를 받게 되면 정수로 상쇄될 수 있다. 문자열의 네 번째 문자를 받기 위해서 `startIndex`를 받고나서 `offsetBy: 3`으로 네 번째 문자를 얻을 수 있다.

<br />

```swift
let pizzaJoint = "café pesto"
let firstCharacterIndex = pizzaJoint.startIndex
//해당 인덱스의 타입은 Int가 아닌 String.Index
let fourthCharacterIndex = pizzaJoint.index(firstCharacterIndex, offsetBy: 3)
let fourthCharacter = pizzaJoint[fourthCharacterIndex] //é

if let firstSpace = pizzaJoint.index(of: " ") {
  //만약 " "이 없다면 nil을 리턴한다.
  let secondWordIndex = pizzaJoint.index(firstSpace, offsetBy:1)
  //사실 이 메소드는 스트링의 메소드가 아니다...콜렉션 프로토콜의 메소드다ㅎㅎ
  //스트링이 이것을 구현했을 수도 있고, 아닐수도 있다.
  //콜렉션 프로토콜 역시 제네릭이라서 콜렉션의 인덱스는 구성 가능하다. 스트링의 경우 String.Index가 된 것.
  let secondWord = pizzaJoint[secondWordIndex ..< pizzaJoint.endIndex]
  //String.Index를 Range로 만들어 사용한다.
  //Range는 제네릭 타입이라서 꼭 정수형일 필요가 없다.(실수형도 가능) 
  secondWord = pizzaJoint.components(separatedBy: " ")[1]
  //Collection의 메소드. Collection에 들어가게 될 요소를 제공하면 지정해준 것으로 분리된 요소의 배열을 생성한다.
  //separatedBy: " "로 하면 공백으로 분리된 모든 단어를 가지고 있겠지? 배열이 비어있을 수 있으니 조심!
}
```

<br />

위의 것들은 인덱스를 이용하는 것인데 대부분의 경우에는 스트링에서 더 높은 단계의 것을 사용한다(`Collection`...). 스트링은 캐릭터의 컬렉션이다. 모두 컬렉션에서 온 것이다. 컬렉션은 시퀀스여서 `for c in s`처럼 이용할 수 있다.

<br />

또 시퀀스를 인자로 받는 배열 생성자도 있다. 어떤 시퀀스의 배열은 그 시퀀스를 살펴보고 요소 하나하나를 배열 안에 넣는다. 그렇게 큰 배열 하나를 만든다. 그럼 배열 안에는 시퀀스의 모든 요소가 들어가 있을 것이다. 그리고 스트링은 문자의 시퀀스이기 때문에 스트링의 배열은 곧 그 캐릭터의 배열이 된다. 그럼 얼마든지 정수로 인덱스에 접근할 수 있게 된다! (스트링을 정수 인덱스로 접근하는 하나의 팁)

```swift
for c in s {} //스트링의 모든 캐릭터를 반복
let characterArray = Array(s)
```

<br />

스트링은 값 타입이고, 구조체이다. `let s = ...` 으로 사용한다(immutable string).

물론 var를 사용해 가변변수로도 사용할 수 있다. `Range Replaceable Collection`이라는 프로토콜이 있는데 가변성이 있는 컬렉션이다. 어떤 것의 범위가 대체될 수 있다는 것이다. `insert(contentsOf: ..., at: index)` 메소드로 단어를 삽입할 수 있다. 불변 변수로 설정한 뒤 + 로 더할 수도 있다.

<br />

### 스트링의 다른 메소드들

```swift
func hasPrefix(String) -> Bool
func hasSuffix(String) -> Bool
var localizedCapitalized/Lowercase/Uppercase: String
func replaceSubrange(Range<String.Index, with: Collection of Character)

s.replaceSubrange(..<s.endIndex, with: "new contents")
//여기서 ..< 앞에 값이 없는데 범위는 어디서부터 시작할까?
//-> 스위프트가 이것이 스트링 인덱스의 범위인걸로 알고(타입 유추) 자동으로 시작 인덱스를 넣는다.
//..< 뒤의 값을 생략하면 마지막 인덱스를 넣는다.

func remove(at: String.Index)
//해당 함수는 Range Replaceable Collection에 있지만 제네릭 타입이기 때문에 정수형이 아닌 String.Index로 인자를 받는다. 컬렉션은 제네릭타입~!!
//index(s.startIndex, offsetBy: ...) 이런식으로 몇번 째 인덱스인지 계산해준 후 넣어주어야 한다.
```



