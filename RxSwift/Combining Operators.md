# Combining Operators

시퀀스를 조합하고, 각 시퀀스의 데이터를 결합한다. Swift의 collection operators와 비슷하다.

<br /><br />

## Prefixing and concatenating

### startWith(_:)

지정된 초기값을 observable 시퀀스의 앞에 붙인다.

<br />

<p align="center"><img width="500" alt="image-20200218163542714" src="https://user-images.githubusercontent.com/16719527/75103772-04b52780-5643-11ea-96b8-2a8ffdc1aa31.png"></p>

<br />

```swift
let numbers = Observable.of(1, 2, 3)
let observable = numbers.startWith(0)

observable
    .subscribe(onNext: {
        print($0)
    })

/*
0
1
2
3
*/
```

<br />

RxSwift의 결정론적인 특성에 잘 맞는 오퍼레이터다. observer에게 초기값을 바로 주고, 나중에 업데이트 할 수 있다.

<br />

<br />

### concat(_:)

concat 오퍼레이터는 두 가지 종류가 있다. 첫 번째는 Observable의 static 메소드로 observable 컬렉션 타입(array 등등)을 매개변수로 갖는다. 컬렉션의 첫번째 시퀀스를 구독해 완료(complete)될 때까지 요소를 릴레이하고, 그 다음 시퀀스로 이동한다. 컬렉션의 모든 observable이 사용될 때까지 이 작업이 반복된다. 만약 어떤 시점에 내부의 observable에서 `error` 이벤트가 방출된다면, 연결된 observable(concatenated observable)도 `error`를 방출하고 종료된다.

<br />

<p align="center"><img width="500" alt="image-20200218165053143" src="https://user-images.githubusercontent.com/16719527/75103774-067eeb00-5643-11ea-8308-d35548f859c5.png"></p>

<br />

```swift
let first = Observable.of(1, 2, 3)
let second = Observable.of(4, 5, 6)

let observable = Observable.concat([first, second])
observable.subscribe(onNext: {
    print($0)
})

/*
1
2
3
4
5
6
*/
```

<br />

두 번째는 Observable의 인스턴스 메소드로 observable을 파라미터로 받는다. Source observable이 완료될 때까지 기다리다가 완료되면 파라미터로 받은 observable을 구독한다. 

```swift
let first = Observable<String>.of("A", "B", "C")
let second = Observable<String>.of("X", "Y", "Z")

let observable = first.concat(second)
observable.subscribe(onNext: {
    print($0)
})

/*
A
B
C
X
Y
Z
*/
```

<br />

당연히 같은 타입의 observable만 연결시킬 수 있다. 다른 타입을 연결시키려고하면 컴파일 에러 발생.

<br /><br />

### concatMap(_:)

`flatMap(_:)`과 유사하다. `flatMap`이 클로저로 observable 시퀀스를 만들고, 생성된 모든 observable은 병합된다. `concatMap`은 클로저에 의해 만들어진 시퀀스를 구독하고, 완료되면 다음 시퀀스의 구독을 시작한다. `concatMap`은 시퀀스의 순서를 보장해야 할 때 유용하다!

<br />

```swift
let sequences = [
    "first" : Observable<String>.of("A", "B", "C"),
    "second" : Observable<String>.of("X", "Y", "Z")
]

let observable = Observable.of("first", "second")
    .concatMap { key in sequences[key] ?? .empty() }

observable.subscribe(onNext: {
    print($0)
})

/*
A
B
C
X
Y
Z
*/
```

<br /><br />

<br />

## Merging

### merge

merge 오퍼레이터는 전달받은 각 시퀀스들을 구독하고, element를 받으면 바로 방출한다. 사전 정의된 순서가 없다.

<br />

<p align="center"><img width="500" alt="image-20200218174923817" src="https://user-images.githubusercontent.com/16719527/75103776-07b01800-5643-11ea-98ad-f30013ca6162.png"></p>

<br />

