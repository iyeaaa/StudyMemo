
### 서론

GCD는 Operation의 비해 비교적 간단한 일이나, 함수를 사용하는 메서드 위주의 작업을 처리하는데에 사용한다.

그런 GCD를 기반으로 조금 더 복잡한 일(취소, 순서지정, 일시중지 등)과 객체에 대한 일을 처리하기 위해 Operation이 등장했다.

Operation은 기존의 스크롤하며 앱의 비동기처리에서 이미지가 지나간 이후의 이미지를 표시할 필요가 없는 등 앱의 더 나은 효율을 위해 사용할 수 있다.


### Operation == 객체화 된 작업

Operation은 작업 자체가 클래스화 되서 클래스의 인스턴스를 이용하는 개념.
객체를 Operation큐에 넣어서 이용할 수 있다.

어떤 단위적인 작업(데이터 다운로드, 압축풀기, 이미지 필터 적용하기)을 클래스로 만들어서 기능을 UP시켰다. 재사용성이 높아진다.

오퍼레이션 큐 없이 그냥 사용하면, 1번스레드에서 동기적으로 사용하고, 인스턴스화해서 한번만 실행가능하다. 다시 사용하려면 다시 객체를 생성해야한다.

또한, 오퍼레이션을 오퍼레이션큐와 결합해서 사용하면 '비동기적'으로 많은 추가기능을 사용할 수 있다.

오퍼레이션의 기능에는 취소, 순서지정이 주요기능이고.
추가적으로 상태체크, KVO notifications, Qos수준, completionBlock 제공된다.

오퍼레이션 메서드 및 변수
- start()
- cancel() 중간에 작업 취소
- 상태체크를 위한 Bool(isReady, isExecuting, isCancelled, isFinished)

오퍼레이션은 겉에서 오퍼레이션의 상태를 체크할 수 있다.
오퍼레이션의 라이프 사이클
- 오퍼레이션 클래스 정의
- 객체화 시키면 isReady 상태
- start() 실행하면(객체의 main() 트리거시킴) isExecuting 상태
- cancel() 실행되서 중간에 취소되면 isCancelled 상태
- 진행되서 마치면 isFinished
실제로는 순서지정 / 취소를 하기위해 이러한 상태체크를 한다.

Operation자체는 apple에서 만든 추상클래스다.
개발자가 이를 사용하기 위해서는 추상클래스를 상속해야하고, 그 안에
- input
- output
- main()
을 구현해야한다.

예를들어 데이터 다운로드를 하려면 url이 필요하고 그 결과물인 압축파일이 생성된다.
여기서 input에 url을 위한 변수를 정의하고 mian()에 데이터 다운로드를 위한 코드를 작성하고
output에 압축파일을 저장하기 위한 변수를 정의해야한다.


### Operation Queue

일반적으로 오퍼레이션을 담아서 사용하는 오퍼레이션 큐도 클래스다.

1. 오퍼레이션 큐는 몇개의 쓰레드를 사용할 것인지 구체적인 설정이 가능하다.

	디폴트는 -1이며 이 때는 시스템이 알아서 개수를 결정한다. 
	즉 기본적으로 Concurrent로 작동한다.
	maxConcurrentOperationCount에 몇개의 스레드를 사용할 지 설정할 수 있다.

2. Qos

	디폴트는 background고, 구체적인 설정이 가능하다.
	GCD의 글로벌 큐에서 unspecified가 빠진 5개의 Qos를 가지며
	qualityOfService에 열거형으로 설정해 줄 수 있다.
	GCD와 마찬가지로 오퍼레이션 자체의 품질을 설정하면 큐의 품질이 승격되며,
	다만, 기반이 되는 GCD까지 설정할 수 있어, 이를 설정하면 가장 우선적으로 적용된다.

3. 오퍼레이션 추가 방법

	오퍼레이션, 오퍼레이션 배열, 클로저를 넣을 수 있고,
	오퍼레이션 큐에 넣고 쓰레드에 배정되면 isExecuting 상태가 된다.

4. 오퍼레이션이 한번 실행되거나, 취소되면 오퍼레이션 큐를 떠난다

5. waitUntilAllOperationsAreFinished()을 실행하면 동기적으로 기다린다.

	 당연히 main쓰레드에서 실행하면 안된다.

6. 일시정지 - 재개 가능하다.

	 isSuspended의 true / false를 직접 설정할 수 있다.
	 isSuspended를 true로 설정하면 실행되던 오퍼레이션은 계속 실행되고,
	 새로 추가된 오퍼레이션은 false가 될때까지 스케줄링 되지 않는다.


### Block Operation

안에 Block을 내장하는 오퍼레이션, 내부에 클로저를 가진 오퍼레이션이다.
내부에 있는 클로저들을 DispatchQueue.global() 큐로 보내서 다른 쓰레드에서 일을 하도록 시킴.
디스패치 그룹과 유사하게 동작한다. (내부의 클로저 작업들이 모두 종료되었을 때, 알려준다.)
기본적으로 동기적으로 작동한다. 하지만 다른 큐로 보내서 비동기적으로 실행하게 할 수도 있다.

기존의 오퍼레이션에서 Block이라는 개념을 추가했기 때문에, 원래 존재하던 오퍼레이션의 기능들도 사용할 수 있다.

일반적으로 Operation을 많이 사용하는데, GCD같이 단순한 클로저가 필요한 경우에 Block Operation을 사용한다.
