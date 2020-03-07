# Error Handling

## Managing errors

아무도 앱에 에러가 발생하지 않는다고 보장할 수 없다. 언제나 어떤 유형의 에러 처리 메커니즘이 필요하다.

<br />

**주로 발생하는 에러들**

- **No internet connection**

  인터넷을 연결해서 데이터를 검색하고 처리해야할 때, 디바이스가 오프라인 상태인 경우 이를 감지해서 적절하게 응답할 수 있어야 한다.

- **Invalid input**

  사용자가 정해진 입력 형식이 아닌 다른 것을 입력했을 때, 예를 들어 전화번호 입력창에 문자를 입력한다던지?

- **API error, HTTP error**

  API에서 발생하는 에러는 매우 다양하다. HTTP 에러(응답 코드 400 ~ 500)도 발생할 수 있다. 또 JSON response의 status 필드 사용? 같은 에러도 발생할 수 있다.

<br />

**처리하는 법**

- **Catch**

  지정된 default 값으로 에러를 처리한다.

  <img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200301180741558.png" alt="image-20200301180741558" style="zoom:50%;" />

- **Retry**

  성공할 때까지 지정된(아니면 무제한...) 횟수만큼 다시 시도한다.

  <img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200301180827180.png" alt="image-20200301180827180" style="zoom:50%;" />

<br />

<br />

<br />

## Throwing errors

### RxCocoa - URLSession 에러처리

아래는 RxCocoa의 URLSession+Rx 파일 중 일부분이다.

```swift
    public func data(request: URLRequest) -> Observable<Data> {
        return self.response(request: request).map { pair -> Data in
            if 200 ..< 300 ~= pair.0.statusCode {
                return pair.1
            }
            else {
                throw RxCocoaURLError.httpRequestFailed(response: pair.0, data: pair.1)
            }
        }
    }

```

`URLRequest`에서 생성된 `Observable<Data>`를 리턴하는데, 응답 코드 200~300의 값들은 데이터를 반환하고, 그 외의 코드는 `RxCocoaURLError.httpRequestFailed(response: pair.0, data: pair.1)`를 throw하는 것을 볼 수 있다. 에러가 발생하면 return하지 않는다.

RxCocoa는 이렇게 기본 Apple framework에서 반환된 시스템 오류를 랩핑한 에러들을 랩핑한 에러들을 가지고있다. 에러에 대한 더 자세한 디테일을 제공하고, 오류 처리 코드를 더 간단하게 작성할 수 있다.

<br />

<br />

<br />

## Catch

`catch` 오퍼레이터는 스위프트의 `do-try-catch` 플로우와 매우 비슷하다. observable이 수행하다가 뭔가 잘못되면 error를 감싼 이벤트를 보낸다.

RxSwift에는 두 가지 중요한 에러 catch 오퍼레이터가 있다.

<br /><br />

### catchError(_:)

```swift
func catchError(_ handler:) -> RxSwift.Observable<Self.E>
```

클로저를 파라미터로 받고, 완전 다른 observable을 반환한다. 

![image-20200303144604167](/Users/soojung/Library/Application Support/typora-user-images/image-20200303144604167.png)

Observable에서 error가 발생했을 때, 캐싱된 이전 값들을 리턴하는 캐싱 전략 등등에서 사용할 수 있다. 이 경우에 catchError는 이전에는 사용이 가능했지만 어떤 이유로 더 이상 사용이 불가능한 값들을 리턴한다.

<br />

<br />

### catchErrorJustReturn(_:)

```swift
func catchErrorJustReturn(_ element:) -> RxSwift.Observable<Self.E>
```

에러를 무시하고 미리 정의한 값(파라미터로 넣은 값)을 리턴한다. 이 오퍼레이터는 catchError(_:)와 다르게 특정 에러에 대한 값을 리턴할 수 없으므로 더 제한적이라고 볼 수 있다. 어떤 에러든 에러라면 모두 같은 리턴 값을 갖는다.

<br />

### 주의

에러는 observable 체인에 의해서 전달된다. 그래서 만약 에러 핸들링 처리가 안되어있다면 체인의 시작에서 발생한 에러는 최종 구독까지 전달될 것이다. 따라서 한 observable에서 에러가 발생했을 때, 에러 구독이 통지되고 모든 구독이 dispose 될 수 있다는 뜻이다! observable에 에러가 발생했을 때 observable은 종료되고(terminated), 에러 후에 뒤따라오는 모든 이벤트는 무시될 것이다.

<img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200303145835000.png" alt="image-20200303145835000" style="zoom:50%;" />

위 그림에서 네트워크에서 에러가 발생하고 observable 시퀀스도 에러를 방출했을 때, UI를 업데이트하는 구독은 중지되고, 그 후의 어떤 업데이트도 수행하지 않는다.

