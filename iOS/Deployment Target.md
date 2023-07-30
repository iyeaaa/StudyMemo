
최소 OS 지원 버전.

예를들어 앱을 실행 중에 앱의 평점을 메길 수 있는 SKStoreReviewController는iOS 10.3 버전부터 지원하는 기능이므로, 그에 맞에 Deployment Target을 설정해야한다.

Q1. PROJECT iOS Deployment Target vs TARGETS iOS Deployment Target

A1. TARGETS이 PROJECT를 OVERRIDE 한다.

Q2. PROJECT iOS Deployment Target 과 TARGETS iOS Deployment Target 이 다를 경우에는?

A1. TARGETS iOS Deployment Target 이 Minumum 버전이 된다.

[참고 블로그](https://adervise1.tistory.com/entry/iOS-Deployment-Target)