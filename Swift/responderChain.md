# Responder Chain과 First Responder의 역할

[Using Responders and the Responder Chain to Handle Events](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/using_responders_and_the_responder_chain_to_handle_events)

​    

## 1. Responder Chain의 개념

- iOS에서 어플리케이션은 이벤트를 __`responder objects`__ 를 통해 처리함

- __`responder object`__ 는 보통 __`UIResponder`__ 의 구체적인 인스턴스로 구현되며, __`UIResponder`__ 의 대표적인 서브클래스로 __`UIView`__ , __`UIViewController`__ 등이 존재

- 특정 이벤트가 발생하면 이에 대한 데이터를 __`responder obejct`__ 가 받게 되는데, 일단 데이터를 받으면 2가지 선택지가 있음

  1. 직접 이벤트를 처리
  2. 이벤트를 다른 __`responder object`__ 로 전달

-  최초로 이벤트가 발생했을 때, 보통의 경우 __`UIKit`__ 이 자동으로 이벤트를 제일 적절한 __`responder object`__ 로 넘겨주는데 여기서 __`자동으로 이벤트를 넘겨받는 적절한 responder object`__ 가 바로 __`first responder`__ 임

- 만일 이벤트를 직접 처리하지 않고 넘기는 경우라면(Unhandled Events), __어느 한 `responder object` 에서 다른 `responder object` 로 데이터를 넘겨야 되는데, 이러한 구조를 가리켜 `responder chain`__ 이라고 부름

  - __`responder chain`__ 이 존재함으로써 처리되지 못한 이벤트를 자동으로 다음으로 넘겨 처리하거나, 최종적으로 무시될 수 있는 것!
  - __`UIResponder`__ 의 내부 속성을 보면 __`next`__ 라는 옵셔널 타입이 존재하는 것을 볼 수 있는데, 이것이 __`responder chain`__ 상에서 이벤트를 넘겨받을 다음 __`responder object`__ 가 되는 것이며, 만일 nil일 경우에는 넘겨받을 객체가 없는 것을 의미함

  ```swift
  var next: UIResponder? // 다음 객체
  var isFirstResponder: Bool //first responder 여부
  var canBecomeFirstResponder: Bool //first responder 가능 여부
  var canResignFirstResponder: Bool //first responder 포기 여부
  
  func resignFirstResponder()-> Bool //UIKit에 해당 객체를 first responder로 여기도록 요청
  func becomeFirstResponder()-> Bool //해당 객체에 윈도우에서 first responder의 역할을 하지 않게 됨을 알림
  ```

- __`responder object`__ 의 __`next`__ 는 변경 가능한 속성인데, 이는 곧 __`next`__ 를 오버라이딩해서 개발자가 원하는 방식으로 바꿈으로써 __`responder chain`__ 이 변경 가능함을 의미함

  (이미 구현된 많은 클래스가 해당 속성을 오버라이딩하고 있음)

​    ![img](https://blog.kakaocdn.net/dn/Nf37T/btq9reobFgd/U5reaCRGKnibsje1iHD280/img.png)

- 위의 그림에서 __`UITextField`__ 가 이벤트를 처리하지 않는다면, __`UIKit`__ 은 해당 요소의 부모클래스가 되는 __`UIView`__ 로 이벤트를 우선 전달하고, 루트가 되는 __`UIView`__ 를 소유하는 __`UIViewController`__ 를 거쳐 __`UIWindow`__ , __`UIApplication`__ , __`UIApplicationDelegate`__ 순서로 각 요소가 이벤트를 처리하지 않을 경우 다음 요소로 이벤트를 전달함
- 결국 어느 한 요소에서 처리되지 않으면 상위로 넘기는 과정을 반복하는 것!

​    

## 2. First Responder과 이벤트를 전달받을 Responder의 결정

> UIKit이 어떤 기준으로 최초로 이벤트를 전달받을 적절한 responder object를 결정하는 지의 기준에 대해 알아본다.

### First Responder의 결정 기준

- 보통의 경우 어떤 이벤트가 들어왔는 지에 따라 __`first responder`__ 의 결정 기준이 달라짐
  - touch event : 터치가 발생한 뷰
  - press event : 포커싱된 객체
  - shake-motion event : 개발자 혹은 __`UIKit`__ 이 지정한 객체 
  - remote-control event : 개발자 혹은 __`UIKit`__ 이 지정한 객체
  - editing menu message : 개발자 혹은 __`UIKit`__ 이 지정한 객체
- 여기서의 이벤트는 __`UIEvent`__ 의 __`type`__ 속성을 말하며, 이는 __`UIEvent.EventType`__ 이라는 열거형 값으로 각 케이스가 바로 위에서 언급하고 있는 각 이벤트의 종류에 해당됨

### Touch Event에서의 First Responder 결정

- 터치 이벤트의 경우 __`UIKit`__ 은 이것이 발생한 뷰를 찾기 위해 __`hit-test`__ 를 수행하는데, 이는 특정 터치를 포함하는 뷰 계층을 찾아서 해당 계층의 제일 깊은 서브 뷰를 찾고 이를 __`first responder`__ 로 지정하는 과정을 의미함

```swift
func hitTest(_ point: CGPoint, 
        with event: UIEvent?) -> UIView?
```

- 위의 메소드에서 __`point`__ 는 이벤트를 받는 쪽의 좌표 바운드를 의미하고, __`event`__ 는 발생한 이벤트를 의미하지만 만일 이벤트 발생 범위 바깥에서 이를 호출하고자 할 경우에는 nil로 둠

- __`hitTest`__ 는 뷰 계층에서 현재 뷰의 각 서브 뷰의  __`point`__ 메소드 호출을 통해 어떤 서브 뷰가 터치 이벤트를 다음으로 받을 지를 결정하며, 아래와 같은 서브 뷰는 무시됨

  - disabled 상태가 된 경우

  - 투명도가 0.01 미만인 경우

```swift
func point(inside point: CGPoint, 
      with event: UIEvent?) -> Bool
```

​    

​    

## 추가 정리 거리

> 맥락상 보충

- Controls communicate directly with their associated target object using action messages. When the user interacts with a control, the control sends an action message to its target object. Action messages are not events, but they may still take advantage of the responder chain. When the target object of a control is `nil`, UIKit starts from the target object and traverses the responder chain until it finds an object that implements the appropriate action method. For example, the UIKit editing menu uses this behavior to search for responder objects that implement methods with names like [`cut(_:)`](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354193-cut), [`copy(_:)`](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354191-copy), or [`paste(_:)`](https://developer.apple.com/documentation/uikit/uiresponderstandardeditactions/2354189-paste).

- Gesture recognizers receive touch and press events before their view does. If a view's gesture recognizers fail to recognize a sequence of touches, UIKit sends the touches to the view. If the view does not handle the touches, UIKit passes them up the responder chain. For more information about using gesture recognizer’s to handle events, see [Handling UIKit Gestures](https://developer.apple.com/documentation/uikit/touches_presses_and_gestures/handling_uikit_gestures).

- When a touch occurs, UIKit creates a [`UITouch`](https://developer.apple.com/documentation/uikit/uitouch) object and associates it with a view. As the touch location or other parameters change, UIKit updates the same `UITouch` object with the new information. The only property that does not change is the view. (Even when the touch location moves outside the original view, the value in the touch’s [`view`](https://developer.apple.com/documentation/uikit/uitouch/1618109-view) property does not change.) When the touch ends, UIKit releases the `UITouch` object.