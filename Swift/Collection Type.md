> inflearn의 야곰님의 swift기초 강의를 바탕으로 작성하였습니다.

# Collection Type

여러 값들을 묶어서 하나의 변수로 표현

<br>



Array
------

순서가 있는 리스트 컬렉션 타입


### 선언과 생성

```swift
var, let 변수명: Array<DataType> = Array<DataType>()
```


```swift
var integers: Array<Int> = Array<Int>()
var doubles: Array<Double> = [Double]() // Literal 표현으로 생성
var strings: [String] = [String]() // Type에도 Literal 표현으로 넣을 수 있음
var characters: [Character] = [] // 빈 Array 생성

let immutable Array = [1, 2, 3] // 생성할 때 값을 넣어줌(let을 사용해서 선언하면 값 변경 불가능)
```




### 요소 접근

```Swift
arrayName[index]
```

index 번째 값 반환

index는 0부터 시작



```swift
immutable[0] //1 (0번째 값)
integers[0] // 에러 - 비어있는 Array
```



### 요소 추가

```swift
arrayName.append(value)
```

valuer값을 가진 요소를 해당 Array 뒤에 추가



```swift
integers.append(1)
integers.append(2)
integers.append(3)

integers[0] // 1 (0번째 값에 접근)
integers.append(1.23) // 에러
```





### 요소 검사

```swift
arrayName.contains(value) // true or false
```

value값이 해당 Array에 들어있는지 확인



```Swift
integers.contains(1) // true
integers.contains(5) // false
```





### 요소 삭제

```Swift
arrayName.remove(at: index)
```

index 번째 있는 값을 삭제 후 반환

```swift
arrayName.removeLast()
```

맨 마지막 값 삭제 후 반환

```swift
array.removeAll() // []
```

해당 Array 안의 모든 변수 삭제 후 반환



```Swift
integers.remove(at: 1) // 2
integers.removeLast() // 3
integers.removeAll() // []
```





### 사이즈

```swift
arrayName.count
```

해당 Array의 현재 사이즈 반환



```swift
integers.count // 0
```

<br>



Dictionary
------------------

키와 값의 쌍으로 이루어진 컬렉션 타입 (HashMap과 유사)



### 선언과 생성

```swift
var, let 변수명: Dictionary<Key DataType, Value DataType> = Dictionary<Key DataType, Value DataType>()
```



```swift
var anyDictionary: Dictionary<String, Any> = [String: Any]() // Literal 표현 가능
let emptyDictionary: [String: String] = [:] //빈 Dictionary 생성
let initializedDictionary: [String: String] = ["name": "soojung", "nation": "korea"]
```





### 요소 추가

```swift
anyDictionary["someKey"] = "value"
anyDictionary["someKey"] = "dictionary"
anyDictionary["anotherKey"]= 100

anyDictionary	// ["someKey": "dictionary", "anotherKey": 100]
```





### 요소 삭제

```swift
dictionaryName.removeValue(forKey: KeyValue)
```

해당 key값을 가진 요소 삭제 후  value 반환

```swift
dictionaryName[keyValue] = nil
```

이렇게도 사용 가능



```swift
anyDictionary.removeValue(forKey: "anotherKey") // 100
// "anotherKey"라는 Key를 가진 요소 삭제 후 Value 값 100 반환
anyDictionary["someKey"] = nil // nil

anyDictionary // [:]
```





### 주의!

```swift
let someValue: String = initializedDictionary["name"] //에러
```

Dictionary의 해당 key 값을 가진 요소의 value가 있을 수도 있고 없을 수도 있음 (nil 가능)

따라서 상수에 값을 넣어줄 수 없음

<br>



Set
------

순서가 없고, 멤버가 유일한 컬렉션 타입



### 선언과 생성

```swift
var 변수명: Set<DataType> = Set<DataType>()
```

축약형 없음



```swift
var integerSet: Set<Int> = Set<Int>() //Set([])
```





### 요소 추가

```swift
변수명.insert(value)
```



```swift
integerSet.insert(1) // inserted true, memberAfterInsert 1
integerSet.insert(2) // inserted true, memberAfterInsert 2
integerSet.insert(3) // inserted true, memberAfterInsert 3
integerSet.insert(3) // inserted false, memberAfterInsert 3
integerSet.insert(3) // inserted false, memberAfterInsert 3
```

Set은 멤버가 유일한 컬렉션 타입이기 때문에 이미 가지고있는 값이라면 추가되지 않음





### 요소 검사

```swift
setName.contains(value) // true or false
```

해당 value값을 가지고 있는지 검사



```swift
integerSet.contains(1) //true
integerSet.contains(100) //false
```





### 요소 삭제

```swift
setName.remove(value)
```

value값을 가진 요소 삭제



```swift
setName.removeFirst()
```

해당 set의 첫번째 요소 삭제





### 집합 연산

- **Union**

```swift
setA.union(setB)
```

두 set의 **합집합**을 구해줌



```swift
let setA: Set<Int> = [1, 2, 3, 4, 5]
let setB: Set<Int> = [3, 4, 5, 6, 7]

let union: Set<Int> = setA.union(setB) // {2, 4, 5, 6, 7, 3, 1}
```

Set은 순서가 없는 컬렉션 타입이기 때문에 정렬되지 않은 값이 들어감



- **Intersection**

```swift
setA.intersection(setB)
```

두 set의 **교집합**을 구해줌



```swift
let setA: Set<Int> = [1, 2, 3, 4, 5]
let setB: Set<Int> = [3, 4, 5, 6, 7]

let union: Set<Int> = setA.intersection(setB) // {5, 3, 4}
```



- **Subtracting**

```swift
setA.subtracting(setB)
```

두 set의 **차집합**을 구해줌

setA에서 setB의 요소들을 뺀 집합 (setA - setB)



```swift
let setA: Set<Int> = [1, 2, 3, 4, 5]
let setB: Set<Int> = [3, 4, 5, 6, 7]

let union: Set<Int> = setA.intersection(setB) // {2, 1}
```





### 정렬

```swift
setName.sorted()
```

value 오름차순으로 정렬



```swift
let sortedUnion: [Int] = union.sorted() // [1, 2, 3, 4, 5, 6, 7]
```

