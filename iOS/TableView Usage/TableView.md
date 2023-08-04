
~~~
class ChatViewController: UIViewController {
    @IBOutlet weak var tableView: UITableView!
    
    override func viewDidLoad() {
        tableView.dataSource = self
    }
}
~~~

~~~
extension ChatViewController: UITableViewDataSource {
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return messages.count
    }

    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: K.cellIdentifier, for: indexPath)
        cell.textLabel?.text = messages[indexPath.row].body
        return cell
    }
}
~~~

UITableViewDataSource 프로토콜을 준수한 후
tableView(_ :numberOfRowsIn:) 에 행 개수를 리턴하고
tableView(_ :cellForRowAt:) 에 셀을 만들어 리턴해줘야 한다.


~~~
class ChatViewController: UIViewController {
    @IBOutlet weak var tableView: UITableView!
    
    override func viewDidLoad() {
        tableView.delegate = self
    }
}
~~~

~~~
extension ChatViewController: UITableViewDelegate {
    func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        print(indexPath.row)
    }
}
~~~

UITableViewDelegate를 준수하고
tableView(_ :didSelectRowAt:)에 셀을 선택했을 때 실행할 함수를 입력한다.


### TableView 체크박스 문제 해결

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

