# Tries (트라이)

트라이는 컬렉션으로 표현할 수 있는 데이터들을 저장하는데 특화되어있는 트리다.

예를 들어 아래와 같은 영어단어들을 생각해볼 수 있다.

<p align="center"><img width="220" alt="image-20200126121628627" src="https://user-images.githubusercontent.com/16719527/73130933-5b7c1100-4045-11ea-933f-367daa0cf0a2.png"></p>
스트링의 각 문자들은 노드에 맵핑된다. 스트링의 마지막 노드는 종료 노드로 표시된다. 위 이미지에서 점으로 나타낸 것이 종료 노드를 나타내는 것이다.

트라이의 이점은 접두사 매칭 작업 등에서 가장 잘 나타난다.

<br /><br />

### 예시

스트링 컬렉션을 가지고있을 때, 접두사 매칭 작업을 어떻게 구축할 수 있을까?

```swift
class EnglishDictionary {
  private var words: [String]
  
  func words(matching prefix: String) -> [String] {
    return words.filter { $0.hasPrefix(prefix) }
  }
}
```

<br />

`words(matching:)` 은 문자열 배열을 살펴보고, 접두사에 매칭되는 스트링을 반환할 것이다.

`words` 배열이 크지않을 땐 꽤 괜찮은 방법이지만, 만약 배열에 수천 개 이상의 단어가 있다면 시간이 아주 오래걸릴 것이다. `words` 배열의 크기를 `n`, 해당 배열 중 가장 길이가 긴 스트링의 길이를 `k`라고 하면, `words(matching:)`의 시간 복잡도는 `O(k*n)` 다. 

<br />

트라이는 이런 유형의 문제에서 우수한 성능을 가진다. 노드가 자식 노드를 여러개 가질 수 있는 트리로서, 각 노드는 단일 문자를 나타낼 수 있다. 루트에서 종료 노드까지 따라가면서 단어를 만들어갈 수 있다. 트리의 재밌는 특징은 여러 단어가 동일한 문자를 공유 할 수 있다는 것이다.

<br />

아래 이미지에서 접두사로 `CU` 가 오는 단어를 찾아보자.

<p align="center"><img width="220" alt="image-20200126121628627" src="https://user-images.githubusercontent.com/16719527/73130933-5b7c1100-4045-11ea-933f-367daa0cf0a2.png"></p>
처음 `C` 를 가지고 있는 노드를 찾는다. 해당 검색 작업으로 트라이에서 `C`를 포함하지 않는 다른 브랜치들은 빠르게 제외시킬 수 있다.

<p align="center"><img width="220" alt="image-20200126123550228" src="https://user-images.githubusercontent.com/16719527/73130934-5b7c1100-4045-11ea-8db0-c43b7446b7f0.png"></p>
다음 문자인 `U`를 포함하는 단어로 이동해보자. 

<p align="center"><img width="225" alt="image-20200126123829104" src="https://user-images.githubusercontent.com/16719527/73130935-5c14a780-4045-11ea-92ec-f06e2c75b7a0.png"></p>
접두어를 모두 확인했기 때문에, 트라이는 `U` 노드에 연결되어있는 모든 컬렉션을 리턴한다. 이 경우에는 `CUT`과 `CUTE`가 리턴될 것이다. 만약 수십만 개의 단어를 가지고 있을 때, 트라이를 사용한다면 비교 작업을 상당히 줄일 수 있다.

<br /><br /><br />

## 구현

### TrieNode

```swift
public class TrieNode<Key: Hashable> {
  
  //key는 노드에 대한 데이터를 갖는다. 루트 노드인 경우 key를 가지지않기 때문에 옵셔널 처리해주었다.
  public var key: Key?
  
  //부모 노드에 대해 약한 참조한다. 이를 통해 remove 메소드를 간소화할 수 있다.
  public weak var parent: TrieNode?
  
  //이진 검색 트리에서 노드는 왼쪽과 오른쪽 자식만 가지지만, 트라이는 자식 노드로 여러개를 가질 수 있다.
  //이를 위해서 딕셔너리를 사용했다.
  public var children: [Key : TrieNode] = [:]
  
  //종료 노드임을 나타내는 변수다.
  public var isTerminating = false
  
  public init(key: Key?, parent: TrieNode?) {
    self.key = key
    self.parent = parent
  }
}
```

<br /><br />

### Trie

이제 노드들을 관리할 트라이를 만들어보자.

```swift
public class Trie<CollectionType: Collection>
	where CollectionType.Element: Hashable {
    
  public typealias Node = TrieNode<CollectionType.Element>
  private let root = Node(key: nil, parent: nil)
  public init() {}
}
```

`Trie` 클래스는 `String`을 포함하여 `Collection` 프로토콜을 준수하는 모든 타입으로 생성할 수 있도록 만들었다. 또한 트라이 내부에서 `children` 딕셔너리의 키로 컬렉션의 요소 값을 사용할 것이기 때문에, 컬렉션의 각 요소들은 반드시 `Hashable`을 준수해야한다.

<br /><br />

### Insert

트라이는 `Collection`을 준수하는 모든 타입에서 작동한다. 컬렉션을 가져와서 일련의 노드로 나타내고, 각 노드는 컬렉션의 요소에 맵핑된다.

```swift
public func insert(_ collection: CollectionType) {
  
  //current는 순회 진행 상황을 저장한다. 루트 노드에서 시작한다.
  var current = root
  
  //트라이는 컬렉션의 각 요소를 별도의 노드에 저장한다.
  for element in collection {
    //컬렉션의 각 요소에 대해, children 딕셔너리에 해당 노드가 있는지 검사해야 한다.
    //만약 없다면, 새로운 노드를 만든다.
    if current.children[element] == nil {
      current.children[element] = Node(key: element, parent: current)
    }
    //각각의 루프에서, current 값을 다음 노드로 옮겨야 한다.
    current = current.children[element]!
  }
  
  //순회가 끝나면, current는 컬렉션의 마지막을 나타내는 노드가 될 것이다. 종료 노드로 표시한다.
  current.isTerminating = true
}
```

