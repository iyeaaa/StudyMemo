iOS 개발자가 되려면 정복해야할 많은 정보들이 있는데, 그 중 하나가 애플이 권장하는 MVC 패턴이다.

MVC 패턴은 이해하기 쉽고 친숙해서 iOS 개발에 굉장히 잘 쓰이는데, 알아야할 유의사항이 있다.

### MVC란

MVC 패턴은 3가지로 구성된다.

- Model
	데이터가 존재하는 공간이다.
	persistence, model objects, parsers, managers, and networking 코드들이 존재한다.

- View
	앱 내의 보이는 곳, 인터페이스를 담당하는 공간이다.
	View에서는 보통 어떤 특정 앱에서만 사용하는 알고리즘이나, 코드들을 사용하기보다 사용자에게 시각적으로 표시하는 부분들을 담당하기 때문에 재사용하고, 확장하기 좋다.

- Controller
	컨트롤러는 뷰와 모델의 상호작용을 돕는 역할을 한다.
	사용자가 뷰에게 어떤 입력을 했을 때 컨트롤러가 이를 받아 입력받은 모델의 역할을 수행할 수 있도록하고, 반대로 모델에서 컨트롤러에 요청을 해서 뷰를 변경시키는 등의 역할을 한다고 보면 된다.
	이 때, 컨트롤러는 뷰가 어떤 형태인지 자세히 알 필요없이 뷰와 통신하기 위해 정의된 프로토콜을 사용하는 방식이있다.



![MVC Diagram](https://koenig-media.raywenderlich.com/uploads/2019/01/01-MVC-Diagram-480x241.png)

