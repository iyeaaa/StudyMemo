
Swift의 데이터베이스

![[swift의 데이터베이스.png]]


1. 이니셜 컨트롤러 설정

컨트롤러 생성 후, 이니셜 컨트롤러를 변경하려면 화살표 위치를 설정하고 싶은 컨트롤러로 이동시켜주면 된다.
만약 화살표를 제거했다면, 컨트롤러에서 is initial View Controller를 체크해준다.
또한, 이니셜 컨트롤러가 TableViewController로 변경했다면 부모클래스를 UITableViewController로 변경해야한다.


2. TableView
	1. UITableViewController를 상속하면 delegate, datasource에 대한 설정이 필요없다.
	2. tableView.deselectRow(at: indexPath, animated: true) 를 통해 선택해제를 구현할 수 있다.
	3. cell의 accessory 설정으로 cell 오른쪽에 체크마크 등을 넣을 수 있다.


3. Alert 사용법

UIAlertController 생성
~~~
let alert = UIAlertController(title: "Add new Todoey Item", message: nil, preferredStyle: .alert)
~~~


UIAlertAction 생성
~~~
let action = UIAlertAction(title: "Add Item", style: .default) { action in
      // what will happen once the user clicks the Add Item button on our UIAlert
      self.itemArray.append(textField.text ?? "New Item")
      self.tableView.reloadData()
}
~~~


addTextField로 TextField 추가
~~~
alert.addTextField { alertTextField in
        alertTextField.placeholder = "Create new Item"
        textField = alertTextField
}
~~~


addTextField로 action 추가
~~~
alert.addAction(action)
~~~


alert 실행
~~~
present(alert, animated: true, completion: nil)
~~~



4. appdelegate
~~~
func application(_ application: UIApplication, didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool
~~~

앱이 로드될 때 호출되는 메서드다.
때문에 나머지 앱들이 다운되더라도 상관없다.
viewController 등에서 viewDidLoad 메서드 전에 호출된다.


~~~
func applicationWillResignActive(_ application: UIApplication)
~~~

앱이 활성상태일 때 전화가 오는 등의 이유로 비활성 상태로 전환되기 직전해 호출되어
사용자가 데이터를 잃지 않게 돕는 함수다.


~~~
func applicationDidEnterBackground(_ application: UIApplication)
~~~

앱이 백그라운드를 지원한다면, 사용자가 앱을 종료할 때 applicationWillTerminate: 대신에 이 메서드가 호출된다.



  ~~~

    func applicationWillEnterForeground(_ application: UIApplication) {

        // Called as part of the transition from the background to the inactive state; here you can undo many of the changes made on entering the background.

    }

  

    func applicationDidBecomeActive(_ application: UIApplication) {

        // Restart any tasks that were paused (or not yet started) while the application was inactive. If the application was previously in the background, optionally refresh the user interface.

    }

~~~

~~~
func applicationWillTerminate(_ application: UIApplication)
~~~

사용자나 시스템이 앱을 종료시키기 직전에 호출되는 함수다.


5. UserDefaults


~~~
let defaults = UserDefaults.standard
~~~

UserDefaults 생성

```
self.defaults.set(self.itemArray, forKey: "TodoListArray")
```

set함수로 데이터 저장

```
print(NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true).last! as String)
```
appdelegate 함수에서 다음 출력값을 통해 저장위치 추론 가능

![[스크린샷 2023-07-31 오후 9.46.24.png]]
다음과 같이 저장된다.

```
defaults.array(forKey: "TodoListArray") as! [String]
```
배열을 저런식으로 get할 수 있다.

하지만 userDefaults는 사용자 지정 형식 데이터를 저장하지 못하는 등 지원하지 않는 데이터 형식이 많다. 또한 데이터가 조금이라도 방대하다면 매우 효율적이지 않다고 한다. 때문에 userDefaults는 사용하지 않는 편이 좋고 NSCoder를 사용해야한다.

6. The Swift Singleton Object

```
let default = UserDefaults.standard
let shardURLSession = URLSession.shared
```

```
class Car {
  var colour = "Red"

  static let standard = Car()
}

let myCar = Car.standard
print(myCar.colour)
```

이 인스턴스들은 모두 싱글톤이다.
싱글톤은 모든 클래스와 개체에 공유될 수 있다는 특징이 있다.

7. TableView 체크박스 문제점

```
let cell = UITableViewCell(style:reuseIdentifier:)
```
이 방식은 cell을 내리고 다시 올릴 때 cell이 다시 생성되는 방식으로 움직이기 때문에
선택된 체크박스가 해제된다.

```
let cell = tableView.dequeueReusableCell(withIdentifier:for:)
```
dequeue로 cell을 생성하게 되면 스크롤을 내려 cell이 화면밖으로 나갔을 때, 
밑의 cell로 움직여 cell이 재사용되기 때문에 위에 있던 체크박스가 갑자기 아래로 내려오게 된다는 문제가 있다.

이 문제를 해결하려면 체크박스 속성과 배열 항목을 연결시켜야한다.

8. [SandBox란?](https://velog.io/@gnwjd309/iOS-sandbox)

9. NSCoder

### 경로 접근하기

```
// FileManager 인스턴스 생성
let dataFilePath = FileManager.default

let dataFilePath = FileManager.default.urls(for: .documentDirectory, in: .userDomainMask).first?.appendingPathComponent("Items.plist")
```

urls 메소드는 요청된 도메인에서 지정된 공통 디렉토리에 대한 URL배열을 리턴한다.

- **for**: 폴더 지정. Download, Document 등
- **in**: 제한 설정. 그 이상은 못가게 하는…

또한, urls 메서드는 요청된 디렉토리를 식별하는 NSURL 객체의 배열이다. 사용자 도메인의 항목이 먼저 나오고 시스템 도메인의 항목이 마지막에 나오도록 도메인 마스크 상수의 순서에 따라 정렬된다.(무슨말이야..)

- appendingPathComponet 메서드
	기존의 URL, ~ /Documents/ 에서 ~ /Documents/Items.plist 로 변환해준다.
	ios16부터는 appending(path:) 메서드를 권장한다.

- appendingPathExtension 메소드
	기존 URL에 확장자를 추가해서 반환해준다.


### 데이터 인코딩, 디코딩 with Codable

```
let data = try PropertyListEncoder().encode(itemArray)
try data.write(to: dataFilePath!)
```

데이터를 인코딩해서 리턴받은 URL을 인자로 넣어주면 된다.

```
if let data = try? Data(contentsOf: dataFilePath!) {
     let decoder = PropertyListDecoder()
     itemArray = try decoder.decode([Item].self, from: data)
  }
}
```

다음과 같이 디코딩해서 사용하면 된다.


10. CoreData

![[데이터 베이스 용어.drawio.png]]
OOP의 Class = CoreData의 Entity = Database의 Table
OOP의 Property = CoreData의 Attribute = Database의 Field

예시)
![[용어.png]]

표 전체
 - 객체에서는 Class, CoreData는 Entity, Database는 Table라고 한다.

열
 -  객체에서는 Property, CoreData는 Attribute, Database에서는 Field라고 한다.

행
 - Coredata에서 NSManagedObject 타입이다.

### CoreData 작동방식

![[CoreData 작동방식.png|400]]




