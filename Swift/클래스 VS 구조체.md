# 클래스 VS 구조체

<br />

<p align="center">

|    🤔적용 가능?     |     클래스     |   구조체   |
| :----------------: | :------------: | :--------: |
|      **Type**      | Reference Type | Value Type |
|      **ARC**       |       O        |     X      |
|    **프로토콜**    |       O        |     O      |
|      **상속**      |       O        |     X      |
|    **익스텐션**    |       O        |     O      |
|  **이니셜라이저**  |       O        |     O      |
| **디이니셜라이저** |       O        |     X      |
|  **타입 캐스팅**   |       O        |     X      |
|  **서브스크립트**  |       O        |     O      |

</p>

<br />

<br />

## Value 타입 VS Reference 타입

Value 타입과 Reference 타입의 가장 큰 차이점은 '무엇이 전달되는가'이다. Value 타입은 전달될 값이 복사되어 전달되고, Reference 타입은 값을 복사하지 않고 참조(주소)가 전달된다.

함수의 전달인자로 전달하거나 변수에 할당할 때, Value 타입의 데이터는 메모리에 전달인자를 위한 인스턴스가 새로 생성된다. 그리고 생성된 새 인스턴스에는 전달하려는 값이 복사되어 들어간다.

Reference 타입의 데이터는 기존 인스턴스의 참조를 전달하므로 새로운 인스턴스가 아닌 기존의 인스턴스 참조만 전달한다. 

<br />

<br />

```swift
struct ValueType {
    var str = "Value 타입 입니다."

    init() {
        print(self.str)
    }
}

class ReferenceType {
    var str = "Reference 타입 입니다."

    init() {
        print(self.str)
    }
}


var value1 = ValueType()		//Value 타입 입니다.
var value2 = value1
value1.str = "과연 value2의 str도 변경될까요?"
print(value2.str)		//Value 타입 입니다.

var reference1 = ReferenceType()		//Reference 타입 입니다.
var reference2 = reference1
reference1.str = "과연 reference2의 str도 변경될까요?"
print(reference2.str)		//과연 reference2의 str도 변경될까요?
```

구조체는 값이 복사되기 때문에 `value1`과 `value2`는 각각 다른 인스턴스를 가지고 있다. 그래서 `value1` 인스턴스의 값을 변경해도 `value2`의 값에는 영향을 주지 않는다.

클래스는 참조가 전달되었기 때문에 `reference1`과 `reference2`는 같은 주소의 인스턴스를 공유하고 있는 셈이다. 그래서 `reference1`의 인스턴스 변수의 값을 변경하면 `reference2`도 함께 변경되었다.

<br />

<br />

### 두 클래스 인스턴스의 참조가 같은지 확인하기

`===` 식별 연산자를 사용한다.

```swift
var reference1 = ReferenceType()
var reference2 = reference1
var reference3 = ReferenceType()

print(reference1 === reference2)
print(reference1 === reference3)
print(reference2 === reference3)
```







