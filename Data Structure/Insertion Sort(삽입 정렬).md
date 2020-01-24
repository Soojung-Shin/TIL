# Insertion Sort(삽입 정렬)

버블 정렬이나 선택 정렬처럼 삽입 정렬은 평균적으로 `O(n^2)` 의 시간 복잡도를 가지지만 삽입 정렬의 성능은 달라질 수 있다. 데이터들이 많이 정렬되어 있을 수록 해야할 작업이 줄어든다. 삽입 정렬은 이미 데이터가 정렬되어 있을 때, `O(n)`의 시간 복잡도를 가진다.

Swift Standard Library의 sort 알고리즘은 작은(20개 미만) 정렬되지 않은 파티션에 대해서 삽입 정렬을 통한 하이브리드 정렬 방식을 사용한다.

<br />

삽입 정렬의 아이디어는 손으로 카드를 정렬할 때와 비슷하다. 삽입 정렬은 앞쪽부터 뒤로 컬렉션을 순회하면서, 각 값을 맞는 위치에 올 때까지 왼쪽으로 옮긴다.

`[9, 4, 10, 3]` 인 배열이 있다고 생각해보자.

1. 비교할 수 있는 앞의 값이 없기 때문에 첫 번째 값은 무시한다.

   ➔ `[9, 4, 10, 3]`

2. 인덱스를 이동시켜 `4` 와 `9` 를 비교한다. `9 > 4` 이기 때문에, `4` 를 `9` 와 위치를 바꿔 왼쪽으로 이동시킨다.

   ➔ `[4, 9, 10, 3]`

3. 인덱스를 이동시켜 `10` 과 `9` 를 비교한다. `10 > 9` 이기 때문에 위치를 바꿀 필요가 없다.

   ➔ `[4, 9, 10, 3]`

4. 마지막으로 `3`을 비교한다. 맞는 위치에 갈 때까지 값을 비교하면서 위치 이동을 반복한다.

   ➔ `[4, 9, 3, 10]`

   ➔ `[4, 3, 9, 10]`

   ➔ `[3, 4, 9, 10]`

<br />

<br />

### 구현

```swift
public func insertionSort<Element> (_ array: inout [Element]) where Element: Comparable {
  guard array.count >= 2 else {
    return
  }
  
  //삽입 정렬은 컬렉션을 한 번만 순회한다.
  for current in 1 ..< array.count {
    
    //값이 앞으로 이동해야하기 때문에 current 인덱스에서부터 거꾸로 순회한다.
    for shifting in (1 ... current).reversed() {
      
      //값이 맞는 위치에 올 때까지 위치 교환을 반복하여 앞으로 보낸다.
      if array[shifting] < array[shifting - 1] {
        array.swapAt(shifting, shifting - 1)
      } else {
        //맞는 위치에 왔다면 for문을 종료하고 current의 다음 인덱스로 간다.
        break
      }
    }
  }
}
```

<br />

삽입 정렬은 값이 이미 정렬되어있을 때, 가장 빠른 정렬 알고리즘 중 하나다. 당연한 말 같지만, 이 점은 모든 정렬 알고리즘에 적용되는 것은 아니다. 예를 들어 많은 데이터들이 있고 이들을 정렬해야할 때, 데이터들이 대체로 정렬되어있는 상태라면 삽입 정렬이 효율적일 것이다.

<br />

<br />

### 일반화

삽입 정렬을 배열이 아닌 컬렉션에서도 이용할 수 있도록 일반화 시켜보자!

삽입 정렬은 요소를 이동시킬 때 컬렉션의 앞에서 뒤쪽으로 이동한다. 따라서 컬렉션은 반드시 `MutableCollection(가변 컬렉션)` 프로토콜과  `BidirectionalCollection(양방향 컬렉션)` 프로토콜을 만족해야 한다.

<br />

```swift
public func insertionSort<T>(_ collection: inout T)
	where T: BidirectionalCollection & MutableCollection,
				T.Element: Comparable {
    
  guard collection.count >= 2 else {
    return
  }
          
  for current in collection.indices {
    var shifting = current
    
    while shifting > collection.startIndex {
      //shifting의 앞에 있는 값을 가져온다.
      let previous = collection.index(before: shifting)
      
      if collection[shifting] < collection[previous] {
        collection.swapAt(shifting, previous)
      } else {
        break
      }
      
      //shifting의 위치가 앞으로 이동되었다면, 해당 위치를 저장하고 위 과정을 반복한다.
      shifting = previous
    }
  }
}
```









