# UIPickerView 사용하기



```swift
class ViewController: UIViewController, UIPickerViewDelegate, UIPickerViewDataSource {

    let MAX_ARRAY_NUM = 10
    let PICKER_VIEW_COLUMN = 1
  	    let PICKER_VIEW_HEIGHT:CGFloat = 80
    var imageArray = [UIImage?]()
    var imageFileName = [ "1.jpg", "2.jpg", "3.jpg", "4.jpg", "5.jpg", "6.jpg", "7.jpg", "8.jpg", "9.jpg", "10.jpg" ]
    
    @IBOutlet var pickerImage: UIPickerView!
    @IBOutlet var lblImageFileName: UILabel!
    @IBOutlet var imageView: UIImageView!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        // Do any additional setup after loading the view.
        
        for i in 0 ..< MAX_ARRAY_NUM {
            let image = UIImage(named: imageFileName[i])
            imageArray.append(image)
        }
        
        lblImageFileName.text = imageFileName[0]
        imageView.image = imageArray[0]
    }


    func numberOfComponents(in pickerView: UIPickerView) -> Int {
        return PICKER_VIEW_COLUMN
    }
  //피커 뷰의 컴포넌트의 수를 설정하는 델리게이트 메소드
  //피커 뷰의 컴포넌트는 피커 뷰에 표시되는 열의 개수(세로)
  
  
    func pickerView(_ pickerView: UIPickerView, rowHeightForComponent component: Int) -> CGFloat {
        return PICKER_VIEW_HEIGHT
    }
  //피커 뷰 룰렛의 세로 높이 값을 변경하는 메소드
  //rowHeightForComponent를 인수를 가지는 델리게이트 메소드
  
    
    func pickerView(_ pickerView: UIPickerView, numberOfRowsInComponent component: Int) -> Int {
        return imageFileName.count
    }
  //피커 뷰의 개수 설정
  //numberOfRowsInComponent 인수를 가지는 델리게이트 메소드
  //이 값은 피커 뷰의 해당 열에서 선택할 수 있는 행의 개수(데이터의 개수)를 의미

    
    func pickerView(_ pickerView: UIPickerView, titleForRow row: Int, forComponent component: Int) -> String? {
        return imageFileName[row]
    }
  //피커 뷰의 각 Row의 타이틀 설정
  //titleForRow 인수를 가지는 델리게이트 메소드
  //피커 뷰에게 컴포넌트의 각 열의 타이틀을 String 값으로 넘겨줌
  
  
    func pickerView(_ pickerView: UIPickerView, viewForRow row: Int, forComponent component: Int, reusing view: UIView?) -> UIView {
        let imageView = UIImageView(image: imageArray[row])
        imageView.frame = CGRect(x: 0, y: 0, width: 100, height: 100)
        
        return imageView
    }
  //피커 뷰의 각 Row의 view 설정
  //viewForRow 인수를 가지는 델리게이트 메소드
  //피커 뷰에게 컴포넌트의 각 열의 뷰를 UIView 타입의 값으로 넘겨줌
  //여기서는 UIImageView를 넘겨주었음
  
  
    func pickerView(_ pickerView: UIPickerView, didSelectRow row: Int, inComponent component: Int) {
        lblImageFileName.text = imageFileName[row]
    }
  //피커 뷰가 선택되었을 때 실행
  //didSelectRow 인수를 가지는 델리게이트 메소드
  //컴포넌트가 여러개일 때 component 인자는 왼쪽부터 순서대로 0, 1, 2, ... 의 값을 가짐
}
```
