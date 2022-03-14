# Delegate 와 Notification 방식의 차이점



## 1. Delegate Pattern

### 1-1. Delegate Pattern 사용 예시

> 이전에 학습한 UIImagePickerController의 사용 예시로 살펴보자

```
In delegation, an object handles a request by delegating to a second object (the delegate).
The delegate is a helper object, but with the original context. (중략)
```

- __`delegate object`__ 는 __`위임자, 대리자`__ 의 뜻에 맞게, 특정 객체로부터 외부 요청에 대한 처리를 __`대신 위임받아 수행하는 일종의 helper`__ 의 기능을 수행
- __`delegate pattern`__ 의 적용을 위해서는 크게 아래 3개의 요소를 필요로 함
  - 위탁자 객체 : delegator object로 표현
  - 위임자 객체 : 위에서 언급한 __`delegate object`__가 여기에 해당
  - 책임 위임 : swift의 경우 보통은 위임자에게 위탁자 정보를 전달하기 위한 __`delegate protocol`__ 의 형태로 구현
- 즉, 위탁자 위임자의 2개의 객체와 두 객체를 연결해주는 프로토콜이 필요한 것!



![image](https://user-images.githubusercontent.com/68586291/154850655-7b768528-916a-453c-bbe5-75cf5ae4033a.png)

```swift
class MyViewController: UIViewController, UIImagePickerControllerDelegate {
  
  override func viewDidLoad() {
    super.viewDidLoad()
    //UIImagePickerController의 delegate object로 현재 MyViewController을 지정
    imagePicker.delegate = self
    //사진앨범에서 이미지를 가져올 것임을 명시
    imagePicker.sourceType = .photoLibrary
  }
  
  //UIImagePickerControllerDelegate의 필요 메소드 구현
  func imagePickerController(_ picker: UIImagePickerController, 
                             didFinishPickingMediaWithInfo info: [UIImagePickerController.InfoKey : Any]){

    //라이브러리로부터 가져온 이미지(UIImage)
    let selectedImage = info[UIImagePickerController.InfoKey.originalImage] as? UIImage

    //뷰컨트로러의 imageView의 image로 가져온 이미지를 할당
    self.imageView.image = selectedImage

    //화면에 띄웠던 UIImagePickerController의 View를 해제
    picker.dismiss(animated: true, completion: nil)
  }
}
```

- __`UIImagePickerControllerDelegate`__ 가 __`delegate pattern`__ 이 적용된 대표적인 케이스
- __`imagePicker.delegate = self`__ 가 의미하는 바는 __`UIImagePickerController`__ 객체가 __사용자가 포토 라이브러리에서 선택한 사진을 처리하는 행위에 대한 책임을 `MyViewController`__ 에 넘긴다는 것을 의미
  - 즉 위임자 객체를 위의 뷰 컨트롤러로 정의한 것

   

### 1-2. Delegate Pattern을 사용하는 이유

```
Delegation is a way to make composition as powerful for reuse as inheritance [Lie86, JZ91]. 
```

- 결론부터 얘기하자면 __`객체간의 합성을 통한 코드의 재사용 효과`__ 를 극대화하기 위해 사용함
- __`객체 간의 합성`__ : 한 객체가 다른 객체의 참조 정보를 얻는 것을 의미함
  - 만일 한 객체가 다른 객체의 기능을 사용할 경우 두 객체를 합성하면 **동일한 기능을 중복으로 작성해도 되지 않아도 되는 이점**이 있음
- 일반적으로 객체 간의 합성을 실현한 대표적인 예가 __`상속`__ 인데, 이 경우 __자식 클래스가 부모 클래스의 구현 정보를 알고 있기 때문에 엄밀히 말해 캡슐화를 완벽히 충족했다고 보기 어려움__
- __또한, 자식 클래스가 부모 클래스에 종속적이므로 부모 클래스의 변경이 잦은 자식 클래스의 변경을 초래할 가능성도 높음__
- 여기서 __객체 간의 합성으로 인한 효과를 얻으면서, 캡슐화를 충족하기 위해 결과적으로 `delegate pattern` 을 적용__
  - 위탁자 객체가 특정 연산 처리에 대한 책임을 위임자 객체에 넘기는 것이 결국 부모 객체의 메소드를 자식 객체가 호출하는 것과 동일
  - 동시에 swift의 경우 프로토콜을 통해 연결하기 때문에, 프로토콜에 정의된 추상 메소드가 아닌 이상 위임자와 위탁자는 서로의 완전한 정보를 알지 못함
  - 추가적으로 위탁자와 위임자 객체는 인터페이스 혹은 프로토콜로 연결되어있지 __종속 관계가 아니므로 상속에 비해 두 객체 간의 결합도가 그만큼 낮음__
  - 결과적으로 __서로 간의 구현 상태를 모르는 상태에서 프로토콜을 통해 이벤터 처리의 요청 혹은 처리 결과를 전달함으로써 두 객체는 각자의 책임에만 온전히 집중하면 됨__

​    

## 2. Notification

<img src="https://i.imgur.com/CtMGiqY.png"/>

- __`NotificationCenter`__ 이라는 싱글톤 객체를 사용하여, 옵저버로 등록된 객체에게 __`Notification`__ 을 __`sender object`__ 가 특정 이벤트 발생 시에 이에 대한 처리를 알리는(__`post`__) 역할임

- delegate pattern과 비교했을 때 __`observer pattern`__ 을 적용한 __`NotificationCenter`__ 의 사용은 코드의 양이 적으면서 __여러 객체에 이벤트를 동시에 전달할 수 있는 이점이 있음__

  - delegate pattern의 경우에는 위탁자, 위임자 두 개의 객체가 위임 프로토콜을 통해 서로 정보를 주고 받았다면, 여기서는 제 3의 객체인 __`NotificationCenter`__ 을 통해 __`sender`__ 와 __`observer`__ 이 서로 정보를 주고받는 것

  - 특정 객체의 __`post`__ 를 여러 개의 객체에서 받고 싶으면,  아래와 같은 옵저버 등록을 __`Notification`__ 을 전달받아야 되는 다수의 객체 내에 명시해주면 되기 때문에 편리함

    ```swift
    //옵저버의 등록
    NotificationCenter.default.addObserver(self, selector: #selector(addingRectangleCompleted(_:)), name: Plane.NotificationName.rectangleAdded, object: self.plane)
    ```

  - 위와 같이 옵저버를 등록하고, __`selector`__ 에 일치하는 메소드를 내부에 정의하면, 해당 옵저버로 __`Notification`__ 을 post하는 순간 동일한 __`selector`__ 메소드가 구현된 객체가 이를 받는 것

  - 만일 이를 delegate 패턴에서 적용한다면, __`Notification`__ 을 post하지 않고 __`delegate method` 를 여러 번 호출해야 하는 번거로움이 있음__

  - 한 번의 호출로 여러 객체 내부의 메소드를 동시에 호출할 수 있는 편리함!

- 다만, 이러한 방식은 맨 처음 __`key-value`__ 형식으로 __`Notification`__ 이 전달하는 __`sender`__ 객체 정보나, 부가적인 객체 정보를 주고 받기 때문에 delegate와 비교했을 때 디버깅이 상대적으로 어렵다는 점에 유의해야 함

  - 쉽게 말해 delegate의 경우에는 코드량이 좀 많고 번거로워도 일단은 어떤 식으로 정보가 전달되는지 코드를 통해 메소드와 파라미터가 모두 보이기 때문에 흐름을 추적할 수 있지만, __`key-value`__ 와 옵저버 등록할 때 명시한 구독 정보만이 존재할 경우에는 상대적으로 정보의 흐름을 추적하는 것이 어려울 수 밖에 없는 것
  - 자바에서 DTO 객체 사용 vs HashMap 사용 으로 인한 편의 논쟁과 비슷한 플로우로 이해하자 