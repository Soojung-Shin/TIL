## Outlet이란?

다른 객체를 참조하는 객체의 속성이다. 예를들어 `IBOutlet`은 `Interface Builder`와 연결을 위한 객체의 속성이라고 볼 수 있다.
<br><br>



## IBOutlet 연결 방법 (Storyboard)

`Interface Builder`에 있는 인스턴스를 코드의 프로퍼티에 연결한다.

`ViewController`에 `@IBOutlet` 어노테이션 추가 후 변수를 선언한다.
<br>


1. `Storyboard` 파일에서 `View Controller` 오른쪽 버튼 클릭 후 해당 `Outlet`을 연결할 인스턴스로 드래그해 연결
2. `Interface Builder`의 `Outlet Inspector`를 통해 연결
3. `Controller` 윈도우를 띄운 후(옵션키 + 클릭) `Interface Builder`에서 코드로 끌어당겨 연결
<br>


**주의!**

연결한 후에 변수 이름을 변경하고 싶을 때는 그냥 변경하면 `IBOutlet`의 연결이 망가질 수 있다. `Controller`의 변수에서 `refactor` -> `rename`으로 이름을 변경해주면 `IBOutlet` 연결부의 이름도 자동으로 변경된다.



