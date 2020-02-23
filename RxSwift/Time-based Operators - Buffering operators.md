# Time-based Operators - Buffering operators

버퍼링을 다루는 오퍼레이터. 새로운 구독자에게 과거의 elements를 다시 전달하거나 버퍼링해 전송한다. 언제 어떻게 예전 elements나 새로운 elements를 언제 어떻게 전달할지를 제어할 수 있다.

<br /><br />

## Replaying past elements

미래의 구독자에게 시퀀스가 방출한 과거의 아이템 일부나 전부를 전달해야할 때 사용한다.

### replay(_:)

source observable에서 방출되는 최근의 replayedElements를 기록하는 새로운 시퀀스를 만든다. 새로운 옵저버가 구독할 때마다, 버퍼링된 요소가 있는 경우 즉시 버퍼링된 elements를 받은 후 일반 옵저버처럼 새 element를 계속 받는다.

`replay`의 매개변수로 지정된 개수로 버퍼의 사이즈를 만들고, 구독자가 생기면 해당 버퍼를 바로 보낸다.

<br />

아래 예제와 함께 확인해보자.

```swift
let elementsPerSecond = 1
let maxElements = 5
let replayedElements = 2
let replayDelay: TimeInterval = 3

let sourceObservable = Observable<Int>.create { observer in
        var value = 1
        let timer = DispatchSource.timer(interval: 1.0 / Double(elementsPerSecond), queue: .main) {
            if value <= maxElements {
                observer.onNext(value)
                value += 1
            }
        }
        return Disposables.create {
            timer.suspend()
        }
    }
    .replay(replayedElements)

let sourceTimeline = TimelineView<Int>.make()
let replayedTimeline = TimelineView<Int>.make()

_ = sourceObservable.subscribe(sourceTimeline)

DispatchQueue.main.asyncAfter(deadline: .now() + replayDelay) {
    _ = sourceObservable.subscribe(replayedTimeline)
}

_ = sourceObservable.connect()
```

<br />

<p align="center"><img src="https://user-images.githubusercontent.com/16719527/75103667-3cbb6b00-5641-11ea-86db-319da5d37869.gif" width="300"></p>



<br />

겹쳐서 잘 안보이지만....구독 즉시 가장 최근에 방출된 2개의 아이템 `2, 3` 을 받아온다. source observable이 매초마다 아이템을 방출하고, 3초 후 다른 observable이 source observable의 구독을 시작했을 때 `replay(replayedElements)` 에 의해서 지정된 개수만큼(여기서는 2개!) 버퍼로 저장된 elements들을 받고, 그 이후로 발생하는 아이템들을 받아오는 것을 확인할 수 있다.

<br />

<br />

### replayAll()

`replayAll`은 방출한 모든 아이템을 버퍼에 저장한다.

위 예제에서 `replay(_:)`를 `replayAll()`로 변경했을 때!

<br />

<p align="center"><img src="https://user-images.githubusercontent.com/16719527/75103668-40e78880-5641-11ea-80e4-3b875d10247f.gif" width="300"></p>

<br />

구독 즉시 이전에 발생한 모든 값 `1, 2, 3` 을 받아온다. 

`replayAll`은 모든 elements를 저장하기 때문에 주의해서 사용해야 한다. 버퍼링되는 모든 elements의 수가 합리적으로 유지되는 경우에만 사용하는 것이 좋다. 예를 들어 HTTP 요청에서 쿼리에서 반환하는 데이터를 유지할 때 대략적인 메모리의 크기를 알고있다. 

종료되지 않을 수도 있거나 많은 데이터를 생성하는 시퀀스에 `replayAll`을 사용하면 메모리 문제가 발생할 수 있다. 메모리 문제로 OS가 앱을 중단시킬 수 있으니 주의해야한다!

<br />

<br /><br />

## Controlled buffering

### buffer(timeSpan:count:scheduler:)

buffer는 지정된 버퍼 사이즈만큼 버퍼가 가득차면 바로 배열을 방출하고, 지정된 시간만큼 기다렸다가 새로운 아이템들 배열을 방출한다.

source observable은 elements의 배열을 방출한다. 각 배열은 요소를 최대 count 개수만큼 가질 수 있다. 만약 timeSpan 전에 많은 요소를 받으면, 오퍼레이터는 버퍼링된 요소를 방출하고 타이머를 리셋한다. 마지막 그룹이 방출된 후 timeSpan만큼의 시간이 지나면 버퍼는 배열을 방출한다. 만약 이 timeframe 동안 받은 요소가 없다면 empty array가 방출된다.

