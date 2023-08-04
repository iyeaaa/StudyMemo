
### custom cell 등록

Creating custom UI with .xib files

[xib 사용법](https://woozzang.tistory.com/189)

	1. xib 생성
	2. indentifier 설정
	3. UINib 생성
	4. UITableView의 register함수로 UINib을 cell로 등록
	5. dequeueReusableCell(withIdentifier:for:)함수로 cell 생성 후 타입캐스팅으로 사용

+)
```
**2023-08-05 03:08:52.537390+0900 MyCommunity[29116:980573] [LayoutConstraints] Unable to simultaneously satisfy constraints.**

**Probably at least one of the constraints in the following list is one you don't want.** 

**Try this:** 

**(1) look at each constraint and try to figure out which you don't expect;** 

**(2) find the code that added the unwanted constraint or constraints and fix it.** 

**(**

    **"<NSLayoutConstraint:0x6000024f4b90 UIView:0x15390f360.height == 75   (active)>",**

    **"<NSLayoutConstraint:0x6000024f4eb0 V:|-(10)-[UIView:0x15390f360]   (active, names: '|':UITableViewCellContentView:0x1539138f0 )>",**

    **"<NSLayoutConstraint:0x6000024f4c30 V:[UIView:0x15390f360]-(10)-|   (active, names: '|':UITableViewCellContentView:0x1539138f0 )>",**

    **"<NSLayoutConstraint:0x6000024f6ad0 'UIView-Encapsulated-Layout-Height' UITableViewCellContentView:0x1539138f0.height == 95.3333   (active)>"**

**)**

  

**Will attempt to recover by breaking constraint** 

**<NSLayoutConstraint:0x6000024f4b90 UIView:0x15390f360.height == 75   (active)>**
```

이런 오류가 떴었지만
height를 greater then or equal로 바꾸니 해결되었다..
해당 [답변](https://stackoverflow.com/a/69739618)에 따르면 렌더링 과정에서 충돌이 발생하는것 같다고한다.
