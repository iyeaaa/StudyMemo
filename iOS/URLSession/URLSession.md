
앱과 서버 간 데이터를 주고받는 API를 제공하는 클래스.
HTTP를 포함한 몇 가지 프로토콜과 통신할 수 있게하고 인증, 쿠키관리, 캐시관리 등을 지원한다.
URL 세션은 비동기적으로 작동하며 iOS의 대표적인 Alamofire, Moya 또한 URLSession을 기반으로 구현되었다.


## 형태

HTTP 통신은 두 가지 구조를 가지는데 Request / Response 이다.
이에 따라 URLSession도 같은 구조를 가지고, 총 4가지 형태를 가지게 된다.

Request 
- URL 인스턴스를 통해 직접 통신
- URLRequest 인스턴스를 만들어 옵션을 설정하고 통신

Response
- 설정된 Task의 Completion Handler 형태
- URLSessionDelegate를 통해 지정된 메서드를 호출


## 동작 순서

1. URLSessionConfiguration 결정
2. Session 생성
3. 사용할 Task 결정, 적절한 Response 메서드 작성
4. Task 실행
5. Task 완료 후 Response 메서드 실행


## 유형

URLSession의 유형은 URLSession의 프로퍼티인 configuration에 의해 결정된다.
configuration은 URLSessionConfiguration 타입이다.

1. 공유 세션

```
URLSession.shared()
```

싱글톤 세션
설정 객체를 가지고 있지 않으며 가장 기본적인 데이터 전송만을 지원한다.
커스터마이징이 불가능하며 아주 제한적인 기능만을 사용할 때 유용하다.

2. Default 세션

```
URLSession(configuration: .default)
```

shared 인스턴스처럼 아주 기본적인 기본만을 제공하지만
커스터마이징이 가능하며 Delegate를 설정할 수도 있다.

3. Ephemeral 세션

```
URLSession(configuration: .ephemeral)
```

사생활 보호 브라우저 모드처럼 아주 기본적인 기능을 제공하나
캐시, 쿠키, 인증정보를 디스크에 남기지 않는다.

4. Background 세션

```
URLSession(configuration: .background)
```

업로드나 다운로드를 백그라운드 상태에서 진행할 수 있게 해준다.


## 유의사항

URLSessionConfiguration 객체가 한번 세션에 설정되었으면, 이를 수정할 수 없고
바꾸고 싶을 떄는 새로 인스턴스를 생성해야한다.


### +

- dataTask 메서드를 실행해서 검사하는 error는 에러가 발생하지 않았을 때 nil을 반환한다.
- 