특정 Swift 버전 또는 플랫폼 및 OS버전과 관련된 선언의 생명주기를 나타냄.

### #available

~~~
if #available(iOS 11.0, *) {
	// do something
} else {

}
~~~


이 반환값은
~~~
#available(iOS 11.0, *)
~~~
iOS 11 이상이 지원되는 플랫폼, 즉 iOS11 이상의 플랫폼에서 실행할 구문이면 true를 반환하고, 아니라면 false를 반환한다.


### @available

함수, 클래스 또는 프로토콜 앞에 놓여서 한정된 플랫폼에서만 함수를 사용할 수 있도록한다.
설정하지 않은 플랫폼에서 함수를 사용하면 컴파일 에러를 띄워준다.

~~~
@available(iOS 17, *)
func f() {}
~~~
라는 함수 f를 선언하면 f는 iOS 17이상에서만 사용할 수 있는 함수가 된다.

만약 [[Deployment Target]]  이 15라고 가정하면,
iOS 버전이 15, 16에서는 f를 사용할 수 없으므로 다음과 같이 사용해야한다.

~~~
if #available(iOS 17, *) {
    f()
}
~~~

