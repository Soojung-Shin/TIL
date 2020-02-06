# Heap Sort (힙 정렬)

힙 정렬은 힙을 사용해서 배열을 오름차순으로 정렬하는 comparison 기반의 알고리즘 입니다.

부분적으로 정렬된 이진트리인 힙은 다음과 같은 특징을 갖습니다.

1. 최대 힙인 경우, 모든 부모 노드는 자식 노드보다 큽니다.
2. 최소 힙인 경우, 모든 부모 노드는 자식 노드보다 작습니다.

<br />

<br /><br />

### 작동 방식

1. `[6, 12, 2, 26, 8, 18, 21, 9, 5]` 라는 정렬되지 않은 배열이 있다고 생각해봅시다.

   먼저 정렬되지 않은 배열이 주어지면 해당 배열을 힙으로 만듭니다.

   `[26, 12, 21, 9, 8, 2, 18, 6, 5]`

   힙에 단일 노드를 추가하는 데에 `O(logn)`이 걸리니까 전체 힙을 만드는 데 걸리는 시간 복잡도는`O(nlogn)` 입니다.

<br />

2. 우선 최대힙의 경우 루트가 항상 최댓값이기 때문에 최댓값을 바로 뽑아낼 수 있습니다. 루트와 배열의 맨 뒷 요소와 자리를 바꿉니다.

   `[5, 12, 21, 9, 8, 2, 18, 6 // 26]`

   새로운 루트인 `5` 를 힙의 규칙에 맞게 이동시킵니다.

   `[21, 12, 18, 9, 8, 2, 5, 6 // 26]`

   마지막 부분은 더 이상 힙의 부분이 아니고 정렬된 배열의 일부분이라고 간주합니다.

<br />

3. 다시 힙의 새로운 루트가 된 `21` 을 정렬된 배열 앞부분에 놓습니다.

   `[6, 12, 18, 9, 8, 2, 5 // 21, 26]`

   새로운 루트인 `6` 을 힙의 규칙에 맞게 이동시킵니다.

   `[18, 12, 6, 9, 8, 2, 5 // 21, 26]`

<br />

4. 힙의 크기가 1이 될 때까지 위 과정을 반복합니다.

<br />

<p align="center"><img width="230" alt="image-20200203222941137" src="https://user-images.githubusercontent.com/16719527/73659483-b19e2380-46d9-11ea-991e-50a0575fbc89.png"></p>
<br /><br /><br />

### 구현

힙은 이미 구현되어있다고 가정합니다. 이미 힙에 `siftDown` 과정이 구현되어있기 때문에 실제 힙 정렬의 구현은 매우 간단합니다.

```swift
extension Heap {
  func sorted() -> [Element] {
    //먼저 기존 힙을 카피해놓습니다. 정렬에는 이 힙을 사용해 기존 힙에는 영향이 가지 않도록 합니다.
    var heap = Heap(sort: sort, elements: elements)
    
    //배열의 마지막 요소부터 앞으로 배열을 순회합니다.
    for index in heap.elements.indices.reversed() {
      //첫번째 요소와 마지막 요소의 위치를 바꿉니다. 힙의 최댓값을 맨 뒤로 옮기는 작업입니다.
      heap.elements.swapAt(0, index)
      //힙은 이제 규칙을 지키지 않고 있기 때문에 루트에 있는 값을 siftDown 시킵니다.
      heap.siftDown(from: 0, upTo: index)
      //siftDown 과정이 끝나면 다시 힙의 최댓값이 루트에 오게 됩니다.
    }
    return heap.elements
  }
}
```

힙 정렬을 지원하기 위해서 `siftDown(from:upTo:)` 메소드를 추가했습니다. 이런 식으로 sift down은 배열의 정렬되지 않은 부분만을 사용해 힙의 크기를 줄일 수 있습니다.

<br /><br />

아래는 추가된 `siftDown(from:upTo:)` 메소드 입니다. 자식을 확인할 때 `upTo` 보다 작은지 확인합니다.

```swift
mutating func siftDown(from index: Int, upTo: Int) {
  var parent = index
  while true {
    let left = leftChildIndex(ofParentAt: parent)
    let right = rightChildIndex(ofParentAt: parent)
    var candidate = parent

    if left < count && sort(elements[left], elements[candidate]) && left < upTo {
      candidate = left
    }
    if right < count && sort(elements[right], elements[candidate]) && right < upTo {
      candidate = right
    }
    if candidate == parent {
      return
    }
    elements.swapAt(parent, candidate)
    parent = candidate
  }
}
```

<br /><br />

```swift
[26, 12, 21, 9, 8, 18, 2, 6, 5]
8
[21, 12, 18, 9, 8, 5, 2, 6, 26]
7
[18, 12, 6, 9, 8, 5, 2, 21, 26]
6
[12, 9, 6, 2, 8, 5, 18, 21, 26]
5
[9, 8, 6, 2, 5, 12, 18, 21, 26]
4
[8, 5, 6, 2, 9, 12, 18, 21, 26]
3
[6, 5, 2, 8, 9, 12, 18, 21, 26]
2
[5, 2, 6, 8, 9, 12, 18, 21, 26]
1
[2, 5, 6, 8, 9, 12, 18, 21, 26]
0
[2, 5, 6, 8, 9, 12, 18, 21, 26] -> 정렬 완료!
```

<br /><br />

힙 정렬은 최선, 최악, 평균의 경우 모두 `O(n logn)`의 시간 복잡도를 갖습니다. 전체 목록을 한 번 순회해야하고, 요소를 교체할 때마다 `O(logn)`인 sift down 작업을 하기 때문입니다.



-----

Artwork/images/designs: from Data Structures & Algorithms in Swift, available at www.raywenderlich.com