## JVM의 구조와 동작

![img](https://upload.wikimedia.org/wikipedia/commons/d/dd/JvmSpec7.png)



### JVM(Java Virtual Machine)의 개념

- 자바 바이트코드를 실행하는 주체이며, 자바 어플리케이션과 운영체제 간의 중개 및 메모리 관리의 역할을 수행

  - 자바 어플리케이션의 실행이 다양한 운영체제 환경에 구애받지 않도록 함
  - __`Garbage Collection(쓰레기 수집)`__ 을 통해  메모리를 효율적으로 사용

- __자바 프로그램 실행 원리__

  ```
  1. 프로그램 실행 후, JVM이 운영체제로부터 메모리(자원)를 할당받으면, JVM은 용도에 맞게 메모리를 나눠서 관리
  2. 자바 컴파일러(javac)가 자바 코드(.java)를 읽어, 자바 바이트코드(.class)로 변환(컴파일) 
  3. 클래스 로더(Class Loader)가 생성된 class 파일들을 JVM에 로딩
  4. 로딩된 class 파일이 실행 엔진(Execution Engine)을 통해 기계가 읽어들일 수 있도록 해석됨
  5. 해석된 바이트코드는 런타임 영역(Runtime Data Area)에 배치되어, 실질적으로 자바 어플리케이션이 수행됨
  ```

- 자바 프로그램의 실행 과정송에서 JVM은 필요에 따라 GC나 스레드 동기화(Thread Synchronization)와 같은 작업을 수행



### JVM의 구조

- __Class Loader(클래스 로더)__ 
  - 런타임 시점에 동적으로 JVM에 컴파일된 class 파일을 로드하며, Runtime Data Area에 클래스 파일을 적재
  - 쉽게 말해, 컴파일된 클래스 파일들을 메모리에 적재하는 것
  - 

