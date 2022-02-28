## Swift 클래스의 성능을 높일 수 있는 방법

> 2022.02.28

### 1.  고려해볼 수 있는 요인들

#### 🔎 Reference Counting

- __클래스의 인스턴스를 참조하는 횟수( `Reference Counting` )가 성능에 영향을 끼칠 수 있음__

- Reference Counting은 우리말로 __`참조계수`__ 혹은 __`참조 횟수 계산`__ 이라고 표현하며, Heap 영역에 존재하는 특정 객체의 참조 개수를 세는 것을 의미함

- Reference Counting은 스위프트의 ARC(Automatic Reference Counting)가 클래스 인스턴스들을 대상으로 관리하며, 참조 타입인 클래스의 인스턴스에 대해서만 적용됨 

  > ARC(Automatic Reference Counting)
  >
  > - 스위프트에서 자동으로 메모리를 관리해주는 방식으로,  더 이상 쓰이지 않는 클래스 인스턴스(객체)를 메모리에서 해제해주는 역할을 수행
  > - 자바의 Garbage Collecting과 다른 점은, 메모리 해제 시점을 런타임이 아닌 컴파일 시점에 결정

```swift
class Example{
  //특정 클래스
}

let a = Example() //Example타입 객체 생성(Reference Counting : 1)
let b = a // a 인스턴스를 b 인스턴스로 얕은 복사(Reference Counting : 2)
```

- 위와 같은 상황에서 특정 인스턴스를 생성하거나, 다른 인스턴스로 참조를 복사하게 되면 Heap 영역의 해당 객체에 대한 Reference Couting이 1 증가하게 됨
  - 클래스가 인스턴스를 생성하면 ARC는 Reference Counting을 비롯한 인스턴스에 대한 정보를 저장하기 위한 메모리 공간을 별도로 할당
  - 만일 다른 변수가 생성된 인스턴스를 참조하는 얕은 복사가 이루어지면, 참조 횟수가 늘어난 것이므로 Reference Counting이 1 증가하는 것
- 인스턴스의 사용이 끝나면 Reference Counting이 1 감소하게 되며 최종적으로 Reference Counting 값이 0이 되면 해당 객체가 차지하던 메모리 영역을 해제하는 것 
- 이런 식의 Reference Counting은 참조 횟수를 계산하는 과정 자체가 오버헤드이기 때문에 이것이 일어나는 횟수를 가능하면 줄이는 것이 좋음

​    

#### 🔎 Allocation

- __인스턴스가 생성될 때 `Stack` 과 `Heap` 영역 중 어느 곳에 할당되는 지 여부가 성능에 영향을 끼칠 수 있음__
- __`Stack`__ 은 후입선출 방식으로 데이터의 삽입 및 제거가 한 곳에서만 일어나는 단순한 구조이기 때문에 이에 대한 시간 복잡도가 O(1)로 비교적 빠름
- 반면, __`Heap`__ 은 메모리 공간을 할당 및 해제할 때마다 전체 공간에서 적절한 공간을 찾는 행위를 수반하기 때문에 상대적으로 느림
- 결과적으로 메모리 할당이 __Stack에서 이루어지는 것이 Heap에 이루어지는 것 보다 더 효율적인 것__

​     

#### 🔎 Method Distpatch

- __`Method Dispatch`__ __의 정적/동적 여부가 클래스의 성능에 영향을 끼칠 수 있음__

- __`Method Dispatch`__ 는 어떤 메소드를 호출할 것인지 결정하고, 해당 메소드를 호출하는 과정을 의미하며 이것이 결정되는 시점이 컴파일 시점이면 __`Static Method Dispatch`__ 가 되고, 런타임 시점이면 __`Dynamic Method Dispatch`__ 라고 부르는 것
- 보통 컴파일러가 컴파일 할 때 특정 메소드가 호출되는 명령의 위치를 파악할 수 있으면 런타임 시점에 위치를 찾을 필요 없이 바로 실행할 수 있고, 위치 판단이 불가능하면 런타임 시점에 테이블을 참고해서 호출할 메소드를 결정하는 과정을 수반하게 됨
  - Swift에서는 보통 클래스마다 __`Virtual Dispatch Table`__ 이라는 함수 포인터 배열로 표현된 테이블을 유지하는데, 런타임 시점에 하위 클래스가 메소드를 호출할 때마다 테이블을 참조하여 실제 호출할 함수를 결정하는 것
- __결국 부수적인 일을 하지 않아도 되는 정적인 방식이 동적인 것보다 더 빠름__

