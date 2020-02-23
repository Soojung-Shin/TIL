# Transforming operators

## Transforming elements

### toArray

observable의 각각의 element들을 배열로 변환한다.

<br />

<p align="center"><img width="500" alt="image-20200216165208376" src="https://user-images.githubusercontent.com/16719527/75103819-bd7b6680-5643-11ea-897d-b639257151c8.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5)
    .toArray()
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
[1, 2, 3, 4, 5]
*/
```

<br /><br />

<br />

### map

Swift standard library의 map과 같은 역할을 하는데, observable에서 작동한다는 것!

<br />

<p align="center"><img width="500" alt="image-20200217130954660" src="https://user-images.githubusercontent.com/16719527/75103820-c10eed80-5643-11ea-8187-35d916196e30.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

let formatter = NumberFormatter()
formatter.numberStyle = .spellOut

Observable<Int>.of(1, 2, 3, 4, 5)
    .map { formatter.string(for: $0) ?? "" }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
one
two
three
four
five
*/
```

<br /><br /><br />

## Transforming inner observables

### flatMap

평평하게! observable의 observable value를 변환한 다음 target observable로 만들어 평평하게 만든다........

<br />

<p align="center"><img width="500" alt="image-20200217135217787" src="https://user-images.githubusercontent.com/16719527/75103823-c2d8b100-5643-11ea-83c9-81380d2520cf.png"></p>

<br />Source observable은 value라는 프로퍼티를 가진 객체인데, 이 value는 그 자체가 Int 타입의 observable이다. (🤔 observable이 observable을 방출하는건가)

`O1`이 `flatMap`에 들어가서 새로운 observable이 된다. 그래서 observable이 (평평해져서) target observable이 되고, 구독자에게 element를 보낸다.

source observable의 `O2`가 `flatMap`에 의해서 target observable로 평평해진다. 

`O3`은 `flatMap`에 의해서 수신되고, value 3을 내보내고 평평해진다.

<br />

`flatMap`은 sorce observable의 각 요소마다 하나씩 만들어진 각각의 observable을 모두 유지하고 있다.

<br /><br />

```swift
let disposeBag = DisposeBag()

let laura = Student(score: BehaviorSubject(value: 80))
let jackson = Student(score: BehaviorSubject(value: 50))

let student = PublishSubject<Student>()

student
    .flatMap { $0.score }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

student.onNext(laura)
laura.score.onNext(90)

/*
80
90
*/

student.onNext(jackson)
jackson.score.onNext(10)

/*
50
10
*/

laura.score.onNext(100)

/*
100
*/
```

<br />

- flatMap을 사용하면 element를 방출하고, 완료하는 평평한 observable 인스턴스를 만든다.
- 일부 비동기 작업을 수행하고 observable이 완료될 때까지 대기시키고, 체인의 나머지 작업들을 수행하도록 한다.

<br /><br /><br />

### flatMapLatest

flatMap에서 가장 최근의 observable만 유지한다. map과 switchLatest(가장 최근의 observable로부터 온 값만 내보내고, 이전의 observable은 unsubscribe 함)을 합쳐놓은 것! 

<br />

<p align="center"><img width="500" alt="image-20200217142704481" src="https://user-images.githubusercontent.com/16719527/75103824-c3714780-5643-11ea-93e8-045159dd74ff.png"></p>

<br />자동으로 가장 최근의 observable만 switch 하고, 이전의 observable들은 unsubscribe한다.

<br />

```swift
let disposeBag = DisposeBag()

let laura = Student(score: BehaviorSubject(value: 80))
let jackson = Student(score: BehaviorSubject(value: 50))

let student = PublishSubject<Student>()

student
    .flatMapLatest { $0.score }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

student.onNext(laura)
laura.score.onNext(90)

/*
80
90
*/

student.onNext(jackson)
jackson.score.onNext(10)

/*
50
10
*/

laura.score.onNext(100)
//암것도 출력안됨! laura는 unsubscribe 됐음
```

<br />

`flatMapLatest`는 네트워킹 작업에서 주로 사용된다. 만약에 자동 검색을 구현하고 있다고 생각해보면 사용자가  `s, w, i, f, t` 를 입력했을 때, 새로운 검색 작업을 시작하고, 이전의 결과들은 무시해야한다. `flatMapLatest`를 사용해 구현할 수 있다.

<br />

<br /><br />

## Observing events

observable의 event를 observable로 변환한다. 예를 들어 observable 프로퍼티를 가지는 observable을 제어할 수 없는 상황에서 외부 시퀀스가 종료되지 않도록 에러 이벤트를 처리하고 싶을 때..!

<br />

```swift
enum MyError: Error {
    case anError
}

let disposeBag = DisposeBag()

let laura = Student(score: BehaviorSubject(value: 80))
let jackson = Student(score: BehaviorSubject(value: 50))

let student = BehaviorSubject<Student>(value: laura)

let studentScore = student.flatMapLatest { $0.score }

studentScore
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

laura.score.onNext(90)
laura.score.onError(MyError.anError)
laura.score.onNext(100)

/*
80
90
Unhandled error happened: anError
 subscription called from:
*/

student.onNext(jackson)
//아무것도 출력되지 않는다.
```

<br /><br />

### materialize

observable에서 방출되는 각 이벤트를 observable로 감싼다.

<br />

<p align="center"><img width="500" alt="image-20200217145155355" src="https://user-images.githubusercontent.com/16719527/75103825-c3714780-5643-11ea-8725-0af1b6e9712f.png"></p>

<br />

```swift
enum MyError: Error {
    case anError
}

let disposeBag = DisposeBag()

let laura = Student(score: BehaviorSubject(value: 80))
let jackson = Student(score: BehaviorSubject(value: 50))

let student = BehaviorSubject<Student>(value: laura)

let studentScore = student.flatMapLatest { $0.score.materialize() }
//studentScore는 materialize()에 의해서 Observable<Event<Int>> 타입이 된다.

studentScore
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

laura.score.onNext(90)
laura.score.onError(MyError.anError)
laura.score.onNext(100)

/*
next(80)
next(90)
error(anError)
*/

student.onNext(jackson)

/*
next(50)
*/
```

`studentScore`가 `error` 이벤트로 종료되었지만, `student` observable은 종료되지 않았다. 그래서 새로운 학생을 변경했을 때, `score`를 성공적으로 받아서 출력할 수 있다.

<br /><br /><br />

### dematerialize

materialized observable을 다시 원상태로 바꾼다.

<br />

<p align="center"><img width="500" alt="image-20200217150550473" src="https://user-images.githubusercontent.com/16719527/75103826-c409de00-5643-11ea-964f-b1f5637527d9.png"></p>

<br />

```swift
enum MyError: Error {
    case anError
}

let disposeBag = DisposeBag()

let laura = Student(score: BehaviorSubject(value: 80))
let jackson = Student(score: BehaviorSubject(value: 50))

let student = BehaviorSubject<Student>(value: laura)

let studentScore = student.flatMapLatest { $0.score.materialize() }

studentScore
    .filter {
        guard $0.error == nil else {
            print($0.error!)
            return false
        }
        return true
    }
    .dematerialize()
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

laura.score.onNext(90)
laura.score.onError(MyError.anError)
laura.score.onNext(100)

student.onNext(jackson)

/*
80
90
anError
50
*/
```

<br /><br />

<br />

<br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.