`merge()`는 source 시퀀스가 완료되고, 모든 내부 시퀀스가 완료되면 완료된다. 내부 시퀀스가 완료되는 순서는 상관없다. 만약 어떤 시퀀스에서 `error`를 방출하면, `merge()` observable은 즉시 `error`를 방출하고, 종료된다.

한 번에 구독되는 시퀀스 수를 제한하고 싶다면 `merge(maxConcurrent:)`를 사용하면 된다. `maxConcurrent`로 지정된 개수만큼 들어오는 시퀀스를 계속 구독한다. 그런 다음 더 들어오는 observable은 queue에 넣는다. 현재 시퀀스 중에 하나가 완료되면 순서대로 큐에서 꺼내서 구독한다. 리소스를 많이 사용하는 작업에서 유용하다. 예를 들어 네트워크 요청이 많을 때 동시 네트워크 연결 수를 제한하기 위해서 사용할 수 있다.

<br /><br /><br />

## Combining elements

### combineLatest

여러 시퀀스의 값을 결합한다. 결합된 내부 시퀀스 중 하나가 값을 내보낼 때마다, 지정한 클로저가 호출된다. 각각의 내부 시퀀스에서 내보낸 마지막 값을 받는다. 여러 textfield를 한 번에 관찰하고 값을 결합하거나 여러 source의 상태를 관찰하는 등등 많이 사용된다.

<br />

<p align="center"><img width="500" alt="image-20200218184531103" src="https://user-images.githubusercontent.com/16719527/75103777-0848ae80-5643-11ea-910c-f87a31b94099.png"></p>

<br />

<br />

```swift
let left = PublishSubject<String>()
let right = PublishSubject<String>()

let observable = Observable.combineLatest(left, right) { lastLeft, lastRight in
        "\(lastLeft) \(lastRight)"
    }

let disposable = observable.subscribe(onNext: { print($0) })


print("> Sending a value to Left")
left.onNext("Hello,")
print("> Sending a value to Right")
right.onNext("world")
print("> Sending another value to Right")
right.onNext("RxSwift")
print("> Sending another value to Left")
left.onNext("Have a good day,")

left.onCompleted()
right.onCompleted()

/*
> Sending a value to Left
> Sending a value to Right
Hello, world
> Sending another value to Right
Hello, RxSwift
> Sending another value to Left
Have a good day, RxSwift
*/
```

`combineLatest`는 각 시퀀스의 가장 마지막 값을 인수로 받는 클로저를 사용해서 observable을 결합한다. 여기에서는 lastLeft와 lastRight를 결합한 String을 만들었다. 클로저의 리턴 타입이 결합된 observable의 타입이 된다. `combineLatest`는 다른 타입의 시퀀스를 결합할 수 있는 유일한 오퍼레이터다~!!

결합된 모든 observable이 하나를 방출할 때 까지 아무일도 일어나지 않는다. 그 후 새로운 값을 방출할 때마다 클로저는 각 observable의 제일 최신 값을 받아서 element를 만든다.

`combineLatest(\_:_:resultSelector:)`는 클로저를 호출하기 전에 모든 observable이 하나의 element를 내보낼 때까지 기다린다. 헷갈릴 수 있으니 주의! 업데이트하는 데 시간이 걸리는 시퀀스에 초기값을 제공하기 위해 `startWith(_:)` 오퍼레이터를 사용하는 것도 좋은 선택이다.

`map(_:)` 오퍼레이터처럼 `combineLatest(\_:\_:resultSelector:)`는 클로저의 리턴 타입을 타입으로하는 observable을 만든다. 오퍼레이터 체인에서 새로운 타입을 만들어 내기 좋은 오퍼레이터! 일반적인 패턴은 값을 튜플에 결합한 다음 체인으로 전달하는 것이다. 아래 코드처럼 값을 조합한 다음 `filter(_:)`를 호출한다.

```swift
let observable = Observable
	.combineLatest(left, right) { ($0, $1) }
	.filter { !$0.0.isEmpty }
```

<br />

