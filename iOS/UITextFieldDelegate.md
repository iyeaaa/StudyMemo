
![[Pasted image 20230725212533.png|400]]

위 사진에서 searchField라는 UITextField를 생성하고 위임자 설정을 해주고 있다.

![[Pasted image 20230725213337.png|500]]

UITextFieldDelegate 프로토콜을 채택해서 ​searchFiled에서 특정 이벤트가 생겼을 때 어떤 함수를 실행해줄것인지 설정해줄 수 있다.


### textFieldShouldBeginEditing

	텍스트 필드를 클릭하면 textFieldShouldBeginEditing 메서드가 호출된다.
	Edit을 시작할 것인지 묻는다고 생각하면 된다.
	false를 리턴하면 키보드도 나타나지 않고 입력 자체가 불가능하다.
	true가 반환되면 textFieldDidBeginEditing, textFieldShouldClear을 호출한다.


### textFieldDidBeginEditing

	입력이 시작됨은 확정되었으므로, 입력이 시작되었을 때 실행할 코드를 작성하면 된다.



### textFieldShouldClear

	텍스트를 모두 지울것인지 묻는 함수다.
	true를 리턴하면 텍스트를 모두 지워버리고, false를 리턴하면 텍스트를 놔둔다.

​
### textFieldShouldReturn

	키보드에서 return을 누르거나 키보드에서 입력후 엔터를 치면
	textFieldShouldReturn 이 호출된다.
	근데 도대체 반환값이 어떤역할을 하는지 모르겠다.
	공식문서에 따르면
	'true if the text field should implement its default behavior for the return button; otherwise, false.'
	라는데, 도대체 어떤 기본동작이 있는지 모르겠다. 몇번을 테스트해도 변화가 안보였다.
​

### textFieldShouldEndEditing

	TextField의 endEditing(true)를 실행하면 호출되는 함수다.
	편집을 종료할지 묻는 함수라고 생각하면 된다.
	반환값으로 true를 리턴하면 편집이 종료되면서
	textFieldDidEndEditing이 호출되고 키보드가 사라진다.
	false가 리턴되면 그냥 끝난다.


### textFieldDidEndEditing

	텍스트의 모든 입력이 끝났을 때 어떤 동작을 실행할지 정하면 된다.