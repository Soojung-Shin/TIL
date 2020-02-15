# Subjects

Subjects는 옵저버블과 옵저버의 역할을 모두 할 수 있다.

<br /><br /><br />

## PublishSubject

PublishSubject는 구독한 시점부터 구독자에게 새로운 이벤트를 전달하고 싶을 때 유용하다. 구독을 끊거나, subject가 `.completed`나 `.error` 이벤트와 종료될때까지 유효하다.

empty로 시작해서 구독자들에게 새로운 elements들을 방출한다.

현재 구독자(subscriber)들에게만 이벤트를 방출한다. 그래서 구독하지 않았을 때 발생한 이벤트들에 대해서는 알 수 없다. 

<br />

<p align="center"><img width="540" alt="image-20200214133632426" src="https://user-images.githubusercontent.com/16719527/74581562-48c97c00-4ff4-11ea-9afb-9ecd5d1f86d1.png"></p>

subscriber1은 이벤트 1이 발생한 후에 구독했기 때문에 1에 대한 이벤트는 받지 못한다. 그 후에 2, 3을 받는다. subscriber2는 이벤트 2 이후에 구독을 시작했기 때문에 3만 받는다.

<br /><br />

```swift
let subject = PublishSubject<String>()
subject.onNext("Is anyone Listening?")

let subscriptionOne = subject
    .subscribe(onNext: { string in
        print(string)
    })

subject.on(.next("1"))
subject.onNext("2")

/*
1
2
*/

let subscriptionTwo = subject
    .subscribe { event in
        print("2)", event.element ?? event)
    }

subject.onNext("3")

/*
3
2) 3
*/

subscriptionOne.dispose()
subject.onNext("4")

/*
2) 4
*/
```

<br />

Public subject가 중지 이벤트(`.complted` / `.error`)를 받으면, 구독자들에게 중지 이벤트를 보내고, 더 이상 `.next` 이벤트를 내보내지 않는다. 그리고~!! 미래의 구독자들에게도 이 stop 이벤트를 재전송 할 수 있다.

```swift
subject.onCompleted()

/*
2) completed
*/

subject.onNext("5")
//subject가 종료되었기 때문에 이 이벤트는 보내지지 않는다.
subscriptionTwo.dispose()


let disposeBag = DisposeBag()

subject.subscribe {
        print("3)", $0.element ?? $0)
    }
    .disposed(by: disposeBag)
//3번째 구독자가 subject를 다시 시작하게 할까? 놉! 하지만 종료 이벤트는 받는다.

/*
3) completed
*/

subject.onNext("?")
//subject가 종료되었기 때문에 이 이벤트는 보내지지 않는다.
```

subject는 종료된 뒤에 자신을 구독하는 애들한테 종료 이벤트를 다시 보낸다. 그래서 종료시 알림을 받을 뿐 아니라, 이미 종료된 것을 구독했을 때에 대비해서 코드에 중지 이벤트에 대한 핸들러를 포함시키는 것이 좋다. 이런게 버그의 원인이 될 수 있으니 조심하래..

만약에 온라인 경매 앱 같은 time-sensitive data를 모델링 할 때 publish subject를 사용하게 될거래. 오전 10시 1분에 참가한 사용자에게 9시 59분에 경매 1분 전이라는 것을 알리는 것은 이상하다. 당연하지 그걸 말이라고....

<br />

<br /><br />

근데 새로운 구독자에게 가장 마지막에 방출된 element를 보내고 싶을 수도 있다. 그게 구독 전에 방출된 것인데도..! 그런 경우에 선택할 수 있는 몇가지 방법이 있다.

<br /><br /><br />

## BehaviorSubject

초기값이 있는 상태로 시작하고, 새로운 구독자에게는 가장 마지막 element를 전달한다.

publish subject와 비슷하지만, 누군가 자신을 구독하면 가장 마지막 .next 이벤트를 보내준다.

<br />

<p align="center"><img width="690" alt="image-20200214141947656" src="https://user-images.githubusercontent.com/16719527/74581563-4a933f80-4ff4-11ea-85b7-db4318823cc9.png"></p>

subscriber1은 이벤트 1 이후에 구독했지만 구독하자마자 1을 받았다. 그리고 2, 3을 받는다. subscriber2는 2 이후에 구독했지만 구독하자마자 2를 받았다. 그리고 3을 받는다.

<br />

Behavior subject는 항상 가장 마지막 값을 방출해야하기 때문에 생성할 때 반드시 초기값을 넣어주어야 한다. 만약 생성할 시점에 초기값을 줄 수 없다면, PublishSubject를 대신 써야한다.

<br /><br />

```swift
let subject = BehaviorSubject(value: "Initial value")
let disposeBag = DisposeBag()

subject.subscribe {
        print(label: "1)", event: $0)
    }
    .disposed(by: disposeBag)

/*
1) Initial value
*/
```

