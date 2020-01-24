# MutableCollection Protocol (가변 컬렉션 프로토콜)

### Declaration

```swift
protocol MutableCollection where Self.SubSequence : MutableCollection
```

<br />

 `MutableCollection` 프로토콜을 준수하는 컬렉션은 요소를 변경할 수 있다. `Subscript` 할당을 지원하는 컬렉션이다.

<br />

아래 예시를 보자. 학생 배열에서 이름 하나를 수정한다.

```swift
var students = ["Ben", "Ivy", "Jordell", "Maxime"]
if let i = students.firstIndex(of: "Maxime") {
    students[i] = "Max"
}
print(students)
//["Ben", "Ivy", "Jordell", "Max"]
```

<br />

하나의 값을 변경하는 것 뿐만 아니라 해당 컬렉션의 일부분(slice)의 값들을 변경할 수도 있다.

예를 들어, 서브스크립트로 표현된 서브 시퀀스에서 `sort()` 메소드를 사용해서 가변 컬렉션의 일부분을 정렬할 수 있다.

```swift
var numbers = [15, 40, 10, 30, 60, 25, 5, 100]
numbers[0..<4].sort()
print(numbers)
// Prints "[10, 15, 30, 40, 60, 25, 5, 100]"
```

<br />

`MutableCollection` 프로토콜은 컬렉션 자신의 길이가 아닌 컬렉션의 값을 변경하는 것을 허용한다. 요소를 더하거나 제거하는 작업에 대해서는 [`RangeReplaceableCollection` 프로토콜](https://developer.apple.com/documentation/swift/rangereplaceablecollection)을 준수해야 한다.

<br /><br />

### MutableCollection 프로토콜 준수 조건

커스텀 컬렉션에서 `MutableCollection` 프로토콜을 준수하기 위해서는 read와 write를 모두 지원하도록 해당 타입의 서브스크립트를 업그레이드 해야한다.

가변 컬렉션의 인스턴스의 서브스크립트에 저장된 값은 동일한 위치에서 접근 가능해야한다. 즉, 가변 컬렉션인 `a`, 인덱스 `i` 와 값 `x` 에 대해서 아래 두 개의 할당 값은 반드시 동일한 값이어야 한다.

<br />

```swift
a[i] = x
let y = a[i]

// Must be equivalent to:
a[i] = x
let y = x
```

<br />

<br />

-----

https://developer.apple.com/documentation/swift/mutablecollection