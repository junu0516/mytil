### DI(Dependency Injection)

- __의존성__ : 특정 클래스가 제 기능을 다하기 위해, 다른 클래스의 생성을 필요로하는 경우 클래스 간 의존성이 존재한다고 표현
  - 아래의 코드에서 Car 클래스는 Tire 클래스를 필요로 하며, 따라서 Car 클래스는 Tire 클래스의 의존성을 가진다고 할 수 있음
  - 보통 의존 관계는 new 키워드의 선언과 밀접한 관련

```java
class Car{
    Tire tire;
    
    public Car(){
        this.tire = new Tire();
    }
}
```

- 의존성 주입은 객체를 직접생성하지 않고, 외부에서 생성 후 주입시키는 방식을 말함
- 모듈 간 결합이 낮아지기 때문에 유연성이 높아짐
  - 위와 같은 코드에서는 Car 클래스와 Tire 클래스 간의 결합도가 높기 때문에, 하나를 수정하면 다른 하나를 수정해야 하는 번거로움이 존재함
  - 따라서, 결합도를 낮출수록(=의존성 혹은 종속성을 줄일수록) 코드의 재활용성 및 유연한 코드 작성이 가능해지는 것
- 의존성 주입하기
  - 생성자나 setter 메소드를 사용하여 의존성을 주입할 수 있음
  - 위와 같이 new를 통해 직접 생성하지 않고, 외부에서 생성한 객체를 가져오는 것

```java
class Car{
    Tire tire;
    
    public Car(Tire tire){
        this.tire = tire;
    }
    
    public void setTire(Tire tire){
        this.tire = tire;
    }
}
```

- 스프링 프레임워크에서는 의존성 객체를 Bean이라고 하며, 처음 프로젝트가 실행될 때 Bean을 생성하여 Bean 컨테이너에서 관리하게 되고, 이후 필요시 컨테이너로부터 Bean을 주입받아 활용
  - Bean 객체의 생성 및 소멸을 개발자가 아닌 프레임워크가 주도하게 되는 것
  - Singleton Pattern의 특징이 나타남



### IoC(Inversion of Conrol)

- 제어의 역전 : 메소드나 객체의 호출을 개발자가 결정하지 않고, 프레임워크가 결정하는 것

- 스프링에서의 Bean(의존성 객체)은 위에서 언급했듯이 개발자가 아닌, 프레임워크가 생성 및 소멸에 관여하며 개발자는 컨테이너에서 프레임워크가 만든 객체를 호출하여 사용

- 스프링에서 Bean이 만들어지고 실행되는 과정은 아래와 같음

  1. 최초 객체가 생성된다.

  2. 의존성 객체가 주입된다.
     - 필드 인스턴스나 setter 혹은 생성자 등을 통해 주입
     - `@Autowired`  선언하여 프레임워크가 컨테이너에서, 선언된 객체 타입과 일치하는 Bean을 찾아 주입함

  3. 의존성 객체 메소드가 호출된다.

- 보통 의존성 객체를 주입할 때 스프링에서 권장하는 방식은 __생성자방식__임

  - 참고 : https://reflectoring.io/constructor-injection/
  - 외부 접근이 용이하며, 테스트 코드 작성이 수월함
  - __무엇보다 순환참조로 인한 에러를 사전에 방지할 수 있음__
    - 아래와 같이 필드 주입 방식을 통해 두 개의 Bean이 서로 의존하는 상황에서, 하나의 Bean이 내무의 call() 메소드를 호출할 경우에는  CallStack이 지속적으로 쌓이면서 ``StackOverflowError``가 발생
    - Calling Cat -> Calling Dog -> Calling Cat -> Calling Dog이 무한으로 반복되는 상황 ...
    - 문제는 이러한 예외는 컴파일 에러가 아닌 런타임 에러이기 때문에, 메소드가 실제로 호출되기 전까지는 순환참조된 부분을 찾아내기 힘들 수 있음

```java
@Component
public class Dog{
    @Autowired
    private Cat cag;
    
    public void call(Car cat){
        System.out.println("Calling Cat");
        cat.call();
    }
}

@Component 
public class Cat{
    @Autowired
    private Dog dog;
    
    public void call(Dog dog){
        System.out.println("Calling Dog");
        dog.call();
    }
}
```



- 만일, 생성자 주입 방식을 활용할 경우에는 이러한 순환참조 오류를 컴파일 시점에서 잡아낼 수 있기 때문에 권장되는 것
  - 생성자 주입은 보통 __최초 생성자 호출시 1회 호출되는 것이 보장__되기 때문에, 순환참조가 일어날 경우 ``BeanCurrentlyInCreationException`` 이 발생하여 사전에 문제를 방지할 수 있음

```java
@Component
public class Dog{
    
    private Cat cag;
    
    @Autowired
    public Dog(Car cat){
        this.cat = cat;
    }
    
    public void call(Car cat){
        System.out.println("Calling Cat");
        cat.call();
    }
}

@Component 
public class Cat{
    
    private Dog dog;
    
    @Autowired
    public Cat(Dog dog){
        this.dog = dog;
    }
    
    public void call(Dog dog){
        System.out.println("Calling Dog");
        dog.call();
    }
}
```



- 보통 단일 생성자만 존재하면 ``@Autowired``를 사용하지 않아도 되지만, 2개 이상인 경우에는 반드시 어노테이션을 선언할 것!