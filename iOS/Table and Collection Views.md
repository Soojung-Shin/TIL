# Table and Collection Views

`UITableView`와 `UICollectionView`는 `UIScrollView`의 서브 클래스로 무한한 양의 정보에 무한한 접근을 제공하기 위해 사용된다. 둘은 약간 다른 방식으로 정보를 보여준다.

테이블 뷰는 긴 리스트로 행으로 정보를 보여준다. 리스트는 섹션으로 나누어질 수 있지만 크고 긴 리스트다.

컬렉션 뷰는 많은 설정이 가능한데 보통 2차원적인 방법으로 정보를 보여준다. 디폴트로 플로우 레이아웃을 이용해 나타내어 진다. 플로우 레이아웃의 레이아웃 방식은 간단히 텍스트라고 생각하면 된다. 텍스트는 왼쪽에서 오른쪽으로 흐르듯이 작성되고 공간이 더 이상 없으면 다음 줄로 내려간다. 그리고 계속 이어서 작성되고 공간이 다 차면 다음 줄로 내려간다. 이게 컬렉션 뷰가 디폴트로 보여지는 방식이다. 나타내어질 한 뭉치의 아이템을 제공하고, 이것들은 서로 다른 크기를 가질 수 있다. 그와 관계 없이 쭉 나열하고 다음 줄에 이어서 나타낸다. 텍스트가 양 쪽에서 정렬을 맞추듯이 이 형식도 원한다면 정렬이 가능하다. 이게 플로우 레이아웃이 작동되는 방식이다. 컬렉션 뷰는 완전한 커스터마이징이 가능하다. 원한다면 카드 뭉치 같이 생긴 컬렉션 뷰를 위한 레이아웃을 만들 수 있다. 커스텀 레이아웃을 만들면 된다. 플로우 레이아웃은 컬렉션 뷰에 기본으로 딸려오는 레이아웃이다. 하지만 다른 방법으로 레이아웃을 설정할 수 있다는 것은 알고있어야 한다. 그건 플로우 레이아웃이 아니다!

이 두 개를 같이 이야기 하는 이유는 API,  사용하는 프로그래밍 인터페이스가 매우 비슷하기 때문이다. 거의 동일하다. 다른 점은 하나는 행이고 다른 하나는 2차원으로 놓여진 무작위적인 아이템이라는 것이다.

<br />

<br />

### UITableView

테이블 뷰는 크고 긴 리스트다. 리스트는 섹션으로 나누어질 수 있다. (밑에 예시에서는 Junk, Fruit, Dessert로 나누어져 있다.) 

또한 간단한 보조 정보를 보여줄 수도 있다. 간단한 정보를 나타내는데 네 가지 방법이 있다.

|                        Subtitle style                        |                      Left Detail style                       |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| <p align="center"><img width="400" height="400" alt="image-20200101171102338" src="https://user-images.githubusercontent.com/16719527/72704505-55e17f80-3b9c-11ea-88d8-eb0ef8aaf2da.png"></p> | <p align="center"><img width="400" height="400" alt="image-20200101171341456" src="https://user-images.githubusercontent.com/16719527/72704507-55e17f80-3b9c-11ea-9a22-3a926f98b512.png"></p> |
|                    **Right Detail style**                    |                       **Basic style**                        |
| <p align="center"><img width="400" height="400" alt="image-20200101171412475" src="https://user-images.githubusercontent.com/16719527/72704509-55e17f80-3b9c-11ea-908b-e27c51461c0d.png"></p> |            기본 스타일. subtitle이 보이지 않는다.            |

이 네 가지 스타일은 테이블 뷰에 내장되어 있고, 기본적으로 이들을 사용할 수 있다. 또한 커스텀 스타일을 사용할 수도 있다. 

<br />

#### Custom style

커스텀 스타일은 원하는 UI는 무엇이든지 만들 수 있다. 이 UI는 오토 레이아웃을 사용해 스토리보드에 만든 것이다. 

<p align="center"><img width="300" alt="image-20200101171543780" src="https://user-images.githubusercontent.com/16719527/72704511-567a1600-3b9c-11ea-92fd-74101d61757d.png"></p>

<br />

#### 섹션

테이블 뷰에서 행은 그룹으로 묶여 섹션이 될 수 있다. 섹션은 그룹으로 묶인 것 처럼 보여질 수 있다. 이런 그룹 스타일은 테이블의 데이터가 고정될 때만 사용한다. 음식 리스트처럼 매번 바뀔 수 있는 무작위적인 정보에는 사용하지 않는다. 설정 화면처럼 고정된 정보에만 사용된다.

