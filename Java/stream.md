# 람다식과 Java Stream

> Java는 기본적으로 Object-Oriented Programming이기 때문에 프로그램을 객체가 아닌 순수 함수 단위로 나누는 Functional Programming과는 거리가 있지만, 8버전 이후로는 람다식과 Stream API를 통해 Functional Programming의 적용이 일부 가능해졌다.
>
> 
>
> 이번에는 Stream API와 람다식의 적용에 대해 알아보도록 한다.

.

[참고 블로그](https://mangkyu.tistory.com/114)

[Java stream 공식문서](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Stream.html)

.

### 람다(Lambda) 사용법

- 람다 함수는 익명 함수(Anonymous Function)를 지칭하는 용어로, 쉽게 말해 따로 메소드를 만들 필요 없이 코드 한줄로 함수를 만들어 호출하는 것

- __(매개변수)->{실행문}__ 의 구조로 표현하며, 매개변수를 이용해 중괄호 안에 명시된 코드를 실행하는 것

  - 아래의 예시처럼 좀 더 간결한 코드 작성이 가능해짐

  ```java
  //람다식 미적용
  new Thread(new Runnable(){
      @Override
      public void run(){
          System.out.pritnln("Thread executed without lambda");
      }
  }).start();
  
  //람다식 적용
  new Thread(()->{
      System.out.println("Thread executed with lambda");
  }).start();
  ```

  .

- __함수형 인터페이스(Functional Interface)__

  - 인터페이스의 추상 메소드를 람다식의 중괄호 안에 오버라이딩하여 구현할 수 있음
  - __`@FunctionalInterface`__ 어노테이션을 통해 명시적으로 함수형 인터페이스임을 선언할 수 있음

  ```java
  @FunctionalInteface
  interface Calc{
      public int cal(int a, int b);
  }
  
  public class LambdaPractice{
      public static void main(String[] args){
          Calc calc = (a,b)-> {return (a+b);}; //인터페이스 Calc의 추상메소드 오버라이딩
          System.out.println(calc.cal(1,2)); //3 출력
      }
  }
  ```

.

### Stream API

- 함수형 프로그래밍을 위해 Java에서 제공하는 API로 데이터를 추상화하여, 여러 종류의 데이터를 같은 방식으로 처리할 수 있도록 함

- Stream API를 적용하지 않았을 경우

  ```java
  public class Run{
      public static void run(String[] args){
          int[] arr = new int[]{3,5,2,1};
          
          Arrays.sort(arr);
          for(int i=0;i<arr.length;i++){
              System.out.println(arr[i]);
          }
      }
  }
  ```

  ```tex
  1
  2
  3
  5
  ```

- Stream API를 적용한 경우

  ```java
  public class Run{
      public static void run(String[] args){
          int[] arr = new int[]{3,5,2,1};
          IntStream stream = Arrays.stream(arr);
          stream.sorted().forEach(System.out::println);
      }
  }
  ```

  ```tex
  1
  2
  3
  5
  ```

  - 배열의 stream을 생성하였으며, 위와 다른 점은 원본 객체가 되는 arr에 변형이 없음
  - 배열의 정렬은 원본 데이터를 정렬한 것이 아닌, 사실상 별도로 생성된 stream이 원본 데이터로부터 읽어들인 요소들이 정렬된 것
  - 따라서, stream의 사용은 1회성이며, 다시 사용하고자 할 경우에는 stream을 다시 생성
  - __`forEach()`__ 를 통해 내부 반복으로 작업을 처리하기 때문에 for이나 while 보다 더욱 간결하게 반복문을 표현할 수 있음