`combineLatest`는 파라미터로 2개부터 8개까지의 observable 시퀀스를 넣을 수 있다. 위에서 얘기한 것처럼 파라미터로 넣을 시퀀스의 타입이 같을 필요는 없다.

```swift
let choice: Observable<DateFormatter.Style> = Observable.of(.short, .long)
let dates = Observable.of(Date())

let observable = Observable.combineLatest(choice, dates) { format, when -> String in
    let formatter = DateFormatter()
    formatter.dateStyle = format
    return formatter.string(from: when)
}

_ = observable.subscribe(onNext: { value in
    print(value)
})
}

/*
2/19/20
February 19, 2020
*/
```

<br />

observable 컬렉션을 넣어서 클로저로 결합하고, 가장 최근의 값을 배열로 받아올 수도 있다. 컬렉션이기 때문에 observable은 같은 타입의 elements를 내보내야한다. 그래서 튜플로 사용하는 것보다는 유연성이 떨어지지만 알아두면 좋다.

```swift
let disposeBag = DisposeBag()

let left = Observable.of("1", "2", "3", "4", "5")
let right = Observable.of("A", "B", "C", "D")

let observable = Observable
    .combineLatest([left, right]) { strings in
        strings.joined(separator: " - ")
    }
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

/*
1 - A
2 - A
2 - B
3 - B
3 - C
4 - C
4 - D
5 - D
*/
```

<br />

`combineLatest`는 마지막 내부 시퀀스가 완료돼야 완료된다. 그 전에는 계속 결합된 값을 내보낸다. 일부 시퀀스가 종료되면, 방출된 마지막 값을 사용해서 다른 시퀀스의 새 값과 결합한다.

<br /><br />

### zip

파라미터로 받은 observable을 구독하고, 각 observable에서 새로운 값이 방출될 때까지 기다린다. 모든 observable에서 새로운 값이 방출되면, 그 때 그 값들을 가지고 클로저를 호출한다. 파라미터로 2개에서 8개까지의 observable이나 observable 컬렉션을 넣을 수 있다.

<br />

<p align="center"><img width="500" alt="image-20200219162302568" src="https://user-images.githubusercontent.com/16719527/75103778-08e14500-5643-11ea-9fc9-23b5843b5e89.png"></p>

<br />

```swift
enum Weather {
    case cloudy
    case sunny
}

let left: Observable<Weather> = Observable.of(.sunny, .cloudy, .cloudy, .sunny)
let right = Observable.of("Lisbon", "Copenhagen", "London", "Madrid", "Vienna")
let observable = Observable.zip(left, right) { weather, city in
    return "It's \(weather) in \(city)"
  }

observable.subscribe(onNext: { value in
    print(value)
})

/*
It's sunny in Lisbon
It's cloudy in Copenhagen
It's cloudy in London
It's sunny in Madrid
*/
```

위 예제에서 보면 `Vienna` 는 방출되지 않았다. 방출된 각 observable의 next 이벤트의 값들이 동일한 논리 위치(첫 번째 이벤트 값들끼리, 두 번째 이벤트 값들끼리..)를 가진 것들끼리 짝을 이룬 것을 볼 수 있다. 그래서 만약에 내부 observable중 하나가 next 이벤트를 내보내지 않는다면(종료되거나 등등의 이유로) `zip`은 값을 내보내지 않는다. 이걸 **indexed sequencing** 라고 하며, 시퀀스들의 동일한 index의 값들을 다룰 수 있다.

`zip`은 내부 observable이 모두 완료될 때까지 완료되지 않으므로 각자가 자신의 작업을 완료할 수 있도록 한다.

<br />

<br /><br />

## Triggers

어떤 observable들은 그냥 trigger action만 수행할 수도 있고, 어떤 것들은 데이터를 제공할 수도 있다. 

<br />

### withLatestFrom(_:)

특정한 trigger 액션이 발생했을 때, 지정된 observable의 가장 최근에 방출된 값을 받아온다. UI를 다룰 때 아주 유용하다.

<br />

