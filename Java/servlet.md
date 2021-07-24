# Java Servlet의 개념

> Java와 Spring(정확히는 Spring MVC)을 배우면서 항상 듣게 되는 것이 Servlet이다. 대충 요청을 받아 처리하는 것 정도로 알고 있지만,  여전히 추상적인 수준에서만 Servlet을 파악한 정도이다. 
>
> 따라서 이번 기회에 Servlet에 대해 좀 더 자세히 알아보고자 한다.

.

![How Servlets works - YouTube](https://i.ytimg.com/vi/pxmWgTRETi4/maxresdefault.jpg)

.

### Servlet 이란?

- 클라이언트의 요청을 받아, 이를 처리하고 결과(응답)를 반환하는 자바의 웹프로그래밍 기술

- 클라이언트의 요청에 동적으로 작동하게 되며, Spring MVC의 일반적인 패턴에서 __`Controller`__ 에 해당하는 것이 Servlet이라 할 수 있음
  - 일반적인 웹서버(WS)가 정적인 페이지만을 응답으로 제공한다면, 웹어플리케이션서버(WAS)의 __`Servlet Container`__ 가이가지고 있는 Servlet들이 요청에 맞게 동적으로 응답을 제공하게 되는 것
  - __`Servlet Container`__ 은 클라이언트의 요청을 받아 응답하기 위해 웹서버(WS)와 소켓으로 통신함
  - 흔히 사용되는 __`Tomcat`__ 이 이러한 컨테이너의 역할을 수행하는 대표적인 소프트웨어임
  
- Java Thread를 이용하여 동작하며 멀티쓰레딩이 가능함
  - 기존의 __`CGI(Common Gateway Interface)`__  와 비교했을 때, 더 빠르고 자원을 덜 사용한다는 이점이 있음
  
    > CGI(Common Gateway Interface)
    >
    > - 웹서버가 동적으로 컨텐츠를 제공할 수 있게 하는 방법이 정의된 규격을 의미함
    > - 쉽게 말해 동적으로 응답 화면을 코딩해서 보여주는 것으로 생각하면 됨
    > - CGI 초창기에는 C나 Perl로 프로그램을 만들었지만, 이후 서버 자원을 덜 잡아먹도록 개선된 것이 Java의 Servlet인 것
  
  - 기존의 CGI는 일단 요청이 들어오면, __프로세스를 실행시켜 동적인 컨텐츠를 만드는 반면__ , Servlet은 __프로세스 내의 스레드를 실행시켜 동적인 컨텐츠를 만듦__ 
  
  
  
- 단, HTML과 같은 컨텐츠의 내용이 변경되면, Servlet을 다시 생성해야 하는 번거로움이 있음

  - Java 클래스가 메모리에 올라간 것이기 때문에 수정이 있으면 당연히 컴파일도 다시 해야 하는 것!

.

### Servlet의 생명주기

- Java Servlet의 생명주기는 Servlet Container가 수행
  - 따라서 개발자는 Servlet 자체 보다는, Servlet을 통해 구현해야 할 비즈니스 로직에 집중할 수 있음
  - 이는 Spring Framework의 주요 원리 중 하나인  __``IoC(Inversion of Control)``__ , 즉 제어의 역전이 적용된 것으로 Java EE 기반의 어플리케이션들은 실행의 흐름을 개발자가 아닌 컨테이너가 제어하는 것
- 요청을 받으면 Servlet Container은 __`HttpServletRequest`__ , __`HttpServletResponse`__ 객체를 생성하며 미리 선언해둔 __`web.xml`__ 을 기반으로 요청 URL과 적절한 Servlet(혹은 Controller Method)을 매핑
  - 이 때 들어온 요청의 최초 여부를 판단하는데, 이는 매핑할 서블릿 객체를 컨테이너가 이미 생성하여 이것이 메모리상에 존재하는 지로 판단함
  - 최초 요청이면 서블릿을 생성하여 메모리에 올리고, 그렇지 않다면 이미 생성한 객체를 찾아 활용하는 것
- Servlet 처음 생성시 초기화 메소드( __`init`__ )를 호출하고, 이후 요청이 올 때마다 적절한 메소드( __`doGet`__ 혹은 __`doPost`__ )를 호출
  - 여기서 __``init()``__ 은 최초 1회만 호출됨
- 이후 Servlet이 더 이상 필요 없어지면, __`destroy()`__ 메소드 호출 후 JVM의 GC를 통해 메모리에서 제거됨
  - 마찬가지로 __`delete()`__ 역시 최초 1회만 호출됨

.

### HttpServlet 클래스

![img](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcZ2L7I%2Fbtqvt7VXZ0J%2FPGrT5KFekwwDdTqyy66Oxk%2Fimg.png)

- 서블릿 컨테이너의 __`service()`__ 호출 후, 요청을 처리할 Servlet 클래스는 __`HttpServlet`__ 클래스를 상속받아 요청 처리 로직을 직접 구현함

  - 요청 메소드에 따라 __`doGet()`__ , __`doPost()`__ 등의 메소드 로직을 작성

  

- __`HttpServlet`__ 은 내부적으로 __`Servlet`__ 인터페이스가 구현된 __`GenericServlet`__ abstract class를 상속받은 클래스로 __`service()`__ 메소드를 통해 원하는 방식으로 요청을 처리

  - __`GenericServlet`__ : __`Servlet`__ 인터페이스를 상속받아 클라이언트-서버 환경에서, 서버 어플리케이션으로서 필요한 기능이 명시된 클래스로 __``service()``__ 를 제외한 필요한 메소드들을 재정의하여 적절한 기능을 구현

  - __`Servlet`__ : 서블릿 기반 어플리케이션 구현에 반드시 필요한 메소드가 명시된 인터페이스

    - __`init()`__ , __`service()`__ , __`destroy()`__ , __`getServletConfig()`__ , __`getServletInfo()`__ 등 서블릿 생명주기에 꼭 필요한 메소드들이 정의되어 있음

    

-  HttpServlet에는 각 요청에 맞는 추상 메소드를 구현하여 요청 처리

  ```java
  protected doDelete(HttpServletRequest request, HttpServletResponse response);
  protected doGet(HttpServletRequest request, HttpServletResponse response);
  protected doPost(HttpServletRequest request, HttpServletResponse response);
  protected doHead(HttpServletRequest request, HttpServletResponse response);
  protected doOptions(HttpServletRequest request, HttpServletResponse response);
  protected doPut(HttpServletRequest request, HttpServletResponse response);
  // 이런 식으로 http request method에 맞는 추상 메소드를 구현
  ```

  

