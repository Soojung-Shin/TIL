# Observable - Traits

## Single

`Single`은 특수한 `Observable` 중 하나로, `.success(Value)` 이벤트나 `.error` 이벤트 딱 하나만 내보낼 수 있다. `.success` 이벤트는 `.next`와 `.completed`가 합쳐진 것!

<br />

<p align="center"><img width="437" alt="image-20200215152439378" src="https://user-images.githubusercontent.com/16719527/74584476-0ca71300-5016-11ea-8dbb-7cfdedb1732f.png"></p>

<br />

`Single`을 사용하고 싶다면, 아무 `Observable`에 `.asSingle()`을 사용해 `Single`로 변환시켜 구독할 수 있다. `.asSingle()`을 사용했을 때 만약 기존 시퀀스에서 두 개 이상의 이벤트를 방출한다면 에러를 발생시켜 최대 하나의 element만 얻는다.

<br />

### 언제 쓸까?

파일을 저장하거나, 다운로드하거나, 디스크에서 파일을 로드하거나, 기본적으로 값을 생성하는 어떤 비동기 operation 같은 상황에서 유용하게 사용될 수 있다.

1. 성공하면 딱 한 번 값을 방출하는 경우
2. 시퀀스에서 element가 딱 하나 나온다는 것을 표현하고 싶은 경우, 시퀀스에서 값이 1개보다 더 나온다면 에러가 발생한다는 것을 보장하고 싶은 경우

<br />

<br />

<br />

## Maybe

`Maybe`는 `Single`이랑 비슷한데 값을 가지지않는 성공 이벤트(`completed`)가 추가된 것이다.

<br />

<p align="center"><img width="625" alt="image-20200215153228839" src="https://user-images.githubusercontent.com/16719527/74584477-0e70d680-5016-11ea-99f0-a86f3b4dd9ab.png"></p>

<br />

기본적인 `Observable`로도 같은 기능을 만들 수 있지만, `Maybe`를 사용하면 이런 컨텍스트를 더 확실하게 표현할 수 있다.

`Maybe.create({...})` 를 사용하거나, 아무 `Observable`에 `.asMaybe()`를 붙여서 `Maybe`로 변환시켜 사용할 수 있다.

<br /><br />

### 언제 쓸까?

사진을 만들어서 저장하는 앱을 만들고 있다고 해보자. `UserDefaults`에 있는 앨범의 identifier를 사용해서 해당 앨범을 열고, 사진을 그 안에 저장할거다. 이런 경우에 앨범을 여는 동작을 `open(albumId:) -> Maybe<String>` 로 디자인할 수 있다.

- 해당 ID를 가지는 앨범이 존재하는 경우, `.completed` 이벤트를 발생시킨다.
- 사용자가 앨범을 삭제한 경우에는 새로운 앨범을 만들고 `.next` 이벤트에 새 앨범의 ID를 넣어 보낸다. 그럼 `UserDefaults`에 새로운 ID를 다시 저장할 수 있겠지?
- Photos library에 접근할 수 없는 경우나 어떤 에러가 발생한다면 `.error` 이벤트를 발생시킨다.

<br /><br />

<br />

## Completable

`Completable`은 구독이 dispose 되기 전에 `.completed`나 `.error` 이벤트 중 하나를 보낸다.

<br />

<p align="center"><img width="484" alt="image-20200215154646021" src="https://user-images.githubusercontent.com/16719527/74584478-116bc700-5016-11ea-9362-a2d1a80e6120.png"></p>

<br />

`Observable` 시퀀스에 `ignoreElements()` 오퍼레이터를 사용해 간단하게 `Completable`로 변환시킬 수 있다. `Completable.create { ... }` 로도 생성 가능.

<br /><br />

### 언제 쓸까?

아무 값도 내보내지 않는 시퀀스가 왜 필요할까? 비동기 작업의 성공, 실패 여부만 알면 되는 경우에 많이 사용된다.

사용자가 어떤 작업을 할 때마다 document를 자동저장하는 앱이 있다고 해보자. 비동기적으로 백그라운드 큐에서 document를 저장하고, 완료되면 notification으로 알려주고 실패하면 alert box를 띄우려고 한다.

저장 작업의 로직을 `saveDocument() -> Completable`인 함수로 만들어보자.

<br />

```swift
saveDocument()
  .andThen(Observable.from(createMessage))
  .subscribe(onNext: { message in
    message.display()
  }, onError: { error in
    alert(error.localizedDescription)
  })
```

`andThen` operator를 사용해서 성공 이벤트에 따라 여러개의 completables이나 observables을 연결(chain)하고, 그 결과를 구독한다. 하나라도 에러가 발생하면 `.error` 이벤트가 발생한다.

<br />

<br /><br />

-----

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.