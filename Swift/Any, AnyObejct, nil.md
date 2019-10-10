> inflearn의 야곰님의 swift기초 강의를 바탕으로 작성하였습니다.

# Any, AnyObject, nil
<br>

  
Any
---

스위프트의 기본 데이터 타입(Int, String, Bool...)은 구조체로 되어있음

어떤 구조체 타입의 변수도 저장할 수 있음

한 번 초기화한 후에도 Any 타입에 맞는 자료형이라면 들어갈 수 있음

```swift
  val valueAny: Any = "SooJung"
  valueAny = 1234
  valueAny = 123.4
``` 
<br>

  
  
  
AnyObject 
---------

클래스 타입인 변수 저장

클래스 타입이라면 어떠한 값도 들어올 수 있음

```swift
val valueAnyObject: AnyObject
class someClass {}

valueAnyObject = someClass()
valueAnyObject = 123.4 //에러
valueAnyObject = valueAny //에러
```
<br>



nil
---

'없음', 빈 값(= null)을 의미

그러나 Any, AnyObject에는 들어갈 수 없음

Optional에서 이용

```swift
valueAny = nil //에러
valueAnyObject = nil //에러
```
