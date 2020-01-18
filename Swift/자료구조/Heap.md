# Heap

힙은 완전 이진트리로, 두 가지 경우가 있다.

1. Max Heap : 더 높은 값이 더 높은 우선순위를 가진다.

   최대 힙인 경우, 부모 노드는 반드시 자식 노드보다 크거나 같은 값을 가진다. 따라서 루트 노드는 항상 가장 큰 값이 된다.

2. Min Heap : 더 낮은 값이 더 높은 우선순위를 가진다.

   최소 힙인 경우, 부모 노드는 반드시 자식 노드보다 작거나 같은 값을 가진다. 따라서 루트 노드는 항상 가장 작은 값이 된다.

또한, 힙은 완전 이진트리이기 때문에 가장 마지막 레벨을 제외하고는 모두 채워져있어야한다.

<br />

### 힙의 사용

- Collection의 최솟값 혹은 최댓값을 계산
- Heap Sort
- Priority queue를 만들 때
- Priority queue로 Prim, Dijkstra 그래프 알고리즘을 이용할 때

<br />

## Array를 이용한 힙

트리에서 각 노드들은 자식들의 참조를 저장하고있다. 이진 트리에서는 오른쪽 자식, 왼쪽 자식을 참조한다. 힙은 이진 트리이지만 배열로 간단하게 표현할 수 있다.

힙을 배열로 표현하면, 힙에 있는 요소들을 메모리에 모두 함께 저장해 시간과 공간 복잡도를 효율적으로 사용할 수 있다. 또한 두 요소의 값을 바꾸는 작업에서 이진 트리보다 훨씬 간단하게 할 수 있다.

<br />

<img width="412" alt="힙" src="https://user-images.githubusercontent.com/16719527/72661295-76ce9700-3a1b-11ea-9c11-f545bf8988f4.png">

힙을 배열로 나타내면, 각 레벨의 요소나 자식 요소들을 쉽게 조회할 수 있다. 트리에서 자식 노드를 조회하는 것은 `O(log n)`이지만, 배열에서 조회하는 것은 `O(1)` 로 가능하다.

<br /><br />

### 자식 노드 인덱스 구하기



### 자식 노드 인덱스 구하기

부모 노드의 인덱스가 i라고 하면,

- 왼쪽 자식 노드 : 2i + 1
- 오른쪽 자식 노드 : 2i + 2

<br />

### 부모 노드 인덱스 구하기

현재 노드의 인덱스가 i라고 하면, 해당 노드의 부모 노드 인덱스는 `floor((i - 1) / 2)` 가 된다.

<br /><br />

```swift
struct Heap<Element: Equatable> {
  var elements: [Element] = []
  let sort: (Element, Element) -> Bool
  
  init(sort: @escaping (Element, Element) -> Bool) {
    self.sort = sort
    //sort는 힙이 어떻게 정렬되어야하는지를 나타낸다.
    //즉, 해당 힙을 최소힙으로 만들지, 최대힙으로 만들지를 의미한다.
  }
  
  var isEmpty: Bool {
    return elements.isEmpty
  }
  
  var count: Int {
    return elements.count
  }
  
  func peek() -> Element? {
    return elements.first
  }
  
  func leftChildIndex(ofParentAt index: Int) -> Int {
    return (2 * index) + 1
  }
  
  func rightChildIndex(ofParentAt index: Int) -> Int {
    return (2 * index) + 2
  }
  
  func parentIndex(ofChildAt index: Int) -> Int {
    return (index - 1) / 2
  }
}
```

<br /><br />

### Remove

최대 힙으로 예를 들면, 지우는 동작은 힙에서 최댓값을 지우는 동작이다. 최댓값은 루트 노드(배열에서는 맨 앞 element)다.

1. 첫 번째 노드(루트 노드)와 마지막 노드의 위치를 바꾼 후 마지막 노드가 된 최댓값을 삭제한다.

2. 루트 노드를 힙 조건(자식 노드보다 크거나 같아야 함)에 맞게 이동시킨다. 왼쪽, 오른쪽 자식 노드들을 확인하면서 자신보다 큰 값이 있다면 자식 노드와 위치를 바꾼다.

3. 노드는 2번 과정을 반복하다가 더 이상 자식들 중 자신보다 큰 값이 없거나, 자식이 없다면(마지막 레벨에 도달했다면) 멈춘다.

<br />

```swift
mutating func remove() -> Element? {
  guard !isEmpty else { return nil }
  //1. 힙이 비어있는지 확인한다.
  elements.swapAt(0, count - 1)
  //2. 루트와 마지막 element의 위치를 바꾼다.
  defer {
    siftDown(from: 0)
    //4. 이 힙은 더이상 최대힙, 최소힙이 아니기 때문에 sift down 과정을 거쳐 힙 조건에 맞추도록 한다.
  }
  return elements.removeLast()
  //3. 배열에서 마지막 값(최댓값 혹은 최솟값)을 지우고, 리턴한다.
}

mutating func siftDown(from index: Int) {
  var parent = index
  //1. 부모 index를 저장한다.
  while true {
    //2. return 할 때까지 sift down을 반복한다.
    let left = leftChildIndex(ofParentAt: parent)
    let right = rightChildIndex(ofParentAt: parent)
    var candidate = parent

    if left < count && sort(elements[left], elements[candidate]) {
      candidate = left
      //3. 만약 왼쪽 자식 노드가 있고, 부모보다 큰 우선순위를 갖는다면 자식 노드의 인덱스를 candidate에 저장한다.
    }
    if right < count && sort(elements[right], elements[candidate]) {
      candidate = right
      //4. 만약 오른쪽 자식 노드가 있고, 부모보다 큰 우선순위를 갖는다면 자식 노드의 인덱스를 candidate에 저장한다.
    }
    if candidate == parent {
      return
      //5. candidate가 여전히 parent라는 것은 양쪽 자식 노드가 없거나, 양쪽 자식 노드와 힙 조건을 맞췄다는 뜻이다.
      //더이상 sift down 할 필요가 없다는 것을 의미하므로 return 한다.
    }
    elements.swapAt(parent, candidate)
    //6. candidate와 parent의 위치를 변경하고 sift down을 계속한다.
    parent = candidate
  }
}
```