<p align="center"><img width="500" alt="image-20200219164406587" src="https://user-images.githubusercontent.com/16719527/75103779-0979db80-5643-11ea-99c9-f7344c8f0edd.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

let button = PublishSubject<Void>()
let textField = PublishSubject<String>()

let observable = button
    .withLatestFrom(textField)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

textField.onNext("가")
textField.onNext("가나")
button.onNext(())
textField.onNext("가나다")
button.onNext(())
button.onNext(())

/*
가나
가나다
가나다
*/
```

`button`이 값을 내보낼 때, 이 값 대신에 `textField`의 가장 마지막 값을 받아온다.

<br />

<br />

### sample(_:)

withLatestFrom와 마찬가지로 sample은 trigger observable에서 값이 방출되면, 다른 observable에서 최근의 값을 가져와 내보낸다. 그러나 가장 마지막 trigger 이후에 최근의 값이 있다면 내보내고, 새로운 데이터를 받지않았다면 sample은 아무것도 내보내지 않는다.

<br />

<p align="center"><img width="500" alt="image-20200219165822974" src="https://user-images.githubusercontent.com/16719527/75103780-0979db80-5643-11ea-8b1f-e368c42a64f3.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

let button = PublishSubject<Void>()
let textField = PublishSubject<String>()

let observable = textField
    .sample(button)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

button.onNext(())
textField.onNext("가")
textField.onNext("가나")
button.onNext(())
textField.onNext("가나다")
button.onNext(())
button.onNext(())

/*
가나
가나다
*/
```

마지막에 `button`이 두 번 `next` 이벤트를 보냈지만, `"가나다"`는 한번만 출력됐다. `textField`가 새로운 값을 내보내지 않았기 때문~!! `withLatestFrom`에 `distinctUntilChanged()`를 추가해서 똑같은 동작을 만들 수 있지만, 이렇게 간단하게 하나의 오퍼레이터로 만들 수 있다.

<br />

주의할 점은 `withLatestFrom`은 파라미터로 데이터 observable을 받는다는 거고, `sample`은 트리거 observable을 받는다는 것!!

<br /><br /><br />

## Switches

### amb

`amb`는 ambiguous에서 나온 것. `amb`는 두 observable의 ambiguity를 해결하는 observable을 만든다.

`amb`는 두 observable을 모두 구독하고, element가 방출되기를 기다린다. 하나를 받으면?! 그것만 유지하고 다른 하나는 구독을 끊는다. 즉, 첫 번째로 활성화된 observable만 유지한다.

<br />

<p align="center"><img width="500" alt="image-20200219170759979" src="https://user-images.githubusercontent.com/16719527/75103781-0a127200-5643-11ea-88ca-d618b8b9bd36.png"></p>

<br />

```swift
let disposeBag = DisposeBag()

let left = PublishSubject<Int>()
let right = PublishSubject<Int>()

let observable = left
    .amb(right)
    .subscribe(onNext: {
        print($0)
    })
    .disposed(by: disposeBag)

right.onNext(10)
left.onNext(1)
left.onNext(2)
left.onNext(3)
right.onNext(20)
right.onNext(30)

left.onCompleted()
right.onCompleted()

/*
10
20
30
*/
```

`right`가 먼저 `next` 이벤트를 내보냈기 때문에 `amb`는 `right`의 구독만 유지하고, `left`는 끊는다.

<br />

위 코드를 바꿔보자.

```swift
left.onNext(1)
right.onNext(10)
left.onNext(2)
left.onNext(3)
right.onNext(20)
right.onNext(30)

left.onCompleted()
right.onCompleted()

/*
1
2
3
*/
```

`left`의 값을 먼저 내보내니까 `left` 값만 받아오는 것을 확인할 수 있다.

같은 타입의 observable에서만 사용이 가능한 것 같구먼.

중복 서버에 연결해 먼저 응답이 오는 서버와 접속하는 경우 등등에 유용하게 사용할 수 있겠지?

<br /><br />

### switchLatest

가장 마지막으로 받은 observable의 이벤트를 받는다.

