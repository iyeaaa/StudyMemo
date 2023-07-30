
### 순서 관리 (종속성 - Dependencies)

어떤 작업이 어떤 작업에 의존하기 있기 때문에, 어떤 작업이 일어난 후 어떤 작업이 일어나야한다는 의미.
다운로드 -> 압축풀기 -> 이미지필터 -> 컴플리션 처리
압축풀기 작업은 다운로드 작업에 의존한다.
이미지 필터 작업은 압축풀기 작업에 의존한다.
라고 표현한다.

단지 하나의 작업씩 순서적으로 처리한다면 하나의 쓰레드에서 처리하면 되는게 아닌가 싶겠지만,
다운로드를 동시에 2개를 하고 압축을 모두 풀어야하는 상황도 있기때문에 여러 쓰레드를 이용한다.


## 오퍼레이션큐의 순서 관리 방법

URL에서 다운로드하고, 압축파일에서 압축풀기를 하고 이미지를 내뱉는 상황을 보자.
이 상황에서 순서를 관리하려면, 오퍼레이션 큐에서 다운로드 오퍼레이션 작업이 끝나는 시점을 알아 관리하고, 다운로드가 끝나면 압축풀기 오퍼레이션 작업이 시작하도록 해야한다.

1. 순서 설정 (의존성 설정)
2. 프로토콜 설정 (만약 프로토콜을 설정하지 않으면 해당 프로토콜 역할(전달하는) 오페레이션이 필요하다.

1. 압축풀기 오퍼레이션이 다운로드 오퍼레이션에 의존하도록 한다. == 압축 풀이 오퍼레이션이 종료된 후에 다운로드 오퍼레이션이 실행되도록 한다.
2. 압축파일에 관한 설정을한다. 프로토콜을 설정하는것만으로 다운로드 오퍼레이션에서 압축파일을 압축풀기 오퍼레이션으로 전달할 수 있다. (만약 해당 프로토콜을 설정하지 않으면 압푹파일을 전달하는 오퍼레이션을 필요로 할 수 있다.)

~~~
//: [Previous](@previous)

import UIKit

import PlaygroundSupport

PlaygroundPage.current.needsIndefiniteExecution = true

//: # 의존성 설정(순서설정)하는 방법

//: ### 오퍼레이션간의 순서를 정하고, 순서대로 실행하기 위한 방법

  

//: 이미지 다운로드하는 첫번째 (비동기)오퍼레이션 정의

// (1) 이미지 다운로드하는 비동기 오퍼레이션

class ImageLoadOperation: AsyncOperation {

    var inputName: String?

    var outputImage: UIImage?

    override func main() {

        // 이 메서드는 잠깐 sleep했다가 이미지를 반환하는 시뮬레이팅 메서드임.

        simulateAsyncNetworkLoadImage(named: self.inputName) {

            [unowned self] (image) in

            self.outputImage = image

            self.state = .finished

        }

    }

}

  

//: 2-1 데이터전달을 위한 프로토콜 정의 및 채택

// 🔸🔸🔸 1) 데이터를 넘겨주기위한 프로토콜 정의하기

protocol FilterDataProvider {

    var image: UIImage? { get }

}

  

// 프로토콜 채택하기

extension ImageLoadOperation: FilterDataProvider {

    var image: UIImage? { return outputImage }

}

  

  

//: 이미지변형하는 두번째 오퍼레이션 정의

//=========================================================

// (원래형태의) 이미지 변형하는 오퍼레이션 (비교해 보기 위한)

class TiltShiftOperation1: Operation {

    var inputImage: UIImage?

    var outputImage: UIImage?

    override func main() {

        guard let inputImage = inputImage else { return }

        outputImage = tiltShift(image: inputImage)

    }

}

// =======================================================

  

//: 2-2 데이터 뽑아서 사용하기 위한 함수 내용을 main()에 정의

// (2) 이미지 변형하는 오퍼레이션 (동기 함수)

class TiltShiftOperation: Operation {

    var inputImage: UIImage?

    var outputImage: UIImage?

    override func main() {

        // 🔸🔸🔸 2) 프로토콜 채택한 앞의 오퍼레이션에서, 인풋값을 얻어내기

        var imageProvider = dependencies.filter({$0 is FilterDataProvider}).first as? FilterDataProvider

        if inputImage == .none, let dependencyImageProvider = imageProvider {

            inputImage = dependencyImageProvider.image

        }

        // 실제 작업

        outputImage = tiltShift(image: inputImage)

    }

}

  

  

//: 오퍼레이션 생성

let imageLoad = ImageLoadOperation()

let filter = TiltShiftOperation()

  

imageLoad.inputName = "train_day.jpg"

  

  

//: 1.의존성 설정

// 🔸🔸🔸 의존성 설정 "filter오퍼레이션"이 "imageLoad오퍼레이션"에 의존한다.

filter.addDependency(imageLoad)

  

  

//: 두개의 오퍼레이션을 오퍼레이션큐에 넣어서 진행 확인

let queue = OperationQueue()

  

// 큐에 오퍼레이션 넣기 (동기적으로 기다리게 해서 확인해보기)

//timeCheck {

queue.addOperations([imageLoad, filter], waitUntilFinished: true)

//}

  

  

imageLoad.outputImage

filter.outputImage

  

  

  

sleep(3)

PlaygroundPage.current.finishExecution()

  

  

  

  

  

  

  

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

  

//: [Next](@next)
~~~


## 작업 취소

오퍼레이션이 오퍼레이션큐에 들어가면, 오퍼레이션 큐가 관리하기 때문에 어떤것도 할 수 없지만 cancel() 메서드를 통해 취소는 가능하다.
하지만 오퍼레이션이 즉각적으로 동작을 멈추는것은 아니고 우선 isCancelled 변수만 true값으로 변환하는데 이를 이용해서 개발자가 직접 isCancelled가 true일 때 작업이 중지되고 isFinished를 true가 되도록 직접 세팅해야한다.

즉, cancel() 메서드 자체를 실행했을 때에는 중지되어야함을 알리는것 뿐이고,
이를 개발자가 직접 중지시키고 isFinished까지 true로 바꾸어야 실제로 중지된 상태가 된다.
또한 네트워크 작업의 경우에는 서버가 결과를 리턴할때까지 실제로 중지되지 않을 수 있음을 유의해야한다.

OperationQueue에 cancelAllOperations()라는 메서드를 실행하면 모든 오퍼레이션큐의 모든 오퍼레이션의 isCancelled를 true로 바꾼다. 이는 DispatchWorkItem에는 없는 가장 큰 차이점이다.


