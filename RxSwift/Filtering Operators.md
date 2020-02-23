# Filtering Operators

말 그대로 필터링!! 구독자가 조건에 맞는 이벤트만 받을 수 있도록 하는 오퍼레이터들이다.

<br /><br />

## Ignoring operators

### ignoreElements()

`.next` 이벤트는 무시하고, `.completed`, `.error` 같은 중단 이벤트만 받는다.

<br />

<p align="center"><img width="500" alt="image-20200215174242565" src="https://user-images.githubusercontent.com/16719527/75103861-88234880-5644-11ea-8ac2-ee7a7eb4fd65.png"></p>

<br />

`ignoreElements` 는 observable이 종료됐을 때만 알고싶을 경우 사용하면 유용하다.

`Completable` 과 똑같은 동작을 한다.

<br />

```swift
let disposeBag = DisposeBag()
let subject = PublishSubject<String>()

subject
    .ignoreElements()
    .subscribe { _ in
        print("짠!")
    }
    .disposed(by: disposeBag)

subject.onNext("A")
subject.onNext("B")
subject.onNext("C")

subject.onCompleted()

/*
짠!
*/
```

<br />

<br /><br />

### elementAt(_:)

방출된 이벤트들 중에서 지정된 인덱스의 이벤트만 받는다. 인덱스는 댕근 0부터 시작.

<br />

<p align="center"><img width="500" alt="image-20200215181122083" src="https://user-images.githubusercontent.com/16719527/75103862-8a85a280-5644-11ea-81de-68eaef12da84.png"></p>

<br />

```swift
let disposeBag = DisposeBag()
let subject = PublishSubject<String>()

subject
    .elementAt(1)
    .subscribe { event in
        print("얍! \(event) 받았다.")
    }
    .disposed(by: disposeBag)

subject.onNext("A")
subject.onNext("B")
subject.onNext("C")

/*
얍! next(B) 받았다.
얍! completed 받았다.
*/
```

`elementAt(_:)` 은 해당 element를 받으면 바로 구독을 종료한다. 그래서 위에서 해당하는 인덱스의 이벤트를 받자마자 바로 `completed` 이벤트를 받은 것을 볼 수 있다.

<br /><br />

### filter

주어진 클로저를 true로 통과하는 애들만 받는다. 클로저에 들어가 false가 리턴되는 애들은 무시한다.

<br />

<p align="center"><img width="500" alt="image-20200215182157300" src="https://user-images.githubusercontent.com/16719527/75103863-8b1e3900-5644-11ea-985c-a8366bba9822.png"></p>

<br />

```swift
let disposeBag = DisposeBag()
let observable = Observable<Int>.of(1, 2, 3, 4, 5, 6)

observable
    .filter { $0 % 2 == 0 }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
2
4
6
*/
```

<br />

<br /><br />

## Skipping operators

### skip(_:)

첫 번째 이벤트부터 지정된 개수만큼 무시하고 그 다음 이벤트부터 받는다.

<br />

<p align="center"><img width="500" alt="image-20200215182753599" src="https://user-images.githubusercontent.com/16719527/75103864-8c4f6600-5644-11ea-9355-a478f0dce01c.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

Observable.of("A", "B", "C", "D", "E", "F")
    .skip(3)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
D
E
F
*/
```

<br /><br />

### skipWhile

`skipWhile`은 클로저로 주어진 조건이 true일 동안 스킵하다가, false가 되면 모두 통과시킨다.

<br />

<p align="center"><img width="500" alt="image-20200215184818081" src="https://user-images.githubusercontent.com/16719527/75103865-8c4f6600-5644-11ea-8dd8-4860eaa2ab73.png"></p>

<br />

위 마블 다이어그램을 보면, 1은 클로저에서 true로 반환되기 때문에 스킵되고, 2는 false를 반환하기 때문에 스킵되지 않는다. 그 이후로는 이제 스킵하지 않기 때문에 (조건을 확인하지 않고) 3도 받는다.

<br />

```swift
let disposeBag = DisposeBag()

Observable.of(2, 2, 3, 4, 4)
    .skipWhile { $0 % 2 == 0 }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
3
4
4
*/
```

짝수인 동안에만 스킵하다가 홀수를 만나면 그 이후로부터는 다 받는다.

<br /><br />

### skipUntil(_:)

`skipUntil`은 어떤 observable을 구독하고 있을 때, 트리거 observable에서 값이 발생할 때까지 이벤트를 스킵한다. 트리거에서 값이 발생하고 난 후부터는 모든 값을 받아온다.

<br />

<p align="center"><img width="600" alt="image-20200215185606548" src="https://user-images.githubusercontent.com/16719527/75103867-8ce7fc80-5644-11ea-8025-15d1c20cfb7b.png"></p>

<br />

위 그림에서 Source observable에서 발생하는 값들을 스킵하고 있다가, Trigger observable에서 `.next` 이벤트가 발생한 후부터 값을 받아오는 것을 볼 수 있다.

`skipUntil`은 파라미터로 트리거 역할을 할 옵저버블을 받는다.

```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
    .skipUntil(trigger)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

subject.onNext("A")
subject.onNext("B")

trigger.onNext("얍!")

subject.onNext("C")
subject.onNext("D")

