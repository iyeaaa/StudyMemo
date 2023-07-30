
![[Pasted image 20230728221833.png|400]]


1. viewDidLoad

모든 IBOutlet, IBAction 뷰와 관련된 모든 개체가 연결되어 액세스 가능하다.
viewDidLoad 메서드는 뷰가 생성되었을 때 한번만 호출된다.


2. viewWillAppear

viewDidLoad함수와 달리 여러번 호출될 수 있다.
뷰가 나타나기 직전에 호출되어 사용자는 아직 아무것도 볼 수 없지만,
탐색바를 보였을 때 했던것처럼 뷰에서 어떤 변화도 관찰할 수 없기때문에,
특정 UI구성요소를 숨기거나 보여주기에 좋은 시점이다.


3.  viewDidAppear

사용자가 viewController를 볼 수 있게 된 시점에 호출된다.
카운트다운 타이머나 애니메이션를 시작하기에 좋은 시점이다.


4. viewWillDisappear

뒤로 이동하거나 현재 사용중인 viewController에서 나갈 때 호출되는 첫번째 함수다.
viewController가 사라지는 그 시점에 호출하고싶은 내용을 작성하면 된다.
예를 들어 애니메이션을 멈추고, UI의 모습을 바꾸고 싶거나 또는 탐색바를 사라지게 할 때 이 함수를 사용하면 좋다.


5. viewDidDisappear

사용자가 볼 수 없게 되었다는 거지 아직 메모리에서 해제된 상태는 아니라는 점에 유의하자.
아직 스택 백그라운드에 남아있다.


+ Tip )

	Page1의 viewDidDisappear()이 Page2의 화면이 보여지고 나서 호출됨에 유의하자