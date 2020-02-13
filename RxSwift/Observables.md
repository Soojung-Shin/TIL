# Observables

`Observable<T>` 클래스는 데이터의 immutable한 변화값들을 이벤트의 시퀀스로 비동기적으로 만들어준다. 다른 객체나 이용자가 시간에 따라 바뀌는 이벤트나 값을 구독(subscribe)할 수 있게 만들어준다.

`Observable<T>` 클래스를 사용하면 하나 이상의 옵저버가 어떤 이벤트에 실시간으로 반응해 앱의 UI를 업데이트하거나, 새롭게 들어오는 데이터를 처리하고 활용할 수 있습니다.

<br />

`Observable<T>`가 채택하고 있는 [`ObservableType` 프로토콜](https://github.com/ReactiveX/RxSwift/blob/master/RxSwift/ObservableType.swift)은 매우매우 간단하다. `Observable` 은 세 가지 이벤트를 만들어낼 수 있다.

- **next**

  가장 최신 값이나 다음 값을 전달하는 이벤트다. 옵저버가 값을 받는다. `Observable` 는 종료 이벤트가 발생할 때 까지 이런 이벤트를 무한대로 만들어낼 수 있다.

- **completed**

  이벤트 시퀀스가 성공적으로 종료되었을 때 발생하는 이벤트다. `Observable`의 라이프사이클이 정상적으로 종료되었다는 의미로 더이상 이벤트를 만들어내지 않는다.

- **error**

  `Observable` 이 에러로 종료되었을 때 발생하는 이벤트다. 더 이상 이벤트를 만들어내지 않는다.

<br /><br />

<p align="center"><img width="593" alt="image-20200212195618458" src="https://user-images.githubusercontent.com/16719527/74427869-47387080-4e9b-11ea-85c7-6c4cb443476e.png"></p>



<p align="center"><img width="533" alt="image-20200212200201035" src="https://user-images.githubusercontent.com/16719527/74427878-499aca80-4e9b-11ea-8638-b0aa71e3284f.png"></p>

<br />

<br /><br />

### Finite observable sequences

옵저버블은 0개 이상의 값을 방출하다가 정상 종료 되거나 에러로 종료된다.

iOS 앱에서 인터넷에서 파일을 다운로드 하는 과정을 생각해보자.

1. 다운로드를 시작한다. 받아오는 데이터를 observing 시작한다.
2. 파일의 데이터 조각들을 계속 받는다.
3. 네트워크 연결이 끊어지면, 다운로드가 중지되고 에러가 발생해서 연결이 타임아웃 된다.
4. 다운로드가 완료되면, 성공적으로 끝난다. 

<br />

```swift
//API.download(file:)는 Observable<Data> 인스턴스를 리턴한다. 이 인스턴스가 네트워크를 통해 받아온 데이터 조각을 Data 타입의 값으로 내보낸다.
API.download(file: "http://www...")
	.subscribe(onNext: { data in
    //next 이벤트를 onNext 클로저로 구독할 수 있다.
  	//임시 파일에 데이터를 추가한다.
  },
	onError: { error in
    //error 이벤트를 onError 클로저로 구독할 수 있다.
  	//사용자에게 에러가 발생했음을 알린다. error.localizedDescription을 alert으로 띄울 수도 있고...
  },
  onComplete: {
    //completed 이벤트를 처리하기 위해서 onCompleted 클로저를 사용한다.
    //다운로드된 파일을 사용한다. 다운로드 된 파일을 새로운 뷰 컨트롤러로 push 하거나...맘대루
  })
```

<br />

<br />

### Infinite observable sequences

UI 이벤트 시퀀스 같은건 infinite할 수 있다. 기기 방향 전환(회전)에 반응하는 코드를 생각해보자.

<p align="center"><img width="566" alt="image-20200212203531147" src="https://user-images.githubusercontent.com/16719527/74427882-4acbf780-4e9b-11ea-8be0-51188ef1c02c.png"></p>

1. `NotificationCenter`의 `UIDeviceOrientationDidChange` notification을 관찰하는 클래스를 추가한다.
2. 방향 전환 이벤트를 처리하기 위한 콜백 메소드를 지정한다. `UIDevice`에서 현재 방향을 받아오고, 최근값에 따라서 반응한다.

이런 방향전환 이벤트 시퀀스는 그냥 종료되지 않는다. 기기가 있는 한, 방향 전환이 발생할 수 있다. 시퀀스가 사실상 무한하기 때문에 관찰을 시작한 순간에 항상 초기값을 갖는다.(?)

사용자가 기기를 회전하지 않아도 시퀀스가 종료되었다는 의미는 아니다. 그냥 이벤트가 발생하지 않은 것!

<br />

```swift
//UIDevice.rx.orientation은 Observable<Orientation>을 생성하는 컨트롤 프로퍼티라고 가정한다.
UIDevice.rx.orientation
	.subscribe(onNext: { current in
    //현재 orientation 구독한다.
    switch current {
      case .landscape:
      	//UI를 landscape에 맞게 재배치한다.
      case .portrait:
      	//UI를 portrait에 맞게 재배치한다.
    }
  })
//infinite한 이벤트 시퀀스이기 때문에 error, completed 이벤트는 발생하지 않을 것! onError나 onCompleted는 생략해도 된다.
```

<br /><br /><br />

## Lifecycle

옵저버블이 값을 방출시키는 것 = next 이벤트. 값이나 인스턴스나 제스쳐 이벤트 등등을 담을 수 있다.

<p align="center"><img width="487" alt="image-20200213141501854" src="https://user-images.githubusercontent.com/16719527/74427885-4b648e00-4e9b-11ea-97f4-0509f411bfa5.png"></p>

마블 다이어그램에서 맨 오른쪽에 세로 바가 있다. completed 이벤트로, 정상 종료되었음을 의미한다. 옵저버블이 세 번의 탭 이벤트를 방출하고, 끝났음을 나타낸다. 더 이상 아무것도 방출하지 않는다.

<p align="center"><img width="488" alt="image-20200213141732429" src="https://user-images.githubusercontent.com/16719527/74427886-4bfd2480-4e9b-11ea-9f61-2815aa9b7204.png"></p>

빨간 X 표시는 옵저버블이 error 이벤트를 방출했다는 것을 의미한다. 옵저버블은 error 이벤트를 방출한 후에 종료되고, 더 이상 아무것도 방출하지 않는다.

<br />

즉, 옵저버블은 elements를 가지고 있는 next 이벤트를 방출한다. 종료 이벤트(error, completed 이벤트)가 방출하기 전까지 계속 next 이벤트를 방출할 수 있다. 옵저버블이 종료되면 더 이상 이벤트를 방출할 수 없다.

<br /><br />

```swift
/// Represents a sequence event. ///
/// Sequence grammar:
/// **next\* (error | completed)**
public enum Event<Element> {
		/// Next element is produced.
    case next(Element)
		/// Sequence terminated with an error.
		case error(Swift.Error)
		/// Sequence completed successfully.
    case completed
}
```

이벤트는 이런 enum으로 되어있다. `.next`는 `Element` (제네릭) 인스턴스를 가지고 있고, `.error` 이벤트는 `Swift.Error` 인스턴스를 가지고 있다. `.completed`는 따로 데이터를 가지고 있지 않다. 그냥 단순 종료 이벤트다.

<br />

<br /><br />

## Observable 생성 오퍼레이터 - just, of, from

```swift
let one = 1
let two = 2
let three = 3

//--- just ---
let observable1 = Observable<Int>.just(one)
let observable2 = Observable.just([one])
//just는 element가 하나만 들어있는 배열만 넣을 수 있음


//--- of ---
let observable3 = Observable.of(one, two, three)
//[Int]가 아니라 Int로 타입 추론되어 지정됨.
let observable4 = Observable.of([one, two, three])
//타입 [Int]로 추론됨.


//--- from ---
let observable5 = Observable.from([one, two, three])
//타입은 Int. [Int]가 아니다.


//--- empty ---
let observable6 = Observable<Void>.empty()
//타입 추론할 수 있는 항목이 없기 때문에 명시해주어야 한다.


//--- never ---
let observable7 = Observable<Any>.never()
```

<br />

### just	

하나의 element를 옵저버블 시퀀스로 만든다. 하나의 element가 있는 배열도 가능.

<br />

### of

of 오퍼레이터는 가변(variadic) 파라미터를 가지고, 스위프트가 이를 기반으로 옵저버블의 타입을 추론한다. (아~! `of(_ element: Int..., ~~~)` 이렇게 파라미터를 여러개 받을 수 있는 가변 파라미터(...)로 되어있다고)

<br />

### from

타입이 지정된 배열로부터 개별 element를 옵저버블을 만든다. 배열만 넣을 수 있다.

<br /><br />

<br />

## Subscribe

구독하는 것은 `NotificationCenter`와 유사하지만, 보통 `.default` 싱글톤 인스턴스를 사용하는 것과 달리 Rx에서는 각각의 옵저버블이 다르다.

그리고 옵저버블은 누군가 자신을 구독하기 전에는 이벤트를 방출하거나 작업을 하지 않는다. = *Cold Observable*

<br />

### subscribe()

```swift
func subscribe(_ on: @escaping (Event<Element>) -> Void) -> Disposable
```

<br />

```swift
let one = 1
let two = 2
let three = 3

let observable = Observable.of(one, two, three)

observable.subscribe { event in
	print(event)
}

/*
next(1)
next(2)
next(3)
completed
*/
```

옵저버블이 각 element에 대해 `.next` 이벤트를 방출하고, `.completed` 이벤트를 방출한 후에 종료된다.

<br />

보통 옵저버블로 작업할 때는 `.next` 이벤트 자체가 아니라 `.next`에서 나온 element를 사용하게 될 것.

```swift
observable.subscribe { event in
    print(event.element)
}
/*
Optional(1)
Optional(2)
Optional(3)
nil
*/

observable.subscribe { event in
  if let element = event.element {
    print(element)
  }
}

/*
1
2
3
*/
```

`Event` 는 `element` 라는 프로퍼티를 가지고 있다. `.next` 이벤트만 값을 가지기 때문에 옵셔널 타입이다. 그래서 옵셔널 바인딩을 통해 언래핑해서 값을 사용할 수 있다. `.completed` 이벤트는 값을 가지고있지 않기 때문에 프린트되지 않은 것을 볼 수 있음.

<br />

또 옵저버블이 방출하는 세 가지 이벤트(next, error, completed)에 대한 subscribe 오퍼레이터 `onNext`, `onError`, `onCompleted ` 를 사용할 수도 있다. 

```swift
observable.subscribe(onNext: { element in
	print(element)
})

/*
1
2
3
*/
```

위 subscribe 오퍼레이터는 next 이벤트만 처리한다. onNext 클로저는 .next 이벤트의 element를 인수로 받는다. 옵셔널이 벗겨져서 들어오네?

<br /><br />

<br />

## 다양한 Observable

### Empty

말 그대로 element가 없는 비어있는 옵저버블을 만든다. `.completed` 이벤트만 방출하고 종료됨.

```swift
let observable = Observable<Void>.empty()
observable.subscribe(
    onNext: { element in
        print(element)
    },
    onCompleted: {
        print("completed")
    }
)
//completed
```

`.completed` 이벤트만 발생하고 종료된다. 언제 사용할까? 바로 종료되거나 의도적으로 zero values를 가지는 옵저버블을 리턴해야할 때 유용하대...

<br />

### Never

아무것도 방출하지 않고, 종료되지 않는 옵저버블을 만든다. infinite duration을 나타내는데 사용할 수 있다.

```swift
let observable = Observable<Any>.never()
observable.subscribe(
    onNext: { element in
        print(element)
    },
    onCompleted: {
        print("completed")
    }
)
```

아무것도 출력되지 않는다. completed 이벤트도 발생하지 않고, 종료되지도 않는다.

<br />

### Range

start와 count를 지정해 순차적인 정수값을 가진 옵저버블을 만든다.

```swift
let observable = Observable<Int>.range(start: 1, count: 10)
observable.subscribe(onNext: { i in
    let n = Double(i)
    let fibonacci = Int(((pow(1.61803, n) - pow(0.61803, n)) / 2.23606).rounded())
    print(fibonacci)
})

/*
0
1
2
3
5
8
13
21
34
55
*/
```

이렇게 onNext에서 계산을 처리하는 것 보다 변환(transforming) 오퍼레이터를 넣어 처리하는 것이 더 낫다. (map 같은 걸 말하는듯)

<br />

### Create

옵저버블을 만들어 방출할 이벤트를 지정할 수 있다. 옵저버블에서 subscribe 호출을 구현한다. 옵저버에게 생성될 이벤트를 정의한다.

```swift
static func create(_ subscribe: @escaping (AnyObserver<Element>) -> Disposable) -> Observable<Element>
```

subscribe 파라미터는 AnyObserver를 받아서 Disposable을 리턴한다. AnyObserver는 옵저버블 시퀀스에 값을 추가하는 것을 용이하게 하는 제네릭 타입으로, 구독자들에게 방출된다.

```swift
Observable<String>.create { observer in
    observer.onNext("A")
    observer.onNext("B")
    observer.onNext("C")
    observer.onCompleted()
    
    return Disposables.create()
    //it may seem strange but remember that the subscribe operators return a disposable representing the subscription.
    //Here, Disposables.create() is an empty disposable, but some disposables have side effects.
		}
```

<br /><br />

<br />

## Disposing & Terminating

옵저버블은 누군가 구독하기 전까지는 아무것도 하지 않는다. 구독이 옵저버블 작동(이벤트를 방출하고 .error나 .completed 이벤트와 함께 종료되는 것)의 트리거가 된다.

구독을 취소해서 수동으로 옵저버블이 종료되도록 할 수 있다.

<br />

### dispose()

구독을 명시적으로 취소한다.

```swift
let observable = Observable.of("A", "B", "C")
let subscription = observable.subscribe { event in
    print(event)
}
//옵저버블을 구독했을 때 리턴되는 Disposable을 저장한다.

subscription.dispose()

/*
next(A)
next(B)
next(C)
completed
*/
```

(...?🤔) 암튼 이걸 사용해서 구독을 취소시킬 수 있음. 위 예제에서 옵저버블을 구독하고 있는 것은 하나밖에 없기 때문에 구독을 취소시키면 이 옵저버블은 이벤트 방출을 멈출것.

<br />

### 🤔🤔🤔

*왜 dispose 이벤트는 출력이 안될까..? onDisposed로 이벤트 받아오면 잘 출력되는데...?! 그래고 dispose() 호출 안해도 onDisposed 이벤트는 받는다. 왤까..?*

```swift
let observable = Observable.of("A", "B", "C")
let subscription = observable.subscribe(
        onNext: { print($0) },
        onCompleted: { print("Completed") },
        onDisposed: { print("Disposed") }
    )
//subscription.dispose()

/*
A
B
C
Completed
Disposed
*/
```

<br />

### DisposeBag

Disposable들을 한번에 담아서 관리한다. `.disposed(by:)` 등의 메소드를 사용해서 disposeBag에 disposable을 추가할 수 있다. DisposableBag이 deallocate 되면 담겨있던 disposable들에 `dispose()`가 호출된다.

```swift
let disposeBag = DisposeBag()

Observable.of("A", "B", "C")
    .subscribe { print($0) }
    .disposed(by: disposeBag)
```

많이 사용되는 패턴이다. 옵저버블을 만들고, 구독하고, 바로 disposeBag에 추가한다.

<br />

### Disposable을 왜 신경써야 할까?

만약에 subscription을 disposeBag에 추가하는 것을 깜빡했거나, 아니면 구독이 끝났을 때 수동으로 `dispose()`를 호출하거나, 어떤 이유에서든 어떤 시점에서 옵저버블이 종료된다면(?), 메모리 누수가 발생할 수 있다. 아래의 예시로 확인해보자.

<br />

 ```swift
enum MyError: Error {
    case anError
}
//에러 발생시키기 위해서 Error 프로토콜 준수하는 enum을 만들어주었음.


let disposeBag = DisposeBag()

Observable<String>.create { observer in
    observer.onNext("1")
    observer.onError(MyError.anError)
    observer.onCompleted()
    observer.onNext("?")
    
    return Disposables.create()
		}
    .subscribe(
        onNext: { print($0) },
        onError: { print($0) },
        onCompleted: { print("Completed") },
        onDisposed: { print("Disposed") }
    )
    .disposed(by: disposeBag)

/*
1
anError
Disposed
*/
 ```

<br />

만약에 `.completed`나 `.error` 이벤트가 발생하지 않고, subscribe를 `disposeBag`에도 추가하지 않았다면 어떻게 될까?

```swift
Observable<String>.create { observer in
    observer.onNext("1")
    //observer.onError(MyError.anError)
    //observer.onCompleted()
    observer.onNext("?")
    
    return Disposables.create()
		}
    .subscribe(
        onNext: { print($0) },
        onError: { print($0) },
        onCompleted: { print("Completed") },
        onDisposed: { print("Disposed") }
    )
    //.disposed(by: disposeBag)

/*
1
?
*/
```

옵저버블이 끝나지 않기 때문에 이 disposable은 절대 dispose 되지 않을 것이다. 메모리 누수 발생!!

<br /><br /><br />

## Observable Factory 만들기 - deferred

옵저버블 팩토리는 각 subscribe에 새로운 옵저버블을 제공한다.

```swift
let disposeBag = DisposeBag()

var flip = false

let factory: Observable<Int> = Observable.deferred {
    flip.toggle()
    
    if flip {
        return Observable.of(1, 2, 3)
    } else {
        return Observable.of(4, 5, 6)
    }
}

for _ in 0...3 {
    factory.subscribe(onNext: {
        print($0, terminator: "")
        })
        .disposed(by: disposeBag)
    
    print()
}

/*
123
456
123
456
*/
```

`factory` 를 subscribe 할 때마다 `factory`가 리턴하는 옵저버블을 받아온다. 

<br />

<br /><br />

## Traits - single, completable, maybe

Trait는 일반 옵저버블보다 좁은 동작 set을 가지는 옵저버블이다. Trait를 사용하면 의도를 명확히 할 수 있어 코드를 좀 더 직관적으로 만들 수 있다.

<br />

### single

`single`은 `.success(value)`와 `.error` 이벤트를 방출할 수 있다. `.success(value)` 이벤트는 사실 `.next`와 `.completed` 이벤트의 조합이다.

데이터를 다운받거나, 디스크에서 로딩하는 등의 성공 또는 실패를 발생시키는 일회성 작업에 유용하다.

<br />

### completable

`completable`은 `.completed`나 `.error` 이벤트만 방출한다. 어떤 값도 방출하지 않는다.

파일 쓰기같은 성공적으로 완료하거나 실패할 수 있는 작업에서 사용한다.

<br />

### maybe

`maybe`는 `single`과 `completable`을 합쳐놓은 거라고 생각하면 된다. `.success(value)`, `.completed`, `.error` 이벤트를 방출할 수 있다. 성공 또는 실패 할 수있는 작업을 구현하고 선택적으로 성공시 값을 반환해야하는 경우에 사용할 수 있다.

<br />

`single`을 사용해 playground의 Resource/Copyright.txt에 있는 텍스트를 읽어 출력해보자.

```swift
let disposeBag = DisposeBag()

enum FileReadError: Error {
    case fileNotFound, unreadable, encodingFailed
}

//디스크의 파일에서 text를 읽어와 Single<String>으로 만들어 리턴하는 메소드.
func loadText(from name: String) -> Single<String> {
    return Single.create { single in
        //create 메소드는 disposable을 리턴해야한다.
        let disposable = Disposables.create()
        
        guard let path = Bundle.main.path(forResource: name, ofType: "txt") else {
            single(.error(FileReadError.fileNotFound))
            return disposable
        }
        
        guard let data = FileManager.default.contents(atPath: path) else {
            single(.error(FileReadError.unreadable))
            return disposable
        }
        
        guard let contents = String(data: data, encoding: .utf8) else {
            single(.error(FileReadError.encodingFailed))
            return disposable
        }
        
        single(.success(contents))
        return disposable
    }
}

loadText(from: "Copyright")
    .subscribe{
        switch $0 {
        case .success(let string):
            print(string)
        case .error(let error):
            print(error)
        }
    }
    .disposed(by: disposeBag)
```

<br />

<br /><br />

## Side effect - do

`do` 오퍼레이터를 사용해 side effect를 처리할 수 있다. 생성된 이벤트를 변경하지 않는 작업들을 수행하고 넘어간다. `onSubscribe` 핸들러도 포함해 옵저버블 subscribe에 대한 작업도 수행 가능.

```swift
let disposeBag = DisposeBag()

Observable<Int>.never()
    .do (onSubscribe: { print("do subscribe") })
    .subscribe (
        onCompleted: { print("Completed") },
        onDisposed: { print("Disposed") }
    )
    .disposed(by: disposeBag)

/*
do subscribe
Disposed
*/
```

<br />

<br /><br />

## debug

`debug` 오퍼레이터는 옵저버블에 발생하는 모든 이벤트들에 대한 정보를 출력한다.

```swift
let disposeBag = DisposeBag()
let strObservable = Observable<String>.of("A", "B", "C")

strObservable
    .debug("StrObserver")
    .subscribe { print($0) }
    .disposed(by: disposeBag)

/*
2020-02-13 19:47:33.966: StrObserver -> subscribed
2020-02-13 19:47:33.967: StrObserver -> Event next(A)
next(A)
2020-02-13 19:47:33.967: StrObserver -> Event next(B)
next(B)
2020-02-13 19:47:33.967: StrObserver -> Event next(C)
next(C)
2020-02-13 19:47:33.967: StrObserver -> Event completed
completed
2020-02-13 19:47:33.967: StrObserver -> isDisposed
*/
```

<br /><br /><br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.