/*
C
D
*/
```

<br /><br />

<br />

## Taking operators

skip과 반대 개념이다.

<br />

### take(_:)

첫번째 이벤트부터 지정된 개수만큼의 이벤트만 받고, 그 이후로는 무시한다.

<br />

<p align="center"><img width="500" alt="image-20200215190113672" src="https://user-images.githubusercontent.com/16719527/75103868-8d809300-5644-11ea-8714-7743d490bec8.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

Observable.of(1, 2, 3, 4, 5, 6)
    .take(3)
    .subscribe { event in print(event) }
    .disposed(by: disposeBag)

/*
next(1)
next(2)
next(3)
completed
*/
```

지정된 개수만큼 다 받아오면 바로 `completed` 이벤트를 받아 구독을 끝낸다.

<br /><br />

### takeWhile

`skipWhile`이랑 비슷한데 skip이 아닌 take 동작.

<br />

<p align="center"><img width="500" alt="image-20200216104842249" src="https://user-images.githubusercontent.com/16719527/75103870-8eb1c000-5644-11ea-8da1-8d5882bb7747.png"></p>

<br />

방출된 element의 인덱스를 알고싶다면 `enumerated` 오퍼레이터를 사용하면 된다.  `enumerated` 오퍼레이터는 observable가 내보낸 element를 `(index, element)` 형태의 튜플로 만들어준다. (Swift Standard Library의 `enumerated` 메소드와 같음)

<br />

```swift
let disposeBag = DisposeBag()

Observable.of(1, 3, 5, 7, 9)
    .enumerated()
    .takeWhile { index, num in
        num % 2 == 1 && index < 4
    }
    .map { $0.element }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
1
3
*/
```

<br />

<br />

### takeUntil

`skipUntil` 과 비슷하다. 트리거 observable이 있고, source observable에서 이벤트를 받다가 트리거에서 값이 발생하면 completed 된다.

<br />

<p align="center"><img width="500" alt="image-20200216110006446" src="https://user-images.githubusercontent.com/16719527/75103871-8eb1c000-5644-11ea-9a6e-aff8ae9c74a4.png"></p>

<br />

매개변수로 trigger 역할을 할 observable을 받는다.

```swift
let disposeBag = DisposeBag()

let subject = PublishSubject<String>()
let trigger = PublishSubject<String>()

subject
    .takeUntil(trigger)
    .subscribe { event in
        print(event)
    }
    .disposed(by: disposeBag)

subject.onNext("A")
subject.onNext("B")

trigger.onNext("얍!")

subject.onNext("C")
subject.onNext("D")

/*
next(A)
next(B)
completed
*/
```

<br />

`RxCocoa` 라이브러리 API를 사용할 경우, 구독을 dispose 시킬 때 dispose bag에 담는 대신에 아래처럼 `skipUntil`을 사용할 수 있다. 그러나 메모리 누수가 발생하거나 코드가 복잡해질 수 있기 때문에 dispose bag에 담아 처리하는 것이 젤 간편할 것.

<br />

```swift
_ = someObservable
		.takeUntil(self.rx.deallocated)
		.subscribe(onNext: {
      print($0)
    })
```

뷰 컨트롤러나 뷰 모델이 deallocate 될 때 take 작업을 멈춘다. dispose bag에 담는 거랑 똑같은 동작을 하겠지요?

<br /><br /><br />

## Distinct operators

### distinctUntilChanged

같은 값이 연속되게 여러번 들어오면 첫번째 값 이후로는 무시한다.

<br />

<p align="center"><img width="500" alt="image-20200216111513170" src="https://user-images.githubusercontent.com/16719527/75103872-8f4a5680-5644-11ea-96c5-97afaaed00bc.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

Observable.of("A", "B", "B", "B", "C", "C", "D", "E")
    .distinctUntilChanged()
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
A
B
C
D
E
*/
```

2, 3, 5번째 element는 이전 값과 중복된 값이기 때문에 무시되었다. 이전과 다른 값이라면 중복된 값이 아니기 때문에 받아온다.

`distinctUntilChanged()`는 비교 작업을 기반으로 하는 동작이기 때문에 [`Equatable` 프로토콜](https://developer.apple.com/documentation/swift/equatable)을 준수하는 타입에만 사용이 가능하다. 하지만 `distinctUntilChanged(_:)` 를 사용해 커스텀 비교 로직을 적용할 수 있다.

<br />

<p align="center"><img width="500" alt="image-20200216114827628" src="https://user-images.githubusercontent.com/16719527/75103874-8f4a5680-5644-11ea-8a7c-2c410ad98cbd.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

let formatter = NumberFormatter()
formatter.numberStyle = .spellOut

let observable = Observable<NSNumber>.of(10, 110, 20, 200, 210, 310)
    
observable
    .map { formatter.string(from: $0) }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
ten
one hundred ten
twenty
two hundred
two hundred ten
three hundred ten
*/
    
observable
    .distinctUntilChanged { a, b in
        guard let aWords = formatter.string(from: a)?.components(separatedBy: " "), let bWords = formatter.string(from: b)?.components(separatedBy: " ") else {
            return false
        }
        
        var containsMatch = false
        
        for aWord in aWords where bWords.contains(aWord) {
            containsMatch = true
            break
        }
        return containsMatch
    }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
10
20
200
*/
```

`NSNumber` 타입의 값을 영어 `String` 배열로 변환해 겹치는 단어가 있다면 true, 없다면 false를 반환하는 클로저를 `distinctUntilChanged`에 적용했다. 클로저의 리턴값이 true이면 중복된 값으로 인식해 무시하고, false이면 받는 것을 볼 수 있다~!!

`distinctUntilChanged(_:)`는 `Equatable` 프로토콜을 준수하지 않는 타입에서 중복값을 방지하고 싶을 때 사용할 수 있다!

<br />

<br />

<br /><br /><br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.