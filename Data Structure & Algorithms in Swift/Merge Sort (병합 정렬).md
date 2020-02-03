# Merge Sort (병합 정렬)

병합 정렬은 `O(log n)`의 시간복잡도를 가지는 가장 효율적인 알고리즘 중 하나입니다. 병합 정렬은 Divide and conquer(분할 정복) 개념을 기반으로 합니다. 분할 정복이란 큰 문제를 작게 쪼개서 문제를 간단하게 해결할 수 있게한 후에 솔루션들을 구하고 이것들을 다시 결합해 최종 결과를 구하는 방법입니다. 병합 정렬 또한 먼저 분할(split)한 후에 나중에 병합(merge)합니다.

<br />

`[7, 2, 6, 3, 9]`의 배열이 있다고 생각해봅시다.

1. 먼저, 배열을 반으로 나눕니다.

   `[7, 2]` `[6, 3, 9]`

2. 더 이상 나눌 수 없을 때 까지 계속 나눕니다. 쪼갠 후에는 각 배열이 하나의 값만 가져야 합니다. 하나의 값 밖에 없지만(..) 각 배열은 정렬된 상태라고 볼 수 있습니다.

   `[7]` `[2]` `[6]` `[3, 9]`

   `[7]` `[2]` `[6]` `[3]` `[9]`

3. 나눈 순서로 다시 거슬러 올라오면서 배열들을 합칩니다(merge). 각각의 merge 작업에서는 배열을 정렬된 순서로 놓습니다. 나누어져 있는 배열들은 이미 정렬된 상태이기 때문에 간단합니다.

   `[2, 7]` `[3, 6]` `[9]`

   `[2, 3, 6, 7]` `[9]`

   `[2, 3, 6, 7, 9]`

<br /><br /><br />

## 구현

### split

```swift
public func mergeSort<Element>(_ array: [Element]) -> [Element] where Element: Comparable {
  
  //재귀를 사용하기 때문에 기저 사례가 필요합니다.
  //array의 요소가 한 개 밖에 없다면 더 이상 나누지 않고 반환합니다.
  guard array.count > 1 else {
    return array
  }
  
  //반으로 나눈 후 각각 다시 mergeSort를 호출합니다. array의 요소가 한 개 남을 때까지 split을 반복할 것입니다.
  let middle = array.count / 2
  let left = mergeSort(Array(array[..<middle]))
  let right = mergeSort(Array(array[middle...]))
}
```

<br /><br />

### merge

두 개의 배열을 가져와 병합하는 `merge` 함수를 만듭니다. `merge` 함수는 정렬된 두 배열을 받아서 정렬된 순서로 합치는 작업을 합니다.

```swift
private func merge<Element>(_ left: [Element], _ right: [Element]) -> [Element] where Element: Comparable {
  
  //두 배열의 작업 상황을 추적할 변수입니다.
  var leftIndex = 0
  var rightIndex = 0
  
  //병합된 배열이 저장될 변수입니다.
  var result: [Element] = []
  
  //처음부터 시작해서 왼쪽과 오른쪽 배열의 값을 차례대로 비교할 것입니다.
  //둘 중 하나라도 끝에 도달하면 멈춥니다.
  while leftIndex < left.count && rightIndex < right.count {
    let leftElement = left[leftIndex]
    let rightElement = right[rightIndex]
    
    //왼쪽 값과 오른쪽 값을 비교해서 더 작은 값을 result 배열에 추가합니다.
    if leftElement < rightElement {
      result.append(leftElement)
      leftIndex += 1
    } else if leftElement > rightElement {
      result.append(rightElement)
      rightIndex += 1
    } else {
      //값이 서로 같다면 두 개를 동시에 추가합니다.
      result.append(leftElement)
      leftIndex += 1
      result.append(rightElement)
      rightIndex += 1
    }
  }
  
  //while문이 종료됐으면 왼쪽이나 오른쪽 둘 중 하나 이상은 끝까지 확인했다는 의미입니다.
  //아직 끝까지 도달하지 못한 배열이 있는지 확인하고, result에 추가합니다.
  
  //두 배열은 모두 정렬되어있기 때문에 남아있는 배열은 result의 값들보다 크거나 같을 것 입니다.
  //비교하는 과정 없이 바로 추가합니다.
  
  if leftIndex < left.count {
    result.append(contentsOf: left[leftIndex...])
  }
  if rightIndex < right.count {
    result.append(contentsOf: right[rightIndex...])
  }
  
  return result
}
```

<br /><br />

### 마무리

`mergeSort` 함수에서 `merge`를 호출해 마무리 합니다. `mergeSort` 함수가 재귀적으로 동작하기 때문에 끝까지 모두 분할된 후에 병합될 것 입니다.

```swift
public func mergeSort<Element>(_ array: [Element]) -> [Element] where Element: Comparable {
  
  ...
  
  return merge(left, right)
}
```