컬렉션의 길이를 `k`라고 한다면, 삽입 작업의 시간 복잡도는 `O(k)`가 된다. 컬렉션의 모든 요소를 순회하고, 노드를 만들어야하기 때문이다.

<br /><br />

### Contains

```swift
public func contains(_ collection: CollectionType) -> Bool {
  var current = root
  
  for element in collection {
    guard let child = current.children[element] else {
      return false
    }
    current = child
  }
  return current.isTerminating
}
```

삽입 작업과 아주 비슷하다. 컬렉션의 모든 요소가 트라이에 있는지 확인한다. 마지막에 도달했을 때, 반드시 종료 노드여야 한다. 만약 그렇지 않으면 해당 컬렉션은 트라이에 추가되지 않은 것이고, 더 큰 컬렉션의 서브셋을 발견한 것일 뿐이다.

컬렉션의 길이를 `k`라고 한다면, 컬렉션이 트라이에 있는지 확인하기 위해 `k`개의 노드를 순회해야하기 때문에 `contains(_:)`의 시간 복잡도는 `O(k)`다.

<br /><br />

### Remove

삭제 작업은 조금 까다롭다. 트라이는 여러 컬렉션이 하나의 노드를 공유할 수 있기 때문에 주의해야 한다.

```swift
public func remove(_ collection: CollectionType) {
  
  //contains 작업과 비슷하다.
  //트라이에 해당 컬렉션이 들어있는지 확인하고, current가 해당 컬렉션의 마지막 노드를 가리키도록 한다.
  var current = root
  
  for element in collection {
    guard let child = current.children[element] else {
      return
    }
    current = child
  }
  
  guard current.isTerminating else {
    return
  }
  
  //종료 노드 표시를 false로 변경한다.
  current.isTerminating = false
  
  //컬렉션들은 노드를 공유할 수 있기 때문에 다른 컬렉션에 속하는 요소라면 삭제하면 안된다. 두 가지를 확인해야 한다.
  //1. 현재 노드에 자식 노드가 있다면 다른 컬렉션과 공유하는 노드다.
  //2. 현재 노드가 종료 노드라면 다른 컬렉션과 공유하는 노드다.
  //컬렉션의 종료 노드부터 시작해 노드를 거꾸로 따라 올라가면서 노드를 지워나간다.
  while let parent = current.parent, current.children.isEmpty && !current.isTerminating {
    parent.children[current.key!] = nil
    current = parent
  }
}
```

삭제하려는 컬렉션의 길이를 `k` 라고 하면, 삭제 작업의 시간 복잡도는 `O(k)`다.

<br /><br />

### Prefix Matching

트라이에서 가장 상징적인 알고리즘인 접두어 매칭 알고리즘이다.

```swift
public extension Trie where CollectionType: RangeReplaceableCollection { ... }
```

접두어 매칭 알고리즘은 컬렉션 타입이 `RangeReplaceableCollection` 으로 제한되기 때문에 `extension` 내부에 작성할 것이다. 컬렉션 타입을 제한하는 이유는 이 알고리즘이 `RangeReplaceableCollection` 타입의 메소드인 `append()`를 사용해야 하기 때문이다.

<br />

```swift
func collections(startingWith prefix: CollectionType) -> [CollectionType] {
  
  //트라이가 해당 접두어를 포함하고 있는지 확인한다. 만약 포함하고 있지 않다면 빈 배열을 리턴한다.
  var current = root
  for element in prefix {
    guard let child = current.children[element] else {
      return []
    }
    current = child
  }
  
  //접두어의 마지막 문자까지 확인했다면, collections(startingWith:after:) 메소드를 사용해서 현재 노드 다음의 모든 시퀀스를 찾는다.
  return collections(startingWith: prefix, after: current)
}
```

<br /><br />

```swift
private func collections(startingWith prefix: CollectionType, after node: Node) -> [CollectionType] {
  
  //결과를 저장할 배열이다. 만약 현재 노드가 종료 노드라면 여기에 추가한다.
  var results: [CollectionType] = []
  
  if node.isTerminating {
    results.append(prefix)
  }
  
  //현재 노드의 자식 노드를 확인한다. 모든 자식 노드에 대해서 재귀적으로 collections(startingWith:after:) 메소드를 호출해 다른 종료 노드들을 찾는다.
  for child in node.children.values {
    var prefix = prefix
    prefix.append(child.key!)
    results.append(contentsOf: collections(startingWith: prefix, after: child))
  }
  
  return results
}
```

<br />

접두사가 매칭된 컬렉션 중 가장 긴 컬렉션의 길이를 `k`라고 하고, 접두사 매칭된 컬렉션의 길이를 `O(m)`이라고 하면, `collection(startingWith:)`의 시간 복잡도는 `O(k*m)`이 된다.

배열을 리턴하는 메소드인 `collections(startingWith:after:)`의 시간 복잡도는 컬렉션의 길이가 `n`이라고 하면 `O(k*n)`이 된다.

각 컬렉션이 균일하게 분산 된 대규모 데이터 집합의 경우, 접두사 일치 확인 작업에 배열을 사용하는 것과 비교하여 성능이 훨씬 향상된다.

<br /><br /><br />

-----

Artwork/images/designs: from Data Structures & Algorithms in Swift, available at www.raywenderlich.com