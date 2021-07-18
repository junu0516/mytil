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
- 요청을 받으면 Servlet Container은 __`HttpServletRequest`__ , __`HttpServletResponse`__ 객체를 생성하며 미리 선언해둔 __`web.xml`__ 을 기반으로 요청 URL과 적절한 Servlet(혹은 Controller Method)을 매핑
- 처음 생성시 초기화 메소드( __`init`__ )를 호출하고, 이후 요청이 올 때마다 적절한 메소드( __`doGet`__ 혹은 __`doPost`__ )를 호출
- 이후 Servlet이 더 이상 필요 없어지면, __`destroy`__ 메소드 호출 후 JVM의 GC를 통해 메모리에서 제거됨

