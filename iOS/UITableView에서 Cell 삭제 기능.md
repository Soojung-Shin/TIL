# UITableView에서 Cell 삭제 기능

### 1. 스와이프 이용

📋 TableViewController

```swift
  override func tableView(_ tableView: UITableView, commit editingStyle: UITableViewCell.EditingStyle, forRowAt indexPath: IndexPath) {
    if editingStyle == .delete {
      //삭제 작업
      tableView.deleteRows(at: [indexPath], with: .fade)
    } else if editingStyle == .insert {
      // Create a new instance of the appropriate class, insert it into the array, and add a new row to the table view
    }    
  }
```

<br />

스와이프하면 나오는 빨간색 칸 속 문자열의 초기값은 "Delete" 입니다. 이것을 원하는 문자열로 바꾸기 위해서 하단의 메소드를 추가해줍니다.

```swift
    override func tableView(_ tableView: UITableView, titleForDeleteConfirmationButtonForRowAt indexPath: IndexPath) -> String? {
        return "삭제"
    }

```

<br />

<p align="center"><img width="301" alt="image-20191121163627708" src="https://user-images.githubusercontent.com/16719527/72658199-ec713d80-39f0-11ea-8001-530439d3a258.png"></p>

<br />

<br />



### 2. Navigation에서 Edit 버튼 이용

📋 TableViewController

```swift
    override func viewDidLoad() {
        super.viewDidLoad()

        // Uncomment the following line to preserve selection between presentations
        // self.clearsSelectionOnViewWillAppear = false

        // Uncomment the following line to display an Edit button in the navigation bar for this view controller.
        //self.navigationItem.rightBarButtonItem = self.editButtonItem
    }

```

`UITableViewController`를 서브클래스로 하는 테이블 뷰 컨트롤러를 만들면 자동으로 `viewDidLoad`메소드에 하단에 설정들이 주석 처리되어 추가되어있습니다.

여기서 밑에있는 코드를 선택해 주석을 풀어주면 자동으로 Edit 버튼이 생성됩니다.

```swift
self.navigationItem.leftBarButtonItem = self.editButtonItem
```

`rightBarButtonItem`을 `leftBarButtonItem`으로 수정해주었습니다.

<br />

<p align="center"><img width="298" alt="image-20191121164429041" src="https://user-images.githubusercontent.com/16719527/72658202-fc891d00-39f0-11ea-83a6-601eeb6cb70d.png"></p>

