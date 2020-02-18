# Share Operator

observable은 lazy, pull-driven 시퀀스다. observable에 몇 개의 오퍼레이터를 호출한다고 observable이 실제로 적용되지 않고, `subscribe(...)`를 호출해야 observable은 활성화되어 elements를 만든다.

<br />

아래 예시를 보자. 아래의 observable은 subscribe 될 때마다 `create` 클로저를 호출한다. (그럼 각 subscribe에 계속 새로운 observable 인스턴스가 할당되겠지?)

<br />

```swift
var start = 0

func getStartNumber() -> Int {
    start += 1
    return start
}

let disposeBag = DisposeBag()

let numbers = Observable<Int>.create { observer in
    let start = getStartNumber()
    
    observer.onNext(start)
    observer.onNext(start + 1)
    observer.onNext(start + 2)
    observer.onCompleted()
    
    return Disposables.create()
}

numbers
    .subscribe(
        onNext: {
            print("subscribe1 : \($0)")
        },
        onCompleted: {
            print("----------")
        }
    )
    .disposed(by: disposeBag)

/*
subscribe1 : 1
subscribe1 : 2
subscribe1 : 3
*/

numbers
    .subscribe(
        onNext: {
            print("subscribe2 : \($0)")
        },
        onCompleted: {
            print("----------")
        }
    )
    .disposed(by: disposeBag)

/*
subscribe2 : 2
subscribe2 : 3
subscribe2 : 4
*/
```

같은 observable을 구독했지만 출력된 결과는 다른 것을 볼 수 있다. `subscribe(...)` 를 호출할 때마다 새로운 Observable을 만들어 구독하는 것이다. 그래서 각 복사가 이전의 것과 같다는 것을 보장할 수 없다. 그리고 각 Observable이 동일한 시퀀스를 만들더라도, 각 구독에 대해서 계속 중복된 observable을 만드는 것은 불필요하다. (메모리 문제?)

<br />

<p align="center"><img width="695" alt="image-20200216123751539" src="https://user-images.githubusercontent.com/16719527/74712637-1923a980-526a-11ea-8721-28de417b896c.png"></p>

<br />

<br />

이런 문제를 피하기 위해서 `share()` 오퍼레이터를 사용해 구독을 공유할 수 있다. Rx 코드의 일반적인 패턴으로 하나의 source observable에서 필터링을 통해 여러 필요한 시퀀스를 만들 수 있다.

<br />

<p align="center"><img width="715" alt="image-20200216123758985" src="https://user-images.githubusercontent.com/16719527/74712641-1c1e9a00-526a-11ea-82a2-fd09b8f4140f.png"></p>

<br />

<br />

`share` 오퍼레이터는 첫번째 구독자가 추가되었을 때만 구독을 만든다. 그 이후에 누군가가 시퀀스를 구독하기 시작한다면, `share`는 이미 생성된 구독을 구독자에게 공유한다. 만약 공유된 시퀀스에 대한 구독이 모두 dispose 됐다면 공유된 시퀀스를 dispose 한다. 그 후에 누군가가 새로운 구독을 시작한다면 새로운 시퀀스를 만들어 제공한다.

share를 사용할 때 주의해야할 점은 `share()`는 새로운 구독자가 생겼을 때 구독하기 이전에 방출된 값들을 다시 전달해주지는 않는다는 것이다. `share(replay:scope:)` 등을 사용해 방출된 값들의 버퍼를 만들어 새 구독자에게 제공해줄 수는 있다.

<br />

<br /><br />

(그래서 위 예시 코드에서 `onCompleted()`를 지우고 `share()`를 붙였는데 첫번째 subscribe만 출력되었구만.. 구독이 종료되지 않았으니까 공유된 시퀀스는 dispose 되지 않고 유지되는데, 지나간 값은 출력해주지 않아서 두번째 subscribe는 출력되지 않은것.

거기에 `onCompleted`를 적용하면 이미 하나의 구독이 끝난 후에 다음 구독을 시작하기 때문에 결국 새로운 Observable이 다시 만들어져서 두 개의 구독에 각자 다른 observable이 생성됨.)

<br />

위의 예시에 `share(replay:)`를 적용했다.

```swift
var start = 0

func getStartNumber() -> Int {
    start += 1
    return start
}

let disposeBag = DisposeBag()

let numbers = Observable<Int>
    .create { observer in
        let start = getStartNumber()
        
        observer.onNext(start)
        observer.onNext(start + 1)
        observer.onNext(start + 2)
        //observer.onCompleted()

        return Disposables.create()
    }
    .share(replay: 3)
    
numbers
    .subscribe(
        onNext: {
            print("subscribe1 : \($0)")
        },
        onCompleted: {
            print("----------")
        }
    )
    .disposed(by: disposeBag)

/*
subscribe1 : 1
subscribe1 : 2
subscribe1 : 3
*/

numbers
    .subscribe(
        onNext: {
            print("subscribe2 : \($0)")
        },
        onCompleted: {
            print("----------")
        }
    )
    .disposed(by: disposeBag)

/*
subscribe2 : 1
subscribe2 : 2
subscribe2 : 3
*/
```

`share(replay:)`를 사용해서 해당 시퀀스가 공유되고있는지 확인해볼 수 있었다. `subscribe2`에서 위처럼 2부터 시작하는 시퀀스를 새로 만든 것이 아니라 `subscribe1`과 같은 시퀀스를 구독하고 있다.

<br />

완료되지 않은 observable에 `share()`를 사용하면 안전하고, 종료(completion) 후에 새로운 구독을 만든다는 것(새로운 시퀀스를 만든다는 것이겠지?)을 보장할 수 있다. (🤔??) 

<br />

<br /><br />

### share() vs share(replay: 1)

서버로 요청을 보내서 응답을 받는 상황이라고 생각해보자. `URLSession.rx.response(request:)`는 서버로 요청을 보내서 응답을 받으면 `.next` 이벤트를 딱 한번만 내보내고 completed된다.

이런 상황에서 만약에 observable이 완료되고, 그걸 다시 구독한다면 새로운 subscription이 만들어지고 서버에 똑같은 요청을 다시 보내게된다.

이런 상황을 막기위해서 `share(replay:scope:)` 오퍼레이터를 사용할 수 있다. 이 오퍼레이터는 가장 최근의 요소들을 버퍼(`replay` 파라미터로 버퍼 사이즈 지정)에 저장하고 있다가 새로운 구독자가 생기면 그걸 보내준다. 그래서 request가 complete 됐어도 새로운 구독자가 생기면 이전에 실행했던 버퍼에 있는 응답을 전달한다.

`scope` 에는 두 가지가 있는데 `.forever` 는 버퍼링된 네트워크 응답이 영구적으로 유지된다. 새로운 구독자는 이 버퍼링된 응답을 받는다. `.whileConnected`는 구독자가 있는 동안에는 유지하다가 구독자가 모두 없어지면 버퍼를 폐기한다. 그 후에 새로운 구독자는 새로운 네트워크 응답을 받는다.

`share(replay:scope:)` 는 complete 된다는 것이 예상되는 시퀀스나 작업량이 많고 여러번 구독되는 시퀀스에 사용하면 좋다. subscription이 추가되는 상황에서 observable이 다시 생성되는 것을 방지할 수 있다. 새 구독자에게 자동으로 마지막 n개의 이벤트를 전달해주기 위해서 사용할 수도 있다.

<br />

<br /><br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.