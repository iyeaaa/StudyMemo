GCD에서는 디스패치 그룹일 때, 비동기 디스패치 그룹에 대해 살펴보았다.
이 때는 enter(), leave() 함수를 통해 그룹이 종료되는 시점을 체크할 수 있었다.
이 개념과 비슷하다.

비동기 함수를 Operation에 넣고 큐로 전달했다고 하자.
그러면 Operation에서 비동기 함수를 다른 스레드에 보낼것이고,
비동기 함수를 제외한 나머지 함수가 비동기 함수보다 먼저 끝나버린다면,
비동기 함수가 작동하고 있는 중에 Operation이 끝났다고 판별된다.

하지만 여기서 Operation이 끝나는 옳은 시점은 비동기 함수가 끝났을 때다.
이런 문제를 해결하기 위해 상태 관리를 수동으로 해야한다.

상태의 종류 1. isReady 2. isExecuting 3. isFinished
하지만 이런 상태는 read-only 값이라서 설정할 수 없다.
그래서 이를 해결할수 있는 기법들을 알아본다.
또한 notificatoins도 수동으로 관리해야한다.

굳이 외울필요는 없는 내용들이다.
하지만 이 내용들은 apple이 제시한 내용에 포함되지 않아서, 프로그래머들이 구현을 해야하지만
이미 다른 개발자들이 구현을 해놓았다.
우리는 이를 가져다 쓰면 되는 내용들이다.
즉, 이해는 하지만 외울 필요는 없고 필요할 때 복사해서 가져도 되는 가벼운 내용들이다.

왜 비동기함수를 오퍼레이션으로 감쌀까?
순서 설정이 가능하기 때문이다. 비동기 작업간의 순서 연결이 가능하다.



~~~
/*

 * Copyright (c) 2016 Razeware LLC

 *

 * Permission is hereby granted, free of charge, to any person obtaining a copy

 * of this software and associated documentation files (the "Software"), to deal

 * in the Software without restriction, including without limitation the rights

 * to use, copy, modify, merge, publish, distribute, sublicense, and/or sell

 * copies of the Software, and to permit persons to whom the Software is

 * furnished to do so, subject to the following conditions:

 *

 * The above copyright notice and this permission notice shall be included in

 * all copies or substantial portions of the Software.

 *

 * THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR

 * IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,

 * FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE

 * AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER

 * LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,

 * OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN

 * THE SOFTWARE.

 */

  

  

import Foundation

  

open class AsyncOperation: Operation {

    public enum State: String {

        case ready, executing, finished

        fileprivate var keyPath: String {

            return "is\(rawValue.capitalized)"

        }

    }

    public var state = State.ready {

        willSet {

            willChangeValue(forKey: newValue.keyPath)

            willChangeValue(forKey: state.keyPath)

        }

        didSet {

            didChangeValue(forKey: oldValue.keyPath)

            didChangeValue(forKey: state.keyPath)

        }

    }

}

  

extension AsyncOperation {

    // NSOperation Overrides

    override open var isReady: Bool {

        return super.isReady && state == .ready

    }

    override open var isExecuting: Bool {

        return state == .executing

    }

    override open var isFinished: Bool {

        return state == .finished

    }

    override open var isAsynchronous: Bool {

        return true

    }

    override open func start() {

        if isCancelled {

            state = .finished

            return

        }

        main()

        state = .executing

    }

    open override func cancel() {

        super.cancel()

        state = .finished

    }

}
~~~