`remove()`의 전체 과정의 시간복잡도는 `O(log n)`이다. 배열에서 element의 위치를 바꾸는 것은 `O(1)`이므로, `siftDown()`의 시간복잡도는 `O(log n)`이다.

<br /><br />

### Insert

1. 추가할 값을 배열의 맨 뒤에 추가한다.
2. 추가한 노드의 부모 노드와 힙 조건에 맞는지 확인한다. 맞지않다면 부모 노드와 위치를 바꾼다.
3. 노드는 2번 과정을 계속 반복하다가 조건에 맞는 부모를 만나거나, 루트 노드에 도달하면 멈춘다.

<br />

```swift
mutating func insert(_ element: Element) {
  elements.append(element)
  siftUp(from: elements.count - 1)
}

mutating func siftUp(from index: Int) {
  var child = index
  var parent = parentIndex(ofChildAt: child)
  while child > 0 && sort(elements[child], elements[parent]) {
    elements.swapAt(child, parent)
    child = parent
    parent = parentIndex(ofChildAt: child)
  }
}
```

`insert(_:)`의 시간복잡도는 `O(log n)` 이다. 배열에 element를 추가하는 것은 `O(1)`이고, `siftUp()` 의 시간복잡도는  `O(log n)` 이다.

<br /><br />

### 루트 노드가 아닌 요소 삭제하기

```swift
mutating func remove(at index: Int) -> Element? {
  guard index < elements.count else { return nil }
  //1. 해당 인덱스가 배열에 포함되는 인덱스인지 확인하고, 아니라면 nil을 반환한다.
  
  if index == elements.count - 1 {
    return elements.removeLast()
    //2. 만약 마지막 요소를 지우는 것이라면 단순히 지우고 리턴하기만 하면 된다.
  } else {
    elements.swapAt(index, elements.count - 1)
    //3. 마지막 요소가 아니라면, 해당 인덱스 값과 마지막 인덱스 값의 위치를 변경한다.
    defer {
      siftDown(from: index)
      siftUp(from: index)
      //5. 힙 조건을 맞추기 위해 sift down, sift up을 실행한다.
    }
    return elements.removeLast()
    //4. 마지막 요소를 지우고 리턴한다.
  }
}
```

**왜 sift down과 sift up을 모두 실행할까?**

만약 마지막 레벨에 있는 값을 삭제한다면 sift up만 실행하면 된다. 하지만 다른 레벨에 있는 값이 삭제된다면 sift down을 실행해야 할 것이다.

루트 노드가 아닌 요소를 삭제하는 것은 `O(log n)`의 작업이다.

<br /><br />

### 힙에서 요소 검색하기

삭제하고 싶은 값이 있다면, 힙에서 어떤 인덱스가 해당 값을 가졌는지 찾아야한다.

힙은 검색 작업에 빠르지 않다. 이진 검색 트리로 된 힙이라면 `O(log n)` 시간에 조회할 수 있지만, 배열로 된 힙이고 배열 안의 노드 정렬이 다르기 때문에 우리는 이진 검색을 사용할 수 없다. 

```swift
func index(of element: Element, startingAt i: Int) -> Int? {
  if i >= count {
    return nil
    //1. i가 배열의 크기보다 크면 검색 실패로 nil을 반환한다.
  }
  if sort(element, elements[i]) {
    return nil
    //2. 찾고있는 요소가 i의 값보다 우선 순위가 높은지 확인한다. 루트 노드부터 확인하기 때문에 우선 순위가 높다면 해당 값이 없다는 의미이므로 nil을 반환한다.
  }
  if element == elements[i] {
    return i
    //3. 검색 성공! i를 반환한다.
  }
  if let j = index(of: element, startingAt: leftChildIndex(ofParentAt: i)) {
    return j
    //4. i의 왼쪽 자식부터 재귀적으로 index를 찾는다.
  }
  if let j = index(of: element, startingAt: rightChildIndex(ofParentAt: i)) {
    return j
    //5. i의 오른쪽 자식부터 재귀적으로 index를 찾는다.
  }
  return nil
  //6. 4,5 과정에서 검색에 실패했다면, 값이 없다는 의미이므로 nil을 반환한다.
}
```

최악의 경우 우리는 모든 요소들을 확인하게 되므로 `O(n)` 이지만 힙의 특징을 사용하여 검색의 시간을 줄일 수 있었다.

<br /><br />

### 이미 있는 배열을 힙으로 만들기

초기화 함수를 추가한다. 

```swift
init(sort: @escaping (Element, Element) -> Bool, elements: [Element] = []) {
  self.sort = sort
  self.elements = elements
  //배열이 들어오면, 이 값들을 힙의 elements 배열로 놓는다.
  
  if !elements.isEmpty {
    for i in stride(from: elements.count / 2 - 1, through: 0, by: -1) {
      siftDown(from: i)
      //elements를 힙 조건에 맞게 정렬한다.
      //전체 요소들을 확인하지 않아도 되고, leaf가 아닌 노드들부터 루트 노드까지 sift down 한다.
    }
  }
}
```

<br />

------

http://www.cse.hut.fi/en/research/SVG/TRAKLA2/tutorials/heap_tutorial/taulukkona.html









