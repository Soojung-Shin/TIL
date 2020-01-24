# Selection Sort(선택 정렬)

선택 정렬은 버블 정렬의 기본적인 아이디어를 따르지만 값 교환 동작의 수를 줄여 시간을 개선한다. 선택 정렬은 한 번의 패스의 마지막에서 한 번만 값 교환이 이루어진다. 

<br />

`[9, 4, 10, 3]` 인 배열이 있다고 생각해보자. 각각의 패스동안 선택 정렬은 가장 작은 정렬되지 않은 값을 찾아 값을 교환한다.

1. 먼저, 가장 작은 값인 `3`을 찾는다. `9`와 위치를 바꾼다.

   ➔ `[3, 4, 10, 9]`

2. 그 다음 가장 작은 값인 `4`를 찾는다. `4`는 이미 맞는 위치에 있으므로 넘어간다.

   ➔ `[3, 4, 10, 9]`

3. 마지막으로 `9`와 `10`의 위치를 바꾼다.

   ➔ `[3, 4, 9, 10]`

<br /><br />

### 구현

```swift
public func selectionSort<Element>(_ array: inout [Element]) where Element: Comparable {
  guard array.count >= 2 else {
    return
  }
  
  //컬렉션의 마지막 하나를 제외하고 모든 요소마다 한 번의 패스를 수행한다.
  //모든 요소들이 정렬되었다면 마지막 하나도 정렬된 위치에 있을 것이다.
  for current in 0 ..< (array.count - 1) {
    var lowest = current
    
    //각 패스마다 컬렉션을 순회해 가장 작은 값을 찾아낸다.
    for other in (current + 1) ..< array.count {
      if array[lowest] > array[other] {
        lowest = other
      }
    }
    
    //만약 현재 인덱스 값이 최솟값이 아니라면 위치를 바꾼다.
    if lowest != current {
      array.swapAt(lowest, current)
    }
  }
}
```

<br />

버블 정렬과 마찬가지로 선택 정렬의 최선, 최악, 평균 시간 복잡도는 `O(n^2)` 이다. 간단하지만 `swapAt` 작업을 줄였기 때문에 버블 정렬보다 성능이 우수하다!

<br />

<br />

### 일반화

선택 정렬을 배열이 아닌 컬렉션에서도 이용할 수 있도록 일반화 시켜보자!

선택 정렬은 앞에서 뒤로만 순회하기 때문에 어떤 컬렉션에서도 사용할 수 있다. 하지만 정렬하는 과정에서 위치를 바꾸는 작업이 필요하기 때문에 `MutableCollection(가변 컬렉션)` 프로토콜을 준수해야 한다.

<br />

```swift
public func selectionSort<T>(_ collection: inout T)
	where T: MutableCollection, T.Element: Comparable {
  
  guard collection.count >= 2 else {
    return
  }
    
  for current in collection.indices {
    var lowest = current
    var other = collection.index(after: current)
    
    //컬렉션을 순회하면서 최솟값의 인덱스를 찾아낸다.
    while other < collection.endIndex {
      if collection[lowest] > collection[other] {
        lowest = other
      }
      other = collection.index(after: other)
    }
    
    //현재 인덱스가 최솟값이 아니라면 최솟값과 위치를 바꾼다.
    if lowest != current {
      collection.swapAt(lowest, current)
    }
  }
}
```

알고리즘의 동작은 동일하다. 컬렉션의 인덱스를 사용하도록 변경하기만 하면 된다.