예를 들어 특정 도시 이름으로 날씨를 검색하는 api를 사용하는 경우, 사용자가 어떤 도시 이름을 검색했을 때 404 not found 에러를 받는다고 해보자. 에러 처리를 하지 않았을 때 해당 observable 시퀀스는 네트워크에서 에러를 받는 순간 에러를 방출하고 종료된다. 시퀀스가 종료되었기 때문에 그 이후에 사용자가 어떤 것을 검색해도 검색 작업과 UI 업데이트는 이루어지지 않는다.

아래처럼 에러를 catch해서 처리하는 과정이 필요하다.

<img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200303150905809.png" alt="image-20200303150905809" style="zoom:50%;" />

<br />

<br />

<br />

## Retry

retry 오퍼레이터는 observable이 에러를 방출했을 때, observable 자체가 반복된다. 

<img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200303153007322.png" alt="image-20200303153007322" style="zoom:50%;" />

retry는 observable의 전체 작업을 반복한다는 것을 꼭! 기억해야 한다. 누가 retry 할지 컨트롤 할 수 없기 때문에 UI를 변경하는 side effect를 observable 안에 넣는 것은 피하는 것이 좋다.

<br />

<br />

### retry()

```swift
func retry() -> RxSwift.Observable<Self.E>
```

observable이 성공할 때까지 무한정 반복한다. 예를 들어 인터넷 연결이 안되어있을 때, 연결될 때까지 작업을 계속 반복할 수 있다. 하지만 이런 방법은 리소스가 많이 들 수 있기 때문에 무한정 반복하는 작업은 별로 추천되지 않는다.

<br /><br />

### retry(_:)

```swift
func retry(_ maxAttemptCount:) -> Observable<E>
```

파라미터로 지정된 횟수만큼만 반복된다. 이 횟수에는 제일 첫 번째 시도도 포함된다.

<br />

<br />

### retryWhen(_:)

```swift
func retryWhen(_ notificationHandler:) -> Observable<E>
```

여기서 중요한 것은 `notificationHandler`가 `TriggerObservable` 타입이라는 것이다! 이 trigger observable은 `Observable`이나 `Subject`가 올 수 있으며 임의의 시간에 retry를 트리거 하는데에 사용된다.

`retryWhen`을 사용해서 네트워크 에러가 발생했을 때 아래처럼 점진적인 백오프 전략(incremental back-off strategy)을 구현할 수 있다.

```
subscription -> error
delay and retry after 1 second
subscription -> error
delay and retry after 3 seconds
subscription -> error
delay and retry after 5 seconds
subscription -> error
delay and retry after 10 seconds
```

일반적인 명령형 코드에서는 작업을 `Operation` 으로 랩핑하거나, GCD 커스텀 랩퍼를 만들어야 할 것이다. 하지만 RxSwift에서는 짧은 코드 블록으로 구현이 가능하다.

타입이 무시될 수 있고(the type can be ignored 🤔?), 트리거가 어떤 타입도 될 수 있다는 것을 고려해서 내부 observable(트리거)가 어떤 타입을 리턴해야하는지 생각해봐야한다.

<br />

```swift
let textSearch = searchInput.flatMap { text in
    return ApiController.shared.currentWeather(city: text)
        .do(onNext: { data in
            self.cache[text] = data
        })
        .retryWhen { e in
            return e.enumerated().flatMap { attempt, error -> Observable<Int> in
                if attempt >= maxAttempts - 1 {
                    return Observable.error(error)
                }
                print("== retrying after \(attempt + 1) seconds ==")
                return Observable<Int>.timer(Double(attempt + 1), scheduler: MainScheduler.instance)
                    .take(1)
            }
        }
        .catchError { error in
            guard let cachedData = self.cache[text] else {
                return Observable.just(.empty)
            }
            return Observable.just(cachedData)
        }
}
```

<br />

```
curl -X GET 
    -H "Content-Type: application/json" 
"http://api.openweathermap.org...." -i -v
Failure (842ms): Status 404

== retrying after 1 seconds ==

curl -X GET 
    -H "Content-Type: application/json" 
"http://api.openweathermap.org...." -i -v
Failure (225ms): Status 404

== retrying after 2 seconds ==

curl -X GET 
    -H "Content-Type: application/json" 
"http://api.openweathermap.org...." -i -v
Failure (206ms): Status 404

== retrying after 3 seconds ==

curl -X GET 
    -H "Content-Type: application/json" 
"http://api.openweathermap.org...." -i -v
Failure (204ms): Status 404
```



<img src="/Users/soojung/Library/Application Support/typora-user-images/image-20200303164927780.png" alt="image-20200303164927780" style="zoom:50%;" />

트리거가 기존 error observable을 사용해 back-off 전략을 구현할 수 있다. 간단쓰~!

<br />

<br />

<br />

---

- Artwork/images/designs: from RxSwift: Reactive Programming in Swift book, available at http:// www.raywenderlich.com.