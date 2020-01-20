# Priority Queue(우선순위 큐)

큐는 단순히 선입 선출(FIFO) 순서를 사용하여 요소의 순서를 유지하는 리스트다. 하지만 우선순위 큐는 큐의 FIFO 대신 우선 순위에 따라 요소를 대기열에서 제외시키는 다른 버전의 큐다.

1. 최대 우선순위 큐는 항상 가장 큰 값이 맨 앞에 있다.
2. 최소 우선순위 큐는 항상 가장 작은 값이 맨 앞에 있다.

<br />

우선순위 큐는 특히 리스트의 최댓값 혹은 최솟값을 확인해야할 때, 유용하게 사용할 수 있다.

<br />

### 용도

- 다익스트라 알고리즘에서 우선순위 큐를 이용해 최소 비용을 계산한다.
- 경로 탐색 알고리즘에서 우선순위 큐를 이용해 길이가 가장 짧은 아직 탐색되지 않은 경로를 추적할 수 있다.
- Heap Sort
- 압축 트리를 구축하는 허프만 코딩에서 최소 우선순위 큐는 부모 노드가 없는 빈도가 가장 작은 두 개의 노드를 반복적으로 찾는 데 사용됩니다.

<br />

<br />

### 큐와 차이점

```swift
public protocol Queue {
  associatedtype Element
  mutating func enqueue(_ element: Element) -> Bool
  mutating func dequeue() -> Element?
  var isEmpty: Bool { get }
  var peek: Element? { get }
}
```

우선순위 큐는 기본 동작은 일반적인 큐와 동일하고, 구현 파트만 다르다.

- `enqueue` : 큐에 요소를 넣는 작업. 작업이 성공하면 `true`를 반환한다.
- `dequeue` : 큐에서 우선순위가 가장 큰 요소를 제거하고 반환한다. 큐가 비어있으면 `nil`을 반환한다.
- `isEmpty` : 큐가 비어있는지 확인한다.
- `peek` : 우선순위가 가장 큰 요소를 확인하여 반환한다. 제거는 하지않는다. 큐가 비어있으면 `nil`을 반환한다.

<br /><br /><br />

## 구현

우선순위 큐를 구현하는 방법은 세 가지가 있다.

1. Sorted array

   최댓값 혹은 최솟값을 얻는데 `O(1)`로 효율적이다. 하지만 삽입은 순서대로 삽입해야하므로 `O(n)`이 필요해 느린 것이 단점이다.

2. Balanced binary search tree(균형 이진 검색 트리)

   `O(log n)`의 시간으로 최댓값과 최솟값을 모두 얻을 수 있는 double-ended priority queue를 만드는데 유용하다. 삽입 작업도 `O(log n)`으로 정렬 배열보다 빠르다.

3. Heap

   힙은 부분적으로만 정렬하기 때문에 배열보다 효율적이다. 모든 힙의 작업은 `O(log n)`이고, peek 작업은 `O(log 1)`이다.

<br />

우리는 힙을 사용해 우선순위 큐를 만들어 볼거다~!

```swift
struct PriorityQueue<Element: Equatable>: Queue {
  //우선순위 큐는 Queue 프로토콜을 준수한다.
  //또한 제네릭 파라미터인 Element는 값을 비교해야하기 때문에 Equatable 프로토콜을 준수한다.
  
  private var heap: Heap<Element>
  
  init(sort: @escaping (Element, Element) -> Bool,
      elements: [Element] = []) {
    //sort라는 클로저를 인수로 받아, 최소 우선순위 큐와 최대 우선순위 큐를 모두 만들 수 있도록 한다.
    heap = Heap(sort: sort, elements: elements)
  }
}
```

<br />

`Queue` 프로토콜을 준수하기 위한 메소드를 추가해준다.

```swift
var isEmpty: Bool {
  return heap.isEmpty
}

var peek: Element? {
  return heap.peek()
}

mutating func enqueue(_ element: Element) -> Bool {
  heap.insert(element)
  //힙은 insert 작업에서 sift up 작업을 함께 진행한다.
  return true
}

mutating func dequeue() -> Element? {
  return heap.remove()
  //힙에서 remove를 하면 마지막 요소와 첫번째 요소를 바꾼 뒤 반환하고, 루트 노드의 sift down 작업이 일어난다.
}
```

그냥 힙의 메소드를 호출하기만 하면 된다.

`enqueue(_:)` 작업은 `O(log n)`이고, `dequeue()` 작업 또한 `O(log n)`이다.