섹션에는 두 가지 스타일이 있는데, plain 스타일은 iOS의 연락처 앱처럼 섹션의 헤더와 푸터는 섹션 분리기(inline separators)로 표시되고 스크롤을 할 때 해당 섹션 안에 있는 콘텐츠 위에 난다. Grouped 스타일은 시각적으로 뚜렷한 행 그룹을 표시하는 섹션이 있다. 섹션의 헤더와 푸터는 콘텐츠 위에 나타나지 않는다.

|                         Plain Style                          |                        Grouped Style                         |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
| ![tableview plain style](https://user-images.githubusercontent.com/16719527/72702013-96d59600-3b94-11ea-80a9-72f2531ffcaf.png) | ![tableview grouped style](https://user-images.githubusercontent.com/16719527/72702012-96d59600-3b94-11ea-9a84-5ce926ea536a.png) |

<br /><br />

<br />

### UICollectionView

<p align="center"><img width="250" alt="image-20200101171854278" src="https://user-images.githubusercontent.com/16719527/72704512-567a1600-3b9c-11ea-8e74-89565ef17399.png"></p>

위 예시에는 줄마다 음식이 쭉 배치된 것이 보인다. 모두 같은 크기를 가지고 한 줄에 세 개씩 있다. 여기에는 행이 없다. 테이블 뷰와 비슷하게 생겨서 행이 있다고 착각할 수 있는데 콜렉션 뷰는 행이 없다~!! 이 아이템들은 그냥 줄에서 줄로 배치된 형태다. (텍스트처럼 줄이 꽉차면 다음 줄로...) 만약 크기가 서로 다르면 이렇게 행이 있는 것 처럼 보이지 않을 것이다.

콜렉션 뷰의 모든 셀은 다 커스텀이다. 콜렉션 뷰는 기본 스타일을 가지고있지 않다.

<p align="center"><img width="250" alt="image-20200101172055867" src="https://user-images.githubusercontent.com/16719527/72704513-5712ac80-3b9c-11ea-884c-a76eb1073660.png"></p>

컬렉션 뷰도 섹션을 가질 수 있다. 컬렉션 유틸리티는 정보를 섹션으로 나누는 개념을 가지고 있다.

<br /><br />

### 어떻게 사용하는가?

객체 팔레트에서 드래그 해서 사용할 수 있다. 미리 패키징되어있는 MVC도 있는데, 만약 우리가 사용하려는 MVC 전체가 테이블 뷰가 되는 경우라면 이 테이블 뷰 컨트롤러를 드래그해서 사용하면 된다. 테이블 뷰 컨트롤러를 통째로 가져오는 것이다. 이는 모든 것이 준비된 뷰 컨트롤러라고 보면 된다. 만약 전체 뷰가 테이블 뷰이거나 콜렉션 뷰인 MVC를 사용한다면 여기 이 컨트롤러를 사용하면 편리하게 이용할 수 있다.

<br />

<br /><br />

## DataSource

### Where does the data com from?

API를 사용할 때 가장 중요하게 이해해야 할 것은 데이터가 어디서 오는지이다. 어떻게 테이블의 데이터를 얻을 것인지가 중요하다. MVC에서 뷰는 데이터를 가질 수 없다는 것을 배웠다. 따라서 테이블 뷰에는 변수로 데이터를 설정할 수 없다. 데이터를 그냥 제공할 수 없기 때문에 테이블 뷰가 데이터를 요청해야 한다.

```swift
//UITableView
var dataSource: UITableViewDataSource
var delegate: UITableViewDelegate

//UICollectionView
var dataSource: UICollectionViewDataSource
var delegate: UICollectionViewDelegate
```

델리게이션과 같은 방식으로 데이터를 요청하게 된다. `dataSource`라는 변수를 이용해 데이터를 요청한다. `dataSource`의 타입은 프로토콜이고, 이 프로토콜에는 데이터를 요청하는 한 뭉치의 메소드가 있다. 데이터를 달라고 계속적으로 요청한다.

테이블 뷰와 컬렉션 뷰는 또 다른 변수인 `delegate`를 가지고 있다. `dalegate`는 데이터가 어떻게 보이는지를 제어한다. 컬렉션 뷰와 테이블 뷰에서 무엇이 보여지는지가 아닌 어떻게!!! 설정되는지를 관여한다.

이 두 변수를 컨트롤러에 설정해야한다. 미리 패키징된 컨트롤러를 드래그하면 이 변수들은 자동적으로 설정된다. 뷰만 드래그해서 가져왔다면 반드시 이 두 변수를 설정해주어야 한다.

<br /><br />

### UITableView/CollectionViewDataSource protocol

이 프로토콜은 많은 메소드를 가지고 있다. 이 중 세 가지 매우~!! 중요한 메소드가 있다. 바로 데이터 요청하는 메소드다.

이 세 가지에 대해서 알아보자. 이 세 가지는 테이블 뷰와 컬렉션 뷰에서 거의 같아서 테이블 뷰의 메소드로 보여주겠다. *(테이블 뷰 row - 콜렉션 뷰 item)*

```swift
func numberOfSections(in tableView: UITV) -> Int
//위에서 섹션으로 테이블을 나눌 수 있다고 했지? 이 섹션이 몇 개가 있는지 알려준다. 대부분의 경우 섹션은 하나일 것이다. //이 메소드를 구현하지 않는다면 섹션은 기본값으로 1이 된다. 모든 정보가 하나의 큰 세션에 담긴다.


func tableView(_ tv: UITV, numberOfRowsInSection section: Int) -> Int
//한 섹션에 몇 개의 행이 있는지 묻는 메소드. 각각의 섹션에 몇 개의 행을 가지고 있냐고 물어본다.
//컬렉션 뷰의 경우에는 이 섹션에 아이템이 몇 개 있는지 묻는다.


//중요~!!!!!!!!!
func tableView(_ tv: UITV, cellForRowAt indexPath: IndexPath) -> UITableViewCell
//각 행에 또는 각 아이템에 데이터를 받아오는 것이다. 이 메소드는 꽤 유동적이다.
//왜냐하면 테이블 뷰나 컬렉션 뷰에 보여줄 데이터는 꽤나 복잡할 수 있기 때문이다. 따라서 자세히 보자~!!!
//IndexPath는 매우 작은 구조체로 섹션과 행 또는 섹션과 아이템을 가지고 있다. 섹션과 행을 합쳐서 나타내는 방법이다.
//따라서 컬렉션 뷰와 테이블 뷰는 매우 간단히 이 섹션에 대한 데이터를 달라고 요청할 수 있고, 이 메소드는 IndexPath를 제공한다.
//IndexPath에 대해 특별한 것은 없다. 그저 변수로 행, 아이템, 섹션을 가지고 있다.(section, row, item)
```

<br /><br />

### Putting data into the UITable/CollectionView

`tableView(cellForRowAt:)` 메소드는 UI 뷰를 반환한다. 이는 UI 뷰의 서브클래스다. 이 뷰는 행을 그리는데 사용된다. 매우 간단하지만 이 부분이 약간 복잡하다. cell reuse에 대한 설명은 아래에 적겠음~!

```swift
func tableView(_ tv: UITV, cellForRowAt, indexPath) -> UITableViewCell {
  let cell = tv.dequeueReusableCell(withIdentifier: "MyCellId", for: indexPath)
  //withIdentifier는 원하는 프로토타입의 identifier를 작성하면 되고,
  //indexPath는 현재 머물러 있는 위치를 나타낸다. 왜냐하면 그 위치에 해당하는 셀을 요청했기 때문이다.
}
```

<br /><br />

<br />

## Cell

### Cell Reuse

위 코드에 있는 `dequeueReusableCell`는 무엇일까? 우선 셀 재사용에 대해 이해해보자.

만약 만 개의 정보가 있는 음악 라이브러리 테이블 뷰라고 가정해보자. 그럼 각각의 앨범 커버, 아트 등 만 개의 뷰를 가지고 있을 것이다. 만 개의 UI 뷰를 갖는다는 것은 매우 비효율적인 일이다. 따라서 실제 테이블 뷰는 화면에 보여지는 행만 UI 테이블 뷰 셀을 생성한다. 우리가 스크롤해서 어떤 셀이 맨 위로 가게 되어 더 이상 뷰에 보이지 않으면 이 셀은 재사용되어 다시 맨 아래에 놓여지게 된다. 따라서 스크롤을 하는 동안 반복적으로 뷰는 재사용된다. 효율성을 위한 타당한 방법이다.

만약 어떤 정보가 맨 위로 가서 더 이상 뷰에 보여지지 않게 되면 이 정보를 보여주던 셀은 즉 UI 뷰는 `UITableViewCell`은 재사용 풀로 가서 `dequeueReusableCell` 메소드를 통해 셀을 가져오게 된다.

<br /><br />

### Cell Creation

만약 재사용할 수 없는 셀이 없다면 어떻게 할까? 테이블이 막 스크린에 띄워진 상태라면? 그 경우 재사용할 수 있는 셀은 아무것도 없을 것이다. 그럼 `dequeueReusableCell`에 필요한 셀들은 어디서 오게될까? 정답은 스토리보드에서 만든 프로토타입을 복사해서 생성하게 된다. 레이블, 버튼, 이미지 등을 사용해 셀의 모양을 만들고 오토 레이아웃 등을 사용해 스토리보드에 셀을 만들어 놓으면 셀이 필요할 때마다 스토리보드에서 복사본을 만들고 이를 재사용하게 된다. 이게 셀이 동작하는 방법이다.

<br />

<p align="center"><img width="680" alt="image-20200101175126032" src="https://user-images.githubusercontent.com/16719527/72704832-2bdc8d00-3b9d-11ea-86a6-75465288d991.png"></p>

위 사진에서는 두 개의 프로토 타입이 있다. 첫 번째는 그냥 기본 셀이고, 두 번째는 이미지, 카테고리, 음식 이름과 자세한 설명을 가지고 있는 복잡한 셀이다.

각각에 대해 스타일을 설정하고 `identifier`를 준다. 타입은 `String`이고, 재사용 가능한 셀을 가져올 때 식별을 위해 사용한다. 우리는 어떤 셀을 재사용하길 원하는지 말하고, 재사용할 셀이 없으면 여기에 있는 셀을 복사해서 만든다. 위에 보이는 모든 이미지와 텍스트가 다 복사된다.

<br /><br />

### Implementing cellForRowAt

**`cellForRowAt`을 사용할 때 주의할 점!!!!!!!!!!!!!! 셀들이 재사용됐다는 사실은 멀티스레딩에 큰 영향을 준다!!!!!! 왜냐하면 행에 네트워크를 통해 로딩해오는 이미지같은 정보가 있다면, 다른 스레드에서 가져오는 작업을 시작하고 인터넷에서 데이터를 가져오는 작업을 할 것이다. 이는 시간이 오래 걸릴 수 있다. 그 사이 사용자는 손가락으로 스크롤 한다면 그 셀은 화면에서 사라지고 해당 셀은 재사용된다. 그럼 앱의 화면에서는 이미 다른 셀을 보여주는데 재사용 되기 전에 요청한 이미지가 오게 될 수 있다. 주의하지 않으면 다른 셀의 이미지를 새로운 셀에 보여주게 될 수 있다. 따라서 이미지를 받아오면 이것을 요청한 셀이 여전히 화면에 보여지고 있는지 확인해야 한다. 멀티스레딩에서 항상 다루는 것이다. 다시 돌아온 데이터에 대해서는 항상 이를 요청한 것이 여전히 있는지를 확인하는 작업이 필요하다. 셀에서는 특히 많이 일어나는데 셀은 끊임없이 보여졌다가 재사용되다가를 반복하기 때문이다.**

<br />

가끔 아래와 같은 코드도 보게 될 것이다.

```swift
func tableView(_ tv: UITV, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  let prototype = decision ? "FoodCell" : "CustomFoodCell"
  //String을 타입으로 가지는 prototype 상수를 decision으로 할당한다. 이때 decision은 무슨 IndexPath를 가지느냐에 달려 있다.
  //예를 들면 어떤 섹션에 있는지 혹은 어떤 데이터가 있는지에 따라서 달라지게 된다.
  //예를 들어, 어떤 과일은 자세한 정보를 가지고 있는데 어떤 과일은 가지고 있지 않는다고 하면, 이런 정보가 있을 경우에 CustomFoodCell를 사용하고, 그렇지 않은 경우에 FoodCell을 사용한다.
  //어떻게 decision을 내리는지 알겠지?! 어떤 것이 적절한지 결정하면 이에 맞는 셀을 큐에서 꺼내게된다.
  let cell = tv.dequeueReusableCell(withIdentifier: prototype, for: indexPath)
}
```

<br />

cell을 불러왔다고 해보자. 그럼 이제 이 셀이 데이터를 가질 수 있도록 어떻게 설정할까? 이는 이 셀이 기본적인 네 가지 스타일 중 어떤 것에 해당하는 지에 달려있다. (서브타이틀, 왼쪽 디테일, 오른쪽 디테일, 기본 셀, 아니면 커스텀 셀!) 기본으로 제공하는 스타일 중 하나라고 하면, tableViewCell은 기본적으로 몇 가지 아울렛을 가지고 있다. 

```swift
textLabel //우리가 설정 가능한 자세한 텍스트.
detailTextLabel
imageView //셀의 왼쪽에 나타나는 작은 이미지.

func tableView(_ tv: UITV, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  let prototype = decision ? "FoodCell" : "CustomFoodCell"
  let cell = tv.dequeueReusableCell(withIdentifier: prototype, for: indexPath)
	
  //기본 스타일 중 하나라고 하면~!!
  cell.textLabel?.text = food(at: indexPath)
  cell.detailTextLabel?.text = emoji(at: indexPath)
}
```

<br />

<br />

### Custom UITableViewCell subclass

기본으로 주어진 아울렛 말고 커스텀 셀을 만들고 싶다면, UITableViewCell의 커스텀 서브 클래스를 사용해야 한다.

<p align="center"><img width="680" alt="image-20200101181254120" src="https://user-images.githubusercontent.com/16719527/72704855-3434c800-3b9d-11ea-96ed-35966a446c08.png"></p>

아울렛이 필요하다. 테이블 뷰 컨트롤러에 아울렛을 넣으려고 할 수도 있지만 이는 불가능하다. 왤까? 왜냐하면 이 셀들이 100개일 수도 있기 때문이다. 우리가 복사해서 사용하는 프로토타입이기 때문에 하나의 아울렛만 있다면 모두에게 적용이 불가능하다. 아울렛 Collection을 배열로 만들 수도 있지만 비효율적이다. 왜냐하면 셀은 계속 재사용되어야 하고 그럼 그 배열이 아주 복잡해질 것이기 때문이다. 따라서 컨트롤러에 아울렛을 놓을 수 없다.

<br />

따라서 UITableViewCell의 서브 클래스를 만들어 아울렛을 놓도록 하자~!!

```swift
class myTVC: UITableViewCell {
  //
  @IBOutlet var ...
}
```

`UITableViewCell`는 `cellForRowAt`이 리턴하는 것이다. 따라서 각각의 행은 자신만의  TableViewCell이 있고, TableViewCell은 자신만의 아울렛을 가지게 된다. 내 맘대로 커스텀 할 수 있다~!!! (기본형과 부제목도 자신만의 아울렛을 가진다. (텍스트 레이블, 디테일 텍스트 레이블, 이미지))

커스텀 `UITableViewCell` 서브 클래스를 생성할 때는 해당 프로토타입 행을 선택해 아이덴티티 인스펙터에서 반드시 해당 클래스를 설정해주어야 한다. 디폴트로 `UITableViewCell`로 되어있을 것이다.

<br />

`CellForRowAt`에 대해서 하나만 이야기하자면, 이 아울렛에 접근할 때는 `dequeueReusableCell`에서 돌아온 셀을 우리의 식별자로 타입 변환이 필요하다. 왜냐하면 그 셀은 `UITableViewCell`의 클래스이기 때문에 내 아울렛의 정보를 모르니깐! 따라서 서브클래스로 타입 변환을 해주어야한다.

```swift
func tableView(_ tv: UITV, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
  let prototype = decision ? "FoodCell" : "CustomFoodCell"
  let cell = tv.dequeueReusableCell(withIdentifier: prototype, for: indexPath)
	
	if let myTVCell = cell as? MyTVC {
    myTVCell.name = food(at: indexPath)
    myTVCell.emoji = emoji(at: indexPath)
  }
}
```

이제 아울렛에 내 데이터로 설정할 수 있다. `indexPath`를 받아서 모델을 보고 데이터를 가져온다.

**컬렉션 뷰는 기본으로 제공되는 스타일이 없기 때문에 반드시 커스텀 셀을 만들어서 사용해야 한다.**

<br />

<br /><br />

## Static Table View

가끔 테이블은 무작위적인 양의 데이터를 위치시키는데 사용하지 않고 기본적인 UI를 나타내는데만 사용될 수 있다. 이런 테이블은 고정된 양의 데이터를 가지고 있다. 보통 설정앱에서 사용되거나, 앱의 선호표를 나타낼 때 등 고정된 양의 정보를 보여주고 정보를 그룹으로 묶어 잘 정렬된 행으로 보여줄 때 사용한다. 따라서 이 때는 행과 섹션의 개수를 세는 등의 함수는 전혀 필요가 없다. 스토리보드에 직접 그리고, 컨트롤러에 행의 데이터를 직접 작성하면 된다. 왜냐하면 이는 복사되는 프로토타입이 아닌 고정된 것이다. 데이터가 고정된 값을 때문에 이 경우에는 컨트롤러로 직접 접근한다. 매우 간단하고 많이 사용된다.

정적 테이블 뷰를 사용하는 방법은 스토리보드의 테이블을 클릭하고, 인스펙터에서 설정을 `Static Cells`로 바꾸면 된다. 그리고 스타일은 항상 그룹화(grouped)되어 있다. 그 후 도큐먼트 아웃라인으로 가면 정적 섹션을 발견할 수 있다. 각각의 섹션에 접근이 가능하다. 테이블 뷰의 인스펙터에서 섹션의 개수를 정할 수 있고, 특정 섹션을 선택하면 섹션 인스펙터에서 행의 개수를 정할 수 있다. 버튼, 레이블 등을 드래그로 추가하고, 컨트롤러에 연결하면 된다.

<br /><br /><br />

## TableView's Segue

행을 클릭했을 때 세그웨이를 가지고싶다면? 두 가지 방법이 있다.

<br />

### 1. 행에서 Segue 하기

행을 클릭하면 이에 대한 세그웨이를 실행한다.

<br />

### 2. Detail Disclosure Accessory 이용하기

테이블 뷰 셀을 클릭하고 인스펙터로 가서 Accesory 항목의 Detail Disclosure을 선택하면 원 안에 있는 작은 i 표시를 얻을 수 있다. 여기서도 세그웨이 할 수 있다. 행과 다른 세그웨이가 가능하다.

<br />

이게 우리 도큐먼트 리스트라고 하면 특정 행을 클릭하면 해당 도큐먼트로 가게 될 것이다. 그리고 작은 i 버튼을 클릭하면 다른 MVC로 가게되겠지? 도큐먼트의 이름, 작성자, 마지막 작성 날짜 등을 바꿀 수 있는 화면으로 넘어가게 될 것이다. 즉, 도큐먼트에 관한 정보들이 있는 화면이다. 두 가지 모두에 대해 세그웨이를 설정할 수 있다.

<br />

세그웨이를 만드는 방법은 동일하다. 행에서 Ctrl을 누르고 드래그한다. 프로토타입 행도 가능하다. 정적인 프로토타입에서도 가능하다. Ctrl을 누르고 드래그하면, 메인 행에 대한 세그웨이를 설정하는 메뉴가 나온다. 필요한 show로 설정하고, `identifier`를 설정해준다. 그 후에 세그웨이를 `prepare` 하면 된다.

<br />

테이블 뷰의 흥미로운 점이 여기 있다~!!! 왜냐하면 프로토타입에서 세그웨이를 하면 세그웨이가 일어나는 곳이 어떤 행인지 알 수 있기 때문이다.

```swift
func prepare(for segue: UIStoryboardSegue, sender: Any?) {
  //중요한 것은 sender 인수다. 버튼을 클릭하면 sender가 버튼 타입이었던 것처럼 행을 클릭하면 그 행은 UITableViewCell이다.
  //UITableViewCell에서 어떤 indexPath 인지 알아내고 이를 통해 올바른 모델 데이터를 얻어 갈 곳으로 준비를 할 수 있다.
  if let identifier = segue.identifier {
    switch identifier {
      case "XyzSegue":
      case "AbcSegue":
      //UITableViewCell로 타입을 변환하거나 서브클래스가 있다면 그것으로 바꿔준다.
      	if let cell = sender as? MyTVC {
          let indexPath = tableView.indexPath(for: cell)
          //이 메소드가 아주 중요~!!
          //indexPath(for: cell)은 indexPath, 즉 선택된 셀에 대한 행과 섹션을 반환한다.
          //여기서 Any는 인수로 받지 않는다는 것을 알아둬야 한다. (for: sender)도 안된다. 타입을 UITableViewCell로 맞춰주어야한다.
          
          let seguedToMVC = segue.destination as? MyVC {
            seguedToMVC.publicAPI = data[indexPath.section][indexPath.row]
            //indexPath를 얻고 나면 세그웨이가 향하는 MVC로 가는 도착지를 얻기위한 일반적인 작업을 한다.
            //모델에서 해당 섹션과 행에 대해 데이터를 받아, 세그웨이가 향하는 MVC의 공개 API에 할당한다.
          }
        }
      default: break
    }
  }
}
```

<br />

<br /><br />

## CollectionView's Segue

컬렉션 뷰 아이템은 타겟 액션이 보통 가장 좋은 선택이다. 컬렉션 뷰 델리게이트는 아이템이 터치될 때마다 호출되는 메소드를 가지고 있다.

```swift
func collectionView(collectionView: UICV, didSelectItemAtIndexPath indexPath: IndexPath)
```

여기서는 `performSegue`를 사용하고 보통처럼 `segue`를 `prepare`하면 된다. 

여기서 `performSegue`를 할 때는 `Any`의 값을 가지는 `sender` 변수를 우리가 원하는 값으로 설정하고 이를 `prepare` 하는데 사용하게 될 것이다.

`UITableView`에서도 사용할 수 있다. 이미 인덱스 경로를 얻었으므로 셀에 대한 인덱스 경로를 위한 작업은 하지 않아도 된다.

<br /><br />

<br />

## What if your Model Changes?

테이블 뷰와 컬렉션 뷰를 사용할 때는 모델이 바뀌었을 때 무슨 일이 일어나야 하는지 생각해보자. 예를 들면 테이블 뷰로 노래 목록을 보여주고 새로운 노래를 추가하면 테이블 뷰는 업데이트 되어야 한다. 어떻게 할까?

<br />

테이블 뷰와 컬렉션 뷰에 reloadData라는 메소드가 있다.

```swift
func reloadData()
//이는 테이블 뷰가 가지고 있는 모든 메소드를 다시 호출한다.
//(예를 들면 행과 섹션의 개수를 주는 메소드 혹은 테이블 뷰 셀을 제공하는 메소드 등)
//모든 보여지는 셀에 대해서 테이블을 다시 로드한다. 약간 무거울 수 있는 메소드다.
//하지만 cellForRow(at indexPaths:)는 보여지는 셀에 대해서만 호출되기 때문에 나쁘지는 않다.

func reloadRows(at indexPaths: [IndexPath], with animation: UITableViewRowAnimation)
//indexPath에 대한 배열을 줘서 그 행들만 다시 로드할 수 있다.
```

모델이 바뀌면 테이블을 업데이트해야 하기 때문에 오랜 시간동안 데이터나 콜렉션 뷰가 모델과 싱크가 맞지 않는 상황은 좋지 않다. 싱크가 맞춰진 상태를 유지해야 한다.

<br />

<br /><br />

## Controlling the height of rows in a Table View

행들의 높이는 어떻게 결정될까? 높이를 설정하는 세 가지 방법이 있다.

1. **테이블 뷰의 변수인 `rowHeight`를 이용하기**

   스토리보드에서도 접근 가능하다. 항상 그 높이가 되도록 행을 고정시킨다. 기본적인 행에 대해 작동한다.

2. **오토 레이아웃을 이용해 행 높이를 설정하기**

   이는 복잡한 셀에 해당한다. 예를 들면 많은 정보 를 가지고있는 과일이 있다면 셀의 크기는 커야한다. 따라서 오토 레이아웃을 제대로 설정하면 일반적인 뷰가 행의 높이를 조절한다. 일반적인 `UIView`를 행 내부에서 상위 뷰로 놓고 그 안에 모든 것을 집어넣는다. 그리고 제약조건들을 설정한다. 행의 가장자리에 그 뷰를 드러내도록 제약을 설정한다. 그러면 테이블 뷰가 나타날 때 오토 레이아웃으로 인해 행이 어떤 크기를 가져야할지 알 수 있다.

   이를 위해 `rowHeight` 변수를 숫자로 설정하는 대신 `UITableViewAutomaticDimension`으로 설정해야한다. 이 변수는 오토 레이아웃에 관련된 일을 하라고 알려주는 특별한 변수고 사용하기에 비싸다(expensive). 음악 리스트에서 이를 만 번 쯤 하기는 어렵다... 

   `estimatedRowHeight` 변수가 있다. 이는 테이블 뷰에게 셀이 스크린에 있지 않으면 어느정도 크다고 가정하라고 알려준다. 오토레이아웃을 적용하지 않는다. 이거도 고려해보아야 하는 것..!

3. **`tableView(heightForRowAt indexPath:)` 구현하기**

```swift
func tableView(heightForRowAt indexPath: IndexPath) -> CGFloat
```

해당 인덱스를 가진 행의 높이를 리턴한다. 오토레이아웃 없이 효과적으로 높이를 계산할 수 있다면 이는 좋은 방법이 될 것~!! 만약 어떤 셀은 어떤 높이를 가지고, 다른 하나는 또 다른 높이를 가진다면 이 메소드를 통해 올바른 높이를 리턴할 수 있다. 아니면 모든 셀이 같은 높이를 가져도 계산해서 높이를 반환하는 것이 더 편하다면 이 메소드를 사용하는 것이 좋다.

컬렉션 뷰의 경우 다른 점은 `sizeForItemAt`이 있다는 점이다. 높이와 너비를 지정해준다.

```swift
func collectionView(_ collectionView: UICollectionView, layout collectionViewLayout: UICollectionViewLayout, sizeForItemAt indexPath: IndexPath) -> CGSize
//collectionViewLayout은 기본으로 레이아웃을 공급하는 인수다.
//만약 collectionViewLayout을 제공한다면 다른 종류의 레이아웃도 가능하다. 기본은 UICollectionViewFlowLayout
```

<br /><br />

<br />

## Header

헤더는 어떻게 넣을까?

### TableView

```swift
func tableView(_ tv: UITV, titleForHeaderInSection section: Int) -> String?
//리턴한 스트링을 헤더에 넣는다.

//UIView를 반환하는 메소드도 있다. 헤더가 UIView가 되도록 만들어서 그 안에 원하는 것을 넣을 수 있다.
```

<br />

### CollectionView

테이블 뷰 보다 섹션 헤더를 넣기가 좀 더 어렵다. 왜냐하면 컬렉션 뷰의 헤더 뷰는 재사용이 가능하기(reusable) 때문이다. 셀처럼..! 이렇게 만들어진 이유는 컬렉션 뷰는 많은 섹션들이 있는데 비해 테이블 뷰에는 천 개의 섹션이 있는 것이 거의 드물다. 컬렉션 뷰는 섹션이 많을 수도 있다.

셀을 사용하는 방식과 매우 비슷하다. 헤더가 활성화되기 위해서는 컬렉션 뷰를 클릭하고 인스펙터에서 `Section Header` 또는 `Section Footer`를 선택한다. 그럼 스토리보드에 드래그 할 수 있는 작은 곳이 생기는데 이는 프로토 타입으로 모든 섹션 헤더에 반복될 것이다.

헤더에는 원하는 모든 것을 넣을 수 있지만, 서브 클래스를 만들어야 한다. 그 클래스는 `UICollectionReusableView` 라고 부른다. `UICollectionViewCell`이 아니여~!!! 서브클래스를 만들고 거기에 아울렛을 넣는다.

그리고 아래의 메소드를 구현한다.

```swift
func collectionView(_ collectionView: UICollectionView, 
                    viewForSupplementaryElementOfKind kind: String, 
                    at indexPath: IndexPath
                   ) -> UICollectionReusableView
//selfRowAtIndexPath와 비슷하지만 이 메소드는 헤더를 위한 것이다.
//이 메소드 안에서 dequeueReusableSupplementaryView(ofKind:withReuseIdentifier:for:)를 사용한다.
//IndexPath에 대한 식별자를 사용한다. 프로토타입에 대한 복사본을 만들어서 스크린에서 스크롤 할 때 이 헤더를 재사용한다.
```

헤더와 푸터는 컬렉션 뷰에만 해당한다.

<br /><br />

<br />

## Row delete

### tableView(canEditRowAt:)

해당 행을 삭제해도 될지를 결정하는 메소드.

기본 값이 true이기 때문에 모든 행이 삭제되어도 괜찮으면 이 메소드를 구현할 필요가 없다. 이 메소드는 삭제가 일어나는 것을 막는 유일한 방법이다.

하지만 이 메소드만으로는 삭제가 이루어지지 않는다. 왜냐면 모델에 대한 삭제 작업도 이루어져야하기 때문~!!

<br />

### tableView(commit editingStyle:)

`editingStyle`은 삭제 혹은 삽입을 의미한다. 그것을 모델에 적용하는 메소드다. 삽입은 추가에 관한 UITableView UI를 제공하는데 이미 바 버튼 액션으로 만들었다면 구현할 필요가 없다. 그럼 삭제 작업만 구현하면 된다. 모델에서 삭제하는 작업을 한다.

이때 주의할 점은 모델과 테이블이 반드시 동기화되어야 한다는 것이다. 만약 서로 갯수가 맞지 않으면 앱이 에러가 나고 crash될 것이다. 모델을 어떻게 업데이트하냐에 따라서 데이터를 reload하거나 행만 삭제하거나 하면 된다. 

<br /><br />

<br />

## Other methods

<p align="center"><img width="560" alt="image-20200102133035590" src="https://user-images.githubusercontent.com/16719527/72704867-3dbe3000-3b9d-11ea-91ad-ded6e6888958.png"></p>

행을 삭제하기 위한 스와이프

행을 이동시키는 것

특정한 행으로 이동하는 것 등등

<br />

<br />

<br />

-----

http://labs.brandi.co.kr/2018/05/02/kimjh.html