​    

### 2. 성능 향상 방법

#### 💡 값 타입 사용을 통해 Reference Counting으로 인한 오버헤드를 최대한 줄이자

- 어떤 클래스 인스턴스에 대한 참조가 매우 많으면 그만큼 Reference Counting 계산으로 인한 오버헤드도 많아질 수 밖에 없을 것
- 또한 값타입인 구조체를 사용했다고 하더라도, 구조체 내부의 프로퍼티가 참조 타입이라면 이로 인한 Reference Counting 계산이 발생하게 됨
  - *지난 번에 언급한 Struct도 사실상 참조타입인 경우가 있다 ... 라는 것과 연관있는 부분이 아닐까 싶음*

- 따라서 Reference Counting으로 인한 오버헤드를 줄이기 위해서 __구조체(Struct)와 같은 값 타입의 사용을 최대한 하는 것이 나음__

​    

#### 💡 구조체 사용을 통해 Heap 영역에의 할당을 최대한 줄이자

- 앞서 언급한대로 클래스는 기본적으로 참조 타입이기 때문에 Heap 영역에 할당되며 이는 Stack에 할당되는 것 보다 더 많은 비용을 수반함

- 결국 위와 마찬가지로 __구조체로 대체할 수 있는 경우라면 최대한 구조체로 대체하는 것이 나을 것__

- [예시 1](https://corykim0829.github.io/swift/Understanding-Swift-Performance/#) Key:Value 형식의 Dictionary를 사용할 때 Key에 해당하는 타입이 클래스 혹은 Heap에의 할당이 발생하는 타입일 경우 이를 구조체로 대체할 것을 이야기하고 있음

  - 만일 Key가 String이라면 String은 내부적으로 Character 타입들을 Heap에 할당하기 때문에 이로 인한 비용이 발생함
  - 여기서 Key를 String이 아닌 완전한 구조체로 대체할 것을 권장하는 것

  ```swift
  //Before
  let dic = [String:Int]() //key가 String
  
  //After
  struct Key: Hashable{
    var keyValue1
    var keyValue2
    //...
  }
  let dic = [Key:Int]() //key가 구조체 타입
  ```

  

#### 💡Dynamic Dispatch가 필요 없는 경우에는 Static Dispatch로 처리하자

- Dynamic Dispatch가 비효율적인 이유는 보통의 경우 클래스가 Virtual Table에서 메소드 정보를 참고해야 하는 행위를 수반하기 때문임

  ```swift
  //부모 클래스
  class Building{
    //부모 클래스 메소드
    func bldgFunc(){}
  }
  
  //자식 클래스
  class Palace: Building{
    //부모 클래스 내 메소드 오버라이딩
    override func bldgFunc(){}
  }
  
  let palace = Palace()
  palace.bldgFunc()
  ```

- 위와 같은 다형성이 적용된 상황에서 __`palace.bldgFunc()`__ 을 호출하면 해당 메소드가 부모 클래스의 메소드인지, 상속받은 자식 클래스의 메소드인 지 컴파일 시점에는 알 수가 없음

  - 따라서 위에서 설명한대로 컴파일러는 클래스 내 메소에 대한 포인터가 저장된 Virtual Table을 만든 후, 적절한 런타임 시점에 이 테이블을 참고해서 메소드를 호출해야 하는 것

- 그렇다면 위와 같이 상속 혹은 다형성 적용이 필요 없는 경우에는 __컴파일러로 하여금 Static Dispatch로 처리하도록 유도할 수 있을 것__

- __`final`  키워드를 사용하여 클래스를 선언하게 되면, 해당 클래스는 상속을 통한 하위 클래스를 생성할 필요가 없음을 선언하게 되므로 해당 클래스의 메소드에 대해서는 컴파일러가 Static Dispatch를 하는 것이 가능해짐__

- 혹은 오버라이딩이나 하위 클래스에서의 호출이 필요없는 메소드에 대해 __`private` 을 선언하여 마찬가지로 상속이 불가능하게 함으로써 Static Dispatch가 가능하도록 할 수도 있음__

​    

### Reference

- 야곰 책

- [Automatic Reference Counting](https://minsone.github.io/mac/ios/swift-automatic-reference-counting-summary)
- [[Swift] 스위프트 성능 이해하기 (1) - struct와 class의 성능 차이](https://corykim0829.github.io/swift/Understanding-Swift-Performance/#)

- [[번역]Method Dispatch in Swift](https://sihyungyou.github.io/iOS-method-dispatch/)