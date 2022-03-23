# KVO(Key-Value-Observing)

### 1. KVO의 개념

- key-value-observing의 약자로, 옵저버 패턴에서 특정 객체의 변경 내용을 다른 객체에게 알리기 위해 사용하는 패턴임
- 보통 model-view의 관계에서 model의 변화를 view에 알리는 데 사용됨
- swift에서는 __`NSObject`__ 를 상속받은 클래스에만 KVO를 적용할 수 있음
- Objective-C에서 사용되던 것을 Swift에서도 사용하기 때문에, 상호환을 위해 __`@objc dynamic var`__ 과 같은 키워드를 선언해줌
- 예제 코드

```swift
import Foundation

//옵저버가 변경을 감지할 대상 클래스를 정의
//반드시 NSObject를 상속받아야 함
class ExampleClass: NSObject{
    //dynamic modifier을 반드시 추가해야 함
    @objc dynamic var name: String = ""
    
    init(_ name: String){
        self.name = name
    }
}

//변경을 감지할 옵저버 클래스 정의
class Observer: NSObject{
  	//NSKeyValueObserving 프로토콜 타입으로 편하게 구현
    var observer: NSKeyValueObservation?
    var target: ExampleClass?
    
    init(_ target: ExampleClass){
        self.target = target
      	//옵저버 대상 등록
      	//맨 첫 인자인 key는 변경 대상 내부에 반드시 @objc dynamic var로 선언이 되있어야 함
        observer = target.observe(\.name, options: [.old,.new]){(object, change) in
            guard let old = change.oldValue else { return }
            guard let new = change.newValue else { return }
            print("이름 변경 감지\nold value : \(old) \nnew value : \(new)")
        }
    }
    
    func updateName(_ name: String){
        self.target?.name = name
    }
  
  	deinit{
      observer?.invalidate()
    }
}

//옵저버 인스턴스 생성 후, 내부에 감지할 타겟 인스턴스 주입
let observer = Observer(ExampleClass("일구"))
//이후 속성에 변경이 있을 때마다 옵저버 내부의 로직이 호출되는 것을 확인할 수 있음
observer.updateName("이구")
observer.updateName("삼구")
```

​    

### 2. KVO의 장/단점

#### 2-1. 장점

- 두 객체 간 동기화를 달성하기 위해서 제일 간단하게 적용해볼 수 있는 방법임

- 당연히 옵저버 패턴의 장점인 1:Many 관계의 커뮤니케이션이 가능하며, 특정 변화가 발생했을 때 여러 옵저버가 한 번에 이를 감지할 수 있음
- 수신 측에서 수신 대상이 될 객체와, 변화 감지 후의 로직을 정의하면 되고 발신 측은 여기에 관여하는 것이 없음
  - 다시 말해 수신측, 발신측 객체간의 결합도가 그만큼 낮다는 이점이 있는 것
  - 수신측은 발신측에서 제대로된 키워드에 대응되는 속성 이름만 알면 됨
  - 굳이.. 발신측에서 신경쓸 부분은 __`@objc dynamic`__ 같은 키워드를 까먹지 않고 명시하고, 수신측에서 __`observe`__ 구현 시 사용할 키워드에 대응되는 속성이 반드시 존재해야 하는 것. 

​     

#### 2-2. 단점

- 옵저버 패턴과 델리게이트 패턴과의 차이에서 알 수 있듯, 커뮤니케이션 과정을 간단하게 정의할 수 있지만... 동시에 디버깅이 필요한 경우에는 그만큼 상황이 힘들어질 수 있음(...)
  - 예를 들어 하나의 여러 옵저버가 하나의 관찰 대상을 바라보고 있는 경우에, 어느 한 옵저버에서 문제가 발생하면 일단 관찰 대상에서 직접적으로 옵저버 내부의 로직을 호출하는 것이 드러나지 않기 때문에 이를 추적하는 과정이 좀 까다로워짐
  - 델리게이트의 경우라면, 일일히 델리게이트 메소드를 호출하기 때문에 번거로워드 해당 메소드의 호출을 따라가면서 추적하면 되기 때문에 위의 상황에 비하면 좀 더 나은 것
-  앞서 말했듯 KVO를 적용하기 위해서는 objective-c 런타임을 사용하게 되는데, swift 런타임과 달리 이는 무조건 동적 디스패치를 적용하므로 성능상의 차이가 발생할 수 있음
- 메모리에서 해제될 경우에는 옵저버도 같이 지워줘야 함 -> 안그러면 충돌일어날 수 있으므로 조심

​    

### 3. 추가

#### dynamic 키워드의 의미?

> @objc는 들어봤어도 dynamic은 여기서 처음봐서 좀 찾아봄

- @objc와 마찬가지로 objective-c와의 상호운용을 위해 사용되는 키워드
- 앞서 학습한 __`dynamic dispatch`__ 는 런타임 시점에 호출해야 할 특정 메소드의 구현을 결정하는 것을 의미함
  -  예를 들면 자식 클래스가 부모 클래스의 메소드를 오버라이딩된 형태가 있다고 가정해보면...
  -  동적인 디스패치가 적용되면 동일한 이름의 메소드를, 부모 메소드와 오버라이딩 된 자식 메소드 중 어떤 것을 호출해야 할 지 런타임 시점에 결정하는 것
- objective-c의 경우 동적인 디스패치를 사용하지만, swift의 경우에는 여러 최적화 대안을 두고 별다른 선택지가 없을 경우에 동적인 디스패치를 선택 ([출처](https://zeddios.tistory.com/296))
  - objective-c의 동적 디스패치의 경우 특정 클래스 내의 메소드나 프로퍼티에 접근할 때, 객체에 메시지를 보내는 방식으로 구현되있으며, 메시지를 받은 객체는 접근 대상이 있는 곳으로 가서 메소드를 호출(다시 말하지만, 런타임 시점에 일어남~~~)
  - 결국 뭐가 되었던 간에 동적 디스패치는 런타임 시점에 발생하기 때문에, 성능상 항상 좋은 대안이라고 볼 수 없으므로 swift의 경우에는 가능하면 컴파일 시점에 메소드의 구현 혹은 호출의 구체화를 파악할 수 있으면 정적 디스패치로 감
  - Like many other languages, Swift allows a class to override methods and properties declared in its superclasses. This means that the program has to determine at runtime which method or property is being referred to and then perform an indirect call or indirect access. This technique, called *dynamic dispatch*, **increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage.**
  - [애플 개발자 블로그](https://developer.apple.com/swift/blog/?id=27) 글의 일부를 발췌한 것인데, 여기서 __`increases language expressivity at the cost of a constant amount of runtime overhead for each indirect usage`__ 부분에서 근거를 찾으면 될듯?
- 그렇다면.... __`dynamic`__ 키워드가 __`objective-c와의 상호운용을 위해 사용된다`__ 라는 의미는, 결국 해당 키워드가 선언된 부분에 한해서는 swift 런타임이 아닌 objective-c 런타임 방식을 채택하겠다는 것을 의미함
- 따라서 정적 디스패치가 아닌, 해당 부분은 동적인 디스패치를 적용하겠다는 것