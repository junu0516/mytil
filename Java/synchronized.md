# synchronized 키워드의 역할

![Synchronization in java and thread synchronization in java - JavaGoal](https://javagoal.com/wp-content/uploads/2020/09/31.png)

[공식문서 설명](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html)

### synchronized

- 자바 코드에서 동기화가 필요한 영역은 모두 __`synchronized`__ 로 표시됨

  > __컴퓨터에서의 동기화__
  >
  > 프로세스 혹은 스레드가 수행되는 시점을 조절하거나, 순서를 명확히 하는 과정

- 자바에서의 synchronized는 __객체에 대한 동기화__ 로 이루어지며, 이는 동기화된 블록에는 __한 시점에는 하나의 스레드만이 접근 및 실행__ 되도록 조정함
- __자바에서의 멀티 스레드__ 와 유의점
  - 자바 프로그램은 기본적으로 __`메인 스레드`__ 가 __`main()`__ 메소드를 실행시키면서 시작되며, 마지막 코드를 실행하거나 __`return`__ 을 만나면 종료됨
  - __`JVM`__ 이 생성한 메인 스레드는, 별도의 작업 스레드를 만들어 이를 병렬로 실행시킬 수 있음
  - 다시말해, 멀티 스레드를 생성할 수 있음을 의미하며, __멀티스레딩에서는 실행 중인 스레드가 하나라도 존재하면 프로세스가 종료되지 않음__
  - 하지만 여러 스레드가 객체를 공유할 경우, 동기화가 제대로 이루어지지 않으면 상태가 예측한 것과 다르게 변경될 수 있음
  - 따라서 __`임계 영역(Critical Section)`__ 을 지정하기 위해 __`synchronized`__ 키워드를 사용하는 것
- synchronized 키워드가 쓰이는 블록(=임계영역의 구분 방식)은 크게 4가지 유형으로 나뉨



- __인스턴스 메소드의 동기화__ (동기화 메소드의 선언)

  - 메소드 선언시 접근 제어자와 함께 synchronized를 선언

    ```java
    public synchronized void increment(int number){
        System.out.println(++number);
    }
    ```

  - 메소드 전체 내용이 임계 영역이 되며, 동기화는 클래스 내부의 동기화된 인스턴스를 기준으로 이루어짐

    - 다시 말해, 하나의 스레드만이 동기화된 인스턴스 메소드에 접근 가능하며, 둘 이상의 인스턴스가 있으면 한 시점에 한 인스턴스에 한 스레드만이 접근하게 됨



- __스태틱 메소드(Static Method)의 동기화__

  - 인스턴스 메소드의 동기화와 비슷한 방식

    ```java
    public static synchronized void increment(int number){
        System.out.println(++number);
    }
    ```

  - 

