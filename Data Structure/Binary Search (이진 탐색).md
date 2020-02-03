# Binary Search (이진 탐색)

이진 탐색은 `O(log n)`의 시간복잡도를 가지는 가장 효과적인 탐색 알고리즘 중 하나입니다. Balanced binary search tree(균형 이진 탐색 트리)에서 요소를 검색하는 것과 비슷합니다.



이진 탐색을 사용하기 위해서는 두 가지 조건이 있습니다.

1. 해당 컬렉션이 RandomAccessCollection 프로토콜을 준수해야 합니다. RandomAccessCollection을 준수하는 컬렉션은 인덱스 이동, 인덱스 사이의 거리 계산 작업에서 빠른 시간 안에 처리할 수 있습니다. 예를 들어 count를 연산하는 경우 `O(1)`이면 가능합니다.
2. 컬렉션이 정렬 되어있어야 합니다.



### 이진 탐색 VS 선형 탐색

스위프트의 Array는 `index(of:)`라는 메소드를 선형 탐색으로 구현했습니다. 이 말은 전체 컬렉션을 순회해 특정 요소를 찾는 것을 의미합니다. 컬렉션의 맨 앞에서부터 시작해 값을 만날 때까지 순회합니다.

반면에 이진 탐색은 이미 정렬되어있는 컬렉션이라는 것을 이용해 조금 더 빠르게 값을 찾아낼 수 있습니다. 

1. **컬렉션의 중간 인덱스를 찾습니다.**

2. **중간 인덱스의 값을 확인합니다.**

   해당 값이 찾고있는 값이라면 리턴합니다. 아니라면 다음 단계로 넘어갑니다.

3. **이진 탐색을 재귀적으로 반복합니다.**

   찾고있는 값이 2번에서 확인한 값보다 작은지, 큰지를 확인합니다. 작다면 중간 인덱스의 왼쪽에 있는(인덱스가 중간 인덱스보다 작은) 요소들만 추려 다시 이진 탐색을 합니다. 크다면 중간 인덱스의 오른쪽에 있는 (인덱스가 중간 인덱스보다 큰) 요소들만 추려 다시 이진 탐색을 합니다.

   더 이상 컬렉션을 왼쪽, 오른쪽으로 나눌 수 없거나, 값을 찾을 때 까지 반복합니다.

이진 탐색은 위와 같은 과정을 통해서 값을 찾아낼 수 있고,  `O(log n)`의 시간복잡도를 가집니다.





### 구현

```swift
//이진 탐색은 RandomAccessCollection 프로토콜을 만족하는 타입에서만 작동할 수 있기 때문에
//RandomAccessCollection의 extension으로 구현하였습니다.
//값을 비교해야하기 때문에, Element이 Comparable 프로토콜을 채택하는 타입으로 제한합니다.
public extension RandomAccessCollection where Element: Comparable {
  //이진 탐색은 재귀적이기 때문에 검색할 범위가 필요합니다. range가 nil이라면 전체 컬렉션을 탐색합니다.
  func binarySearch(for value: Element, in range: Range<Index>? = nil) -> Index? {
    //range가 nil이라면 전체 인덱스를 탐색합니다.
    let range = range ?? start..<endIndex
    
    //range가 하나 이상의 요소를 갖는 범위인지 판단합니다. 만약 요소를 갖지않는다면 탐색 실패로 nil을 반환합니다.
    guard range.lowerBound < range.upperBound else {
      return nil
    }
    
    //위에서 하나 이상의 요소를 갖는 범위임을 확인했기 때문에 middle 인덱스를 구할 수 있습니다.
    let size = distance(from: range.lowerBound, to: range.upperBound)
    let middle = index(range.lowerBound, offsetBy: size / 2)
    
    //middle 인덱스의 값과 찾고있는 값을 비교합니다.
    if self[middle] == value {
      //찾았다! middle 인덱스를 반환합니다.
      return middle
    } else if self[middle] > value {
      //middle 값이 찾고있는 값보다 크다면 range의 맨 앞부터 middle의 앞부분까지의 범위를 다시 이진탐색을 합니다.
      return binarySearch(for: value, in: range.lowerBound..<middle)
    } else {
      //middle 값이 찾고있는 값보다 작다면 middle의 다음 인덱스부터 range의 맨 뒷부분까지의 범위를 다시 이진탐색 합니다.
      return binarySearch(for: value, in: index(after: middle)..<range.upperBound)
    }
  }
}

let array = [1, 5, 15, 17, 19, 22, 24, 31, 105, 150]
let search31 = array.firstIndex(of: 31)
let binarySearch31 = array.binarySearch(for: 31)
print("index(of:): \(String(describing: search31))")
print("binarySearch(for:): \(String(describing: binarySearch31))")

/*
index(of:): Optional(7)
binarySearch(for:): Optional(7)
*/
```



정렬된 배열이 주어진다면 이진 탐색을 고려해보는 것이 좋습니다. 또한 탐색에 `O(n^2)`의 시간이 걸릴 것 같다면, 정렬 후에 이진 탐색을 사용하면 `O(nlong)`으로 시간을 줄일 수 있습니다.