<br />

<p align="center"><img width="500" alt="image-20200219172400583" src="https://user-images.githubusercontent.com/16719527/75103782-0a127200-5643-11ea-8aae-5e087eac64a5.png"></p>

<br />

```swift
let one = PublishSubject<String>()
let two = PublishSubject<String>()
let three = PublishSubject<String>()

let source = PublishSubject<Observable<String>>()

let observable = source.switchLatest()
let disposable = observable.subscribe(onNext: { value in
    print(value)
})

source.onNext(one)
one.onNext("Some text from sequence one")
two.onNext("Some text from sequence two")

//Some text from sequence one

source.onNext(two)
two.onNext("More text from sequence two")
one.onNext("and also from sequence one")

//More text from sequence two

source.onNext(three)
two.onNext("Why don't you see me?")
one.onNext("I'm alone, help me")
three.onNext("Hey it's three. I win.")

//Hey it's three. I win.

source.onNext(one)
one.onNext("Nope. It's me, one!")

//Nope. It's me, one!

disposable.dispose()
```

`source` observable에 가장 마지막에 푸시된 시퀀스의 항목만 출력하는 것을 볼 수 있다.

`switchLatest`는 `flatMapLatest`와 비슷한 동작을 한다. observable에서 가장 최신의 값을 맵핑하고 구독한다. 그래서 가장 최근의 구독만 활성화된다.

<br />

<br /><br />

## Combining elements within a sequence

### reduce

Swift standard library의 `reduce`와 비슷하다. 차이점은 observable 시퀀스에서 동작한다는 것! 

`reduce`는 source observable이 아이템을 방출할 때마다 클로저를 호출해 새로운 값을 만들어낸다. 그리고 source observable이 완료되면 축적된 값을 방출하고 완료된다. reduce로 만들어진  observable의 타입은 클로저의 리턴타입이 되겠지?

<br />

<p align="center"><img width="500" alt="image-20200219174050641" src="https://user-images.githubusercontent.com/16719527/75103783-0aab0880-5643-11ea-8978-7f10c8523c4b.png"></p>

<br />

reduce는 source observable이 complete 되었을 때만 계산한 값을 방출한다. 완료되지 않는 시퀀스에 이 오퍼레이터를 적용하면 아무것도 방출되지 않을 것..! 

```swift
let source = Observable.of(1, 2, 3, 4, 5)

let observable = source
    .reduce(0) { result, newValue in
        return result + newValue
    }
    .subscribe(onNext: { print($0) })

//15
```

<br />

<br />

### scan(_:accumulator:)

reduce랑 기본적인 동작은 같은데 차이점은 source observable에서 값을 받을 때마다 계산된 것을 내보낸다는 것이다. source observable이 완료되면 함께 완료된다.

<br />

<p align="center"><img width="500" alt="image-20200219174733305" src="https://user-images.githubusercontent.com/16719527/75103784-0b439f00-5643-11ea-8aef-d4d623f4fb90.png"></p>

<br />

```swift
let source = Observable.of(1, 2, 3, 4, 5)

let observable = source
    .scan(0) { result, newValue in
        return result + newValue
    }
    .subscribe(onNext: { print($0) })

/*
1
3
6
10
15
*/
```

입력 하나마다 하나의 출력이 발생한다. 값은 클로저에 의해서 누적된 값이다. 

source observable에서 element를 내보낼 때마다 `scan`은 클로저를 호출한다. 새 element와 함께 실행 값을 전달하고, 클로저가 새로 계산된 누적값을 리턴한다.

`reduce`와 마찬가지로 클로저의 리턴 타입이 `scan`으로 만들어진 observable의 타입이 된다.

`scan`은 누적 합계, 통계, 상태 등등 많은 곳에서 사용될 수 있고, `scan` observable로 상태 정보를 캡슐화하는 것이 좋다(🤔?). 그럼 지역변수를 사용할 필요가 없어지고 source observable이 완료되면 사라진다.

<br /><br /><br /><br /><br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.