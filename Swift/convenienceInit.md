# Swift의 Convenience Init

> 2022.03.08

#### Key Point

```
- 스위프트 클래스 상속에서의 생성자 규칙은?
- 부모 클래스의 생성자와 자식 클래스의 생성자는 구분되는가? 구분된다면 왜?
- 편의 생성자란 무엇이며 왜 사용하는지?
```

​    

## 0. 클래스 상속과 생성자

```swift
//부모 클래스
class Building{
  var name: String
  
  //부모 클래스의 생성자
  init(name: String){
    self.name = name
  }
}

//자식 클래스
class Apartment: Building{
  
  //자식 클래스의 생성자 재정의
  override init(name: String){
    //부모 클래스 생성자 호출
    super.init(name: name)
  }
}
```

- 값 타입인 구조체의 경우에는 상속을 할 수 없기 때문에, 결과적으로 __생성자를 위임__ 한다는 개념 자체가 없음
- 하지만, 참조타입인 클래스는 상속이 가능하기 때문에, __부모 클래스의 생성자와 자식 클래스의 생성자를 구분해야 할 필요가 있음__
  - 기본적으로 스위프트는 __자식 클래스가 부모 클래스의 생성자를 상속받지 않음__
  - 이는 __부모 클래스의 생성자가 자식 클래스에도 최적화되있다는 보장이 없기 때문에__ , 결과적으로 부모 클래스의 생성자를 잘못 사용해서 __자식 클래스를 완전히 정확하게 초기화하지 못하는 상황을 방지__ 하고자 하기 때문
- 부모 클래스의 생성자를 자식 클래스에서도 사용하고 싶으면 __자식 클래스에서 부모 클래스의 생성자를 똑같이 구현해야 함__
  - __동일한 지정 생성자라면 이를 `override` 하면 될 것__



## 1. 편의 생성자의 개념

- 클래스의 경우에는 구조체나 열거형과 달리 __`지정 생성자`__ 와 __`편의 생성자`__ 의 두 가지 종료의 생성자를 가짐

​    

### 1-1. 지정 생성자(Designated Initializer)

```swift
init(parameters){
  statements
}
```

- 필요에 따라 부모 클래스의 생성자를 호출하고, 생성자가 클래스 내에서 __정의된 모든 속성을 초기화해야 하는 책임을 가진 생성자__

  *Designated Initializers are the primary initializers for a class. A designated initializer fully initializes all properties introduced by that class and calls an 		appropriate superclass initializer to continue the initialization process up the superclass chain.*

- 클래스의 생성자 중 제일 기본적인 역할을 하므로 보통 하나 이상 정의함
- 만일 자식 클래스에서 지정 생성자가 따로 없는 경우라면 이는 __부모 클래스에서 상속받은 속성 혹은 옵셔널 속성 외에 별다른 속성이 없을 가능성이 큼__

​    

### 1-2. 편의 생성자(Convenience Initializer)

```swift
init(parameters){
  statements
}
```

- 클래스의 생성자를 보조하는 일종의 부수적인 생성자로, __지정 생성자를 내부적으로 호출하는 필수적이지 않은 생성자__

  *Convenience Intializers are secondary, supporting initializers for a class. You can define a convenience initializer to call a designated initializer from the same class as the convenience initializer with some of the designated initializer's parameters set to default values.*

- 지정 생성자와 달리 __모든 속성을 초기화해야 할 의무가 없음__

- 특정 목적으로 인스턴스를 생성하고자 할 때, 일부 속성에 한해서는 개발자가 __의도한 방식으로 초기값을 지정해야 될 필요가 있는데,__ 이런 상황에서 편의 생성자 사용을 통해 초기값이 __고정되야 하는 속성에 한해 항상 같은 값으로 편하게 초기화할 수 있음__

```swift
class Person{
  var name: String
  var age: Int
  
  init(name: String, age: Int){
    self.name = name
    self.age = age
  }
}

class Student: Person{
  var major: String
  
  //지정 생성자
  init(name: String, age: Int, major: String){
    self.major = "Swift"
    super.init(name: name, age: age)
  }
  //편의 생성자
  convenience init(name: String){
    self.init(name: name, age: 7, major: "")
  }
}
```

- 위의 예시를 보면 __`Student`__ 클래스 내부의 __지정 생성자는 파라미터로 부모 클래스의 속성을 포함하여 모든 속성을 초기화 하기 위한 값을 필수적으로 받고 있음__

- 하지만 편의 생성자는 __파라미터로 반드시 모든 속성을 초기화하기 위한 값을 받지 않아도 됨__

  - 단, 편의 생성자 내부를 보면 __`self.init(name: ,age:, major:)`__ 과 같이 __지정 생성자를 내부적으로 호출하는 것을 확인할 수 있음__
  - __`self.init(name: name, age: 7, major: "")`__ 은 결과적으로 __`name`__ 속성을 제외한 나머지 속성에 대해서는 같은 값으로 고정하겠다는 의미

- 만일 위의 예시에서 편의 생성자가 없다면, 매번 아래와 같이 고정값을 일일히 하드코딩으로 넘겨주면서 지정생성자로 인스턴스를 초기화해야 할 것임

  ```swift
  //편의 생성자가 없는 상황에서 내부적으로 age는 7로, major은 빈 문자열 ""로 모두 고정하고 싶은 상황
  var student1: Student = Student(name: "Joe Biden", age: 7, major: "")
  var student2: Student = Student(name: "Vladimir Putin", age: 7, major: "")
  var student3: Student = Student(name: "Xi Jinping", age: 7, major: "")
  ```

- 하지만 편의 생성자를 지정해두면 위의 예시에서 결과적으로 파라미터를 __`name`__ 만 넘기면 되기 때문에 좀 더 편리한 것!

  - 어차피 편의 생성자 내부에서 지정 생성자를 고정값을 가지고 호출하는 부분이 미리 정의되있기 때문!

  ```swift
  var student1: Student = Student(name: "Joe Biden")
  var student2: Student = Student(name: "Vladimir Putin")
  var student3: Student = Student(name: "Xi Jinping")
  ```

​        

## 2. 초기화 위임

<img src="https://media.vlpt.us/images/gooreum_90/post/bd925115-64b8-4020-8dd1-991fbc39f50a/%E1%84%8F%E1%85%B3%E1%86%AF%E1%84%85%E1%85%A2%E1%84%89%E1%85%B3%E1%84%8B%E1%85%B4%20%E1%84%8E%E1%85%A9%E1%84%80%E1%85%B5%E1%84%92%E1%85%AA%20%E1%84%8B%E1%85%B1%E1%84%8B%E1%85%B5%E1%86%B7.png" alt="image" style="width:50%; align: center !important;"/>

- 지정 생성자와 편의 생성자 간의 관계는 결과적으로 아래와 같이 정리 가능함
  - 자식 클래스의 지정 생성자는 부모 클래스의 지정 생성자를 반드시 호출해야 함
  - 편의 생성자는 자기 자신이 정의된 클래스의 다른 생성자를 반드시 호출해야 함
  - 편의 생성자는 따라서 궁극적으로 지정 생성자를 반드시 호출해야 함