구독할 시점까지 아무런 이벤트가 발생하지 않았다면 초기값을 전달한다.

<br />

```swift
let subject = BehaviorSubject(value: "Initial value")
let disposeBag = DisposeBag()

subject.onNext("A")

subject.subscribe {
        print(label: "1)", event: $0)
    }
    .disposed(by: disposeBag)

/*
A
*/
```

구독할 때 가장 최근에 전달된 `.next` 이벤트를 받았다.

<br />

그럼 subject가 종료된 후에 구독한다면?

```swift
subject.onError(MyError.anError)

subject.subscribe {
        print(label: "2)", event: $0)
    }
    .disposed(by: disposeBag)

/*
1) anError
2) anError
*/
```

2번째 subscriber가 anError라는 종료 이벤트를 받은 것을 볼 수 있다. Publish subject와 마찬가지로 종료 후에 구독하면 종료 이벤트를 다시 전송하는듯.

<br />

Behavior subject는 뷰를 최신 데이터로 미리 채워놓고 싶을 때 유용하다. 예를 들어 사용자 프로필 화면의 컨트롤을 behavior subject와 바인딩 시켜 놓으면, 앱이 새로운 데이터를 패치하는 동안에 화면에 가장 최신의 값을 미리 채워놓을 수 있다.

<br /><br /><br />

근데 가장 마지막 값보다 더 많은 이벤트를 보여주고 싶으면? 예를 들어 검색 화면에서 최근 검색어 5개를 보여주고 싶다면...!

<br /><br /><br />

## ReplaySubject

Replay subject는 지정된 크기까지 최신 element들을 일시적으로 캐시하거나 버퍼에 저장한다. 그리고 새로운 구독자에게 이 버퍼를 제공한다.

초기화할 때 bufferSize 파라미터를 제공해야하고, 그 크기만큼의 element 버퍼를 유지한다.

<br />

<p align="center"><img width="629" alt="image-20200214151729696" src="https://user-images.githubusercontent.com/16719527/74581564-4c5d0300-4ff4-11ea-9c7b-cb68431fed45.png"></p>

버퍼 사이즈가 2인 replay subject가 있다. subscriber1은 이미 replay subject를 구독하고 있었으므로 방출된 이벤트들을 받아온다. subscriber2는 이벤트 2 이후에 구독하고, 버퍼에 저장되어있던 1과 2를 받았다.

<br /><br />

```swift
let disposeBag = DisposeBag()
let subject = ReplaySubject<String>.create(bufferSize: 2)

subject.onNext("A") //버퍼: (A)
subject.onNext("B") //버퍼: (A, B)
subject.onNext("C") //버퍼: (B, C)

subject
    .subscribe {
        print(label: "1)", event: $0)
    }
    .disposed(by: disposeBag)

subject
    .subscribe {
        print(label: "2)", event: $0)
    }
    .disposed(by: disposeBag)

/*
1) B
1) C
2) B
2) C
*/

subject.onNext("D") //버퍼: (C, D)

/*
1) D
2) D
*/

subject
    .subscribe {
        print(label: "3)", event: $0)
    }
    .disposed(by: disposeBag)

/*
3) C
3) D
*/
```

<br />

그럼 replay subject는 중단 이벤트가 발생됐을 때 어떻게 할까? 위 코드에서 subscriber3을 추가하기 전에 에러 이벤트를 발생시켜보자.

```swift
subject.onNext("D")
subject.onError(MyError.anError)

subject
    .subscribe {
        print(label: "3)", event: $0)
    }
    .disposed(by: disposeBag)

/*
1) D
2) D
1) anError
2) anError
3) C
3) D
3) anError
*/
```

다른 subject들처럼 에러 후에 구독한 구독자에게도 중단 이벤트를 재전송했다. 근데 중단 이벤트를 받기 전에 버퍼 안에 있는 값들도 받았다. 버퍼가 아직 남아있기 때문.

<br />

```swift
subject.onError(MyError.anError)

/*
1) anError
2) anError
*/

subject.dispose()
//subject를 dispose하는 코드를 추가했다.

subject
    .subscribe {
        print(label: "3)", event: $0)
    }
    .disposed(by: disposeBag)

/*
3) Object `RxSwift.(unknown context at $10e71d950).ReplayMany<Swift.String>` was already disposed.
(ReplayMany는 replay subject를 만드는데에 사용되는 내부 타입이다.)
*/
```

replay subject를 명시적으로 dispose 시키면 새로운 구독자는 해당 replay subject가 이미 dispose 되었다는 에러 이벤트를 받게된다. (다른 subject도 마찬가지)

보통은 replay subject에 `dispose()`를 명시적으로 호출하지 않는다. 왜냐면 만약 구독을 dispose bag에 추가했다면 (그리고 strong 순환 참조가 만들어지지않도록 했다면!), 소유자(뷰 컨트롤러나 뷰 모델)이 deallocate 될 때 모두 dispose되고 deallocate 될 것이다.

<br /><br />

Replay subject를 사용할 때는 버퍼가 메모리에 보관된다는 것을 주의해야한다. 예를 들어 이미지 같은 메모리를 많이 차지하는 인스턴스를 가지는 replay subject에 버퍼 사이즈도 크다면?!

또 주의해야할 점은 배열을 가지는 replay subject를 만들때다. 방출되는 element가 배열일 때, 버퍼는 많은 배열들을 가지게 된다. 메모리에 신경써야 한다.

<br />

<br /><br />

## AsyncSubject



## AsyncSubject

subject가 .completed 이벤트를 받았을 때에만 시퀀스에서 가장 마지막의 .next 이벤트를 방출한다.

<br />

<p align="center"><img width="743" alt="image-20200214161312786" src="https://user-images.githubusercontent.com/16719527/74581566-4e26c680-4ff4-11ea-9e89-8cb0c690421c.png"></p>

<br /><br />

```swift
let disposeBag = DisposeBag()
let subject = AsyncSubject<Int>()

subject.onNext(1)

subject
    .subscribe {
        print(label: "1)", event: $0)
    }
    .disposed(by: disposeBag)

subject
    .subscribe {
        print(label: "2)", event: $0)
    }
    .disposed(by: disposeBag)

subject.onNext(2)
subject.onNext(3)
subject.onCompleted()

/*
1) 3
2) 3
1) completed
2) completed
*/
```

`.completed` 이벤트를 받았을 때 가장 마지막 값을 받아오고, `completed` 이벤트를 받는다.

<br />

`completed` 이벤트 말고 `error` 이벤트가 발생했을 때는?

```swift
//subject.onCompleted()
subject.onError(MyError.anError)

/*
1) anError
2) anError
*/
```

마지막 값을 받아오지 않고 `error` 이벤트만 받고 종료된다.

<br />

<br /><br />

## Relay

Relay는 기존 subject의 behavior를 유지하면서 subject를 감쌀(wrap) 수 있다. 다른 subject나 일반적인 옵저버블에서는 값을 추가할 때 `onNext(_:)` 메소드를 사용했지만, relay에서는 `accept(_:)` 메소드를 사용한다. relay가 오직 값만 받아들일(accept) 수 있고 `.error`나 `.completed` 이벤트는 추가할 수 없기 때문이다. 그래서 non-terminating 시퀀스에서 유용하다.

래핑된 subject와 relay의 차이점은 절대 종료되지 않는다는 것! relay는 종료되지 않는 다는 것을 보장할 수 있다.

<br /><br />

### PublishRelay

PublishRelay는 PublishSubject를 감싼 것이다. accept 메소드와 절대 종료되지 않는 것을 제외하고는 동작은 publish subject와 완전 같음~!

<br />

```swift
let disposeBag = DisposeBag()
let relay = PublishRelay<String>()

relay.accept("똑똑 듣는 사람 있나요?")

relay.subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

relay.accept("1")

/*
1
*/

//relay.accept(MyError.anError)
//relay.onCompleted()
//컴파일 에러!
```

<br />

<br /><br />

### BehaviorRelay

BehaviorRelay는 BehaviorSubject를 감싼 것이다. Behavior relay도 마찬가지로 `.completed`나 `.error` 이벤트로 종료시킬 수 없다. Behavior relay의 장점은 최근 값을 받아올 수 있다는 것!

<br />

```swift
let disposeBag = DisposeBag()
let relay = BehaviorRelay(value: "Initial value")

relay.accept("New initial value")

relay.subscribe {
        print(label: "1)", event: $0)
    }
    .disposed(by: disposeBag)

/*
1) New initial value
*/

relay.accept("A")

/*
1) A
*/

relay.subscribe {
        print(label: "2)", event: $0)
    }
    .disposed(by: disposeBag)

/*
2) A
*/

relay.accept("B")

/*
1) B
2) B
*/

print(relay.value)

/*
B
*/
```

`value` 프로퍼티를 사용해 behavior relay의 최근값에 직접 접근할 수도 있다. 

<br />

Behavior relay는 명령형 프로그래밍과 반응형 프로그래밍을 연결할 때 매우 유용하다. 다른 subject와 마찬가지로 구독을 통해서 .next 이벤트를 받을 때마다 반응할 수 있고, 일회성으로 현재 값을 가져와야 할 때 구독없이도 접근해 가져올 수 있다.

<br /><br /><br />



-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.
- http://reactivex.io/documentation/subject.html