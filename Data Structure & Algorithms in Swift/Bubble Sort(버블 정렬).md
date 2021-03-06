# Bubble Sort(버블 정렬)

버블 정렬은 값을 비교하고 값을 바꾸는 과정을 반복하는 정렬이다. 더 큰 값들이 컬렉션의 뒷쪽으로 배치되어 "bubble up"되기 때문에 버블정렬이라고 한다.

<br />

`[9, 4, 10, 3]` 인 배열이 있다고 생각해보자.

1. 버블정렬은 컬렉션의 맨 앞부터 시작된다. `9` 와 `4` 를 비교한다. 앞에 있는 값인 `9` 가 `4` 보다 크기 때문에 값을 교환한다.

   ➔ `[4, 9, 10, 3]`

2. 다음 인덱스로 이동한다. `9` 와 `10` 을 비교한다. 순서에 맞기 때문에 넘어간다.

   ➔ `[4, 9, 10, 3]`

3. 다음 인덱스로 이동한다. `10` 과 `3` 을 비교한다. 앞에 있는 값인 `10` 이 `3` 보다 크기 때문에 값을 교환한다.

   ➔ `[4, 9, 3, 10]`

   

<br />

컬렉션을 한 번 순회하는 것으로는 대부분 정렬이 완성되지 않는다. 그러나 가장 큰 값인 `10` 이 컬렉션의 맨 끝으로 bubble up 되었다.

<br />

`9` 와 `4` 에도 동일한 과정이 진행된다.

➔ `[4, 9, 3, 10]`

➔ `[4, 3, 9, 10]`

➔ `[3, 4, 9, 10]`

<br />

버블 정렬은 값 교환 없이 컬렉션의 모든 값을 통과했을 때만 종료된다. 크기가  `n` 인 컬렉션일 때 최악의 경우, `n - 1` 번의 순회가 필요하다.

<br /><br />

### 구현

```swift
public func bubbleSort<Element>(_ array: inout [Element]) where Element: Comparable {
  //크기가 1인 컬렉션은 정렬이 필요없기 때문에 리턴한다. 크기가 2이상인 경우에만 정렬을 진행한다.
  guard array.count >= 2 else {
    return
  }
  
  //한 번의 패스로 가장 큰 값이 컬렉션의 맨 끝에 오게 된다.
  //모든 패스는 이전 패스보다 하나 더 적은 값을 비교해야하므로 기본적으로 각 패스마다 하나씩 배열을 줄인다.
  for end in (1..<array.count).reversed() {
    var swapped = false
    
    //아래 for문은 패스 한 번을 수행한다.
    //컬렉션을 순회하면서 값을 비교하고 교환하는 작업을 한다.
    for current in 0..< end {
      if array[current] > array[current + 1] {
        array.swapAt(current, current + 1)
        swapped = true
      }
    }
    
    //만약 패스에 값 교환이 없었다면 컬렉션이 정렬되었다는 것이므로 리턴한다.
    if !swapped {
      return
    }
  }
}
```

<br />

버블 정렬은 이미 배열이 정렬되어있을 경우 `O(n)` 의 시간 복잡도를 가지지만, 최악의 경우 `O(n^2)` 의 시간 복잡도를 가진다. 빠른 정렬법은 아니다!

<br />

<br />

### 일반화

버블 정렬을 배열이 아닌 컬렉션에서도 이용할 수 있도록 일반화 시켜보자!

버블 정렬은 앞에서 뒤로만 순회하기 때문에 어떤 컬렉션에서도 사용할 수 있다. 하지만 정렬하는 과정에서 위치를 바꾸는 작업이 필요하기 때문에 `MutableCollection(가변 컬렉션)` 프로토콜을 준수해야 한다.

<br />

```swift
public func bubbleSort<T>(_ collection: inout T)
	where T: MutableCollection, T.Element: Comparable {
  
  guard collection.count >= 2 else {
    return
  }
    
  for end in collection.indices.reversed() {
    var swapped = false
    var current = collection.startIndex
    
    while current < end {
      //current의 뒤에 있는 값을 가져온다.
      let next = collection.index(after: current)
      if collection[current] > collection[next] {
        collection.swapAt(current, next)
        swapped = true
      }
      //current에 다음 인덱스 값을 저장하고 위 과정을 반복한다.
      current = next
    }
    
    //swap이 일어나지 않았다면 종료한다.
    if !swapped {
      return
    }
  }
}
```

알고리즘의 동작은 동일하다. 컬렉션의 인덱스를 사용하도록 변경하기만 하면 된다.

