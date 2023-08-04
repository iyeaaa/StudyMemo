
1. iOS Navigation Controller

segue를 Button - Page로 연결하면 클릭했을 때 바로 이동할 수 있지만
Page - Page로 연결하면 식별자를 필요로한다.

~~~
performSegue(withIdentifier: "identity", sender: self)
~~~

이런식으로 화면을 이동할 수 있지만, 문자열은 오타가 나더라도 컴파일 에러를 발생하지 않기에 identity를 작성하는 과정에서 개발자가 실수하기 매우 쉽다.

이에 대한 대응책으로, 상수파일을 이용해 문자열을 작성하는 횟수를 최대한 줄이는 방법을 채택할 수 있다.
별거는 아니고 Constants라는 파일과 그 안에 구조체를 생성한 후 우리가 실수할만한 String들을 타입 프로퍼티로 저장해놓고 따로 관리해서 사용하는 방법이 있다.

~~~
struct K {
    static let appName = "⚡️FlashChat"
    static let cellIdentifier = "ReusableCell"
    static let cellNibName = "MessageCell"
    static let registerSegue = "RegisterToChat"
    static let loginSegue = "LoginToChat"

    struct BrandColors {
        static let purple = "BrandPurple"
        static let lightPurple = "BrandLightPurple"
        static let blue = "BrandBlue"
        static let lighBlue = "BrandLightBlue"
    }

    struct FStore {
        static let collectionName = "messages"
        static let senderField = "sender"
        static let bodyField = "body"
        static let dateField = "date"
    }
}
~~~

~~~
self.performSegue(withIdentifier: K.registerSegue, sender: self)
~~~

이런식으로 관리해서 실수를 줄일 수 있다.

2. Swift Loop

~~~
let titleText = "⚡️FlashChat"

for letter in titleText {
    print(letter)
}

output:
⚡️
F
l
a
s
h
C
h
a
t
~~~

String에서 for loop도 가능하다.


3. Cocoapods and the Swift package Manager

코코아 팟이 설치되어있다는 가정하에 코코아 팟을 사용하려면 
프로젝트 폴더로 이동한 terminal에서

~~~
pod init
~~~
을 진행한 후

~~~
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'Flash Chat iOS13' do

  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!
  
  # Pods for Flash Chat iOS13

end
~~~
ruby 언어로 되어있는 pod 파일을 Xcode로 열 수 있는데,
#는 ruby의 주석처리 방식이고 # platform :ios, '9.0'는 최소 iOS 지원버전을 9로 지정함을 뜻한다.

~~~
platform :ios, '9.0'

  

target 'Flash Chat iOS13' do

  use_frameworks!
  
  # Pods for Flash Chat iOS13

  pod 'CLTypingLabel'

end
~~~

이런식으로 podfile에서 CLTypingLabel 패키지를 추가해야한다.

xcode에서 실제로 pod 파일을 빌드하면 에러가 생기는데,
swift에서 업데이트된 부분을 변경하지 않았기 때문이다.

release를 보면 0.4.0까지 나왔는데, 코코아팟에서 인식하지 못하므로 우리가 직접 버전을 설정해줘야 한다.
[Podfile Syntax Reference](https://guides.cocoapods.org/syntax/podfile.html)에서 확인할 수 있듯이 파일 명 뒤에 ~> '버전명' 으로 버전을 지정할 수 있다.

~~~
platform :ios, '9.0'

target 'Flash Chat iOS13' do
  use_frameworks!

  # Pods for Flash Chat iOS13
  pod 'CLTypingLabel', '~> 0.4.0'
end
~~~

이렇게 변경한 후 pod install을 실행하면 에러가 생기는데,
[CLTypingLabel](https://github.com/cl7/CLTypingLabel/blob/master/CLTypingLabel.podspec)의 [podspec](https://github.com/cl7/CLTypingLabel/blob/master/CLTypingLabel.podspec)파일을 보면 

~~~
s.platform = :ios, '10.0'
~~~

iOS 10 이상에서만 가능함을 알 수 있으므로

~~~
platform :ios, '13.0'

target 'Flash Chat iOS13' do
  use_frameworks!

  # Pods for Flash Chat iOS13
  pod 'CLTypingLabel', '~> 0.4.0'
end
~~~
다음과 같이 10 이상의 버전으로 올려준 후 pod install을 진행하면 0.4.0 버전으로 설치할 수 있다.

설치된 pod을 삭제하려면 pod 'CLTypingLabel', '~> 0.4.0' 이 내용을 지운 후
다시 pod install을 진행하면 된다.


### package dependency manager 

코코아팟은 package dependency manager 의 일종으로
의존성 관리자는 프로젝트에 추가한 모든 패키지의 변화를 모니터한다.
10개의 다른 패키지를 프로젝트에 추가하면 대신 관리해주어 매우 편리하다.

대표적인 의존성 관리자는 다음과 같다.

- CocoaPods
- Swift Package Manager 
- Carthage


4. Firebase Firestore as a Cloud Databse, FIrebase Authentication

[Firebase api 문서](https://firebase.google.com/docs?hl=ko%2Fios)에서 자세한 내용 확인가능.

5. [[Navigation Controller]]

6. [[Custom cell]]

7. [[ViewController Lifecycle]]

8. Realm

[@objc dynamic](https://ios-daniel-yang.tistory.com/78) 