<br />

```swift
let bufferTimeSpan: RxTimeInterval = 4
let bufferMaxCount = 2

let sourceObservable = PublishSubject<String>()

let sourceTimeline = TimelineView<String>.make()
let bufferedTimeline = TimelineView<Int>.make()

sourceObservable
    .subscribe(sourceTimeline)

sourceObservable
    .buffer(timeSpan: bufferTimeSpan, count: bufferMaxCount, scheduler: MainScheduler.instance)
    .map { $0.count }
    .subscribe(bufferedTimeline)

DispatchQueue.main.asyncAfter(deadline: .now() + 5) {
    sourceObservable.onNext("😆")
    sourceObservable.onNext("😆")
    sourceObservable.onNext("😆")
}

DispatchQueue.main.asyncAfter(deadline: .now() + 6) {
    sourceObservable.onNext("😆")
}

DispatchQueue.main.asyncAfter(deadline: .now() + 7) {
    sourceObservable.onNext("😆")
}
```

<br />



<p align="center"><img src="https://user-images.githubusercontent.com/16719527/75103670-4349e280-5641-11ea-9bad-0f7f0b345f94.gif" width="300"></p>

<br />

위에서 보면 buffer 사이즈는 2, 타이머는 4초로 지정되어있다. source observable이 아이템을 방출했을 때, 버퍼가 가득차면 데이터를 바로 방출하고, 타이머가 리셋되어 4초 후 나머지 하나가 방출된다. 받은 아이템이 없다면 빈 배열을 방출해 0이 출력되는 것을 볼 수 있다.

<br /><br /><br />

## Windows of buffered observables

### window(timeSpan:count:scheduler:)

`buffer`와 거의 비슷한 동작을 한다. 다른 점은 `window`는 배열을 방출하는 대신에 버퍼링된 아이템들의 `Observable`을 방출한다는 것이다.

<br />

```swift
let elementsPerSecond = 3
let windowTimeSpan: RxTimeInterval = 4
let windowMaxCount = 10

let sourceObservable = PublishSubject<String>()
let sourceTimeline = TimelineView<String>.make()

let timer = DispatchSource.timer(interval: 1.0 / Double(elementsPerSecond), queue: .main) {
    sourceObservable.onNext("🤔")
}

sourceObservable
    .subscribe(sourceTimeline)

sourceObservable
    .window(timeSpan: windowTimeSpan, count: windowMaxCount, scheduler: MainScheduler.instance)
    //새 observable을 받을 때마다 flatMap에서 새로운 timelineView를 추가한다.
    .flatMap { windowedObservable -> Observable<(TimelineView<Int>, String?)> in
        let timeline = TimelineView<Int>.make()
        
        stack.insert(timeline, at: 4)
        stack.keep(atMost: 8)
        
        return windowedObservable
            .map { value in (timeline, value) } //값과 타임라인을 튜플로 묶는다.
            .concat(Observable.just((timeline, nil))) //nil값을 갖는 타임라인을 뒤에 추가해 value가 nil이면 complete로 처리하도록 했다.
    }
    .subscribe(onNext: { (timeline, value) in
        if let value = value {
            timeline.add(.next(value))
        } else {
            timeline.add(.completed(true))
        }
    })
```

<br />

<p align="center"><img src="https://user-images.githubusercontent.com/16719527/75103672-4644d300-5641-11ea-8921-824b5faa4ddb.gif" width="300"></p>

<br />

source observable에서 아이템을 1초에 3개씩 방출하도록 설정했을 때, `window`의 버퍼 사이즈가 10이기 때문에 10개가 가득차면 완료시키고 다음 observable을 내보내는 것을 볼 수 있다. `buffer`와 마찬가지로 가득차면 타이머를 초기화하고 사이클을 다시 시작한다.

<br />

그럼 버퍼가 가득차기 전에 지정된 시간이 끝난다면?! source observable에서 아이템을 1초에 1개씩 방출하도록 설정했다.

<br />

<p align="center"><img src="https://user-images.githubusercontent.com/16719527/75103674-4cd34a80-5641-11ea-9f99-0472790f0df5.gif" width="300"></p>

<br />

`window`의 버퍼가 가득차지 않아도 지정된 시간이 지나면 완료되고 다음 observable을 방출하는 것을 볼 수 있다.

<br /><br /><br /><br /><br />

------

Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.









