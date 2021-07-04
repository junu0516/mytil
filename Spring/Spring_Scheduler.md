# Spring Scheduler 사용하기

> 회사에서 일을 하면서 Scheduler 라는 것을 처음 접하게 되었다. 회사 솔루션 특성상 클라이언트 프로그램에서 요청을 DB에 적재하면, 워커 프로그램에서 주기적으로 Scheduler을 돌리면서 DB에 새로운 요청정보가 있는지 살피고 요청이 들어오면 이를 수행해서 결과를 다시 클라이언트 프로그램으로 전달한다. 여기서는 회사에서 접한 Scheduler에 대해 정리해보도록 한다.



### Spring Scheduler

- 일정 시간 간격, 혹은 일정 시각에 특정 로직을 수행하기 위해 사용되는 것

  - 리눅스의 경우 cron이라는 스케줄러가 별도로 존재하지만, 이는 쉘 스크립트 단위의 명령 수행이기 때문에 단순한 자바 어플리케이션 내부에서 처리 가능한 로직의 경우에는 cron을 사용하는 것이 번거롭고 귀찮을 수 있음
  - 특정 메소드만 주기적으로 호출하면 되는데, 이를 위해 전체 jar을 굳이 주기적으로 돌릴 필요가 없다는 것

- __`Spring Scheduler`__ 외에도 __`Spring Quartz`__ 라는 방식으로도 구현 가능(후자가 더 사용하기 복잡하다 ...)

- SpringBoot를 기준으로 우선 실행 클래스에 __`@EnableScheduling`__ 을 선언하여 사용

  - 혹은 다른 실행 클래스가 선언된 패키지나 하위 패키지에 선언

  ``` java
  @SpringBootApplication
  @EnableScheduling
  public class SchedulerApplication{
      public static void main(String[] args){
          SpringApplication.run(SchedulerApplication.class, args);
      }
  }
  ```

- 이후 스케쥴링을 통해 처리할 비즈니스 로직이 수행될 메소드에 __`@Scheduled`__ 어노테이션을 선언

  - 단, 이는 __Bean으로 등록된 객체의 메소드에 선언되는 경우에만 유효함__
  - __`@EnableScheduling`__ 이 선언된 패키지나, 해당 패키지 하위에 선언되야 함

  ```java
  @Component
  public class Scheduler{
      @Scheduled(fixedDelay = 1000*10)
      public void schedulerMethodExample() throws Exception{
          //스케쥴링할 비즈니스 로직 명시
      }
  }
  ```

  - __스케쥴러 메소드는 반드시 리턴타입을 void로 선언함.__
  - __스케쥴러 메소드는 파라미터를 가질 수 없음__
  - 스케쥴러의 수행 주기 선언은 __`fixedDelay`__ 와 __`fixedRate`__ , 그리고 __`cron`__ 으로 나뉨
    - __fixedDelay__ : 직전 작업의 종료 시점에서 정의된 시간 만큼 지난 후 실행
    - __fixedRate__ :  직전 작업의 시작 시점에서 정의된 시간만큼 지난 후 실행
    - __cron__ : 리눅스의 cron과 동일하게 설정하는 것



### Scheduler 비동기 처리

- Spring Boot에서 스케줄러는 기본적으로 __스레드 1개를 사용하여 동기식으로 실행되는 구조임__

  - 따라서 이전 스케줄이 끝나야 다음 스케줄이 시작되는 것이며 여러 개의 스케쥴링 작업이 난무하면 정상적으로 실행되지 않을 수도 있음
  - __Thread pool__ 을 설정해서 멀티 스레드로 스케줄을 실행시키면 됨
  - 스케줄러를 비동기식으로(=멀티스레딩으로) 실행하고 싶다면 __`@EnableAsync`__ 를 선언하여 사용하는 방법이 있음

  ``` java
  @SpringBootApplication
  @EnableScheduling
  @EnableAsync //비동기적 실행을 선언
  public class SchedulerApplication{
      public static void main(String[] args){
          SpringApplication.run(SchedulerApplication.class, args);
      }
  }
  ```

  

  - Thread pool을 설정하고 싶다면, __`SchedulingConfigurer`__ 을 구현한 __SchedulerConfig__ 클래스에서 설정 관련 메소드를 오버라이딩하여 원하는 설정을 명시
    - [SchedulingConfigurer 인터페이스 공식문서 설명](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/annotation/SchedulingConfigurer.html)
    - [ScheduledTaskRegistrar 클래스 공식문서 설명](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/config/ScheduledTaskRegistrar.html)
    - [ThreadPoolTaskScheduler 클래스 공식문서 설명](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/concurrent/ThreadPoolTaskScheduler.html)

  ```java
  @Configuration
  public class SchedulerConfig implements SchedulingConfigurer{
  	private final int POOL_SIZE = 5; //스레드풀 사이즈
  	
  	@Override
  	public void configureTasks(ScheduledTaskRegistrar taskRegistrar){
          ThreadPoolTaskScheduler threadPoolTaskScheduler = new ThreadPoolTaskScheduler();
          
          threadPoolTaskScheduler.setPoolSize(POOL_SIZE); 
          threadPoolTaskScheduler.setThreadNamePrefix("myThread"); 
          threadPoolTaskScheduler.initialize(); //Set up the ExecutorService.
          
          //생성한 Thread pool을 작업용으로 등록
          taskRegistrar.setTaskScheduler(threadPoolTaskScheduler); 
      }
  }
  ```

  

  - __fixedRate__ 의 경우 시간 주기가 실행시간보다 짧은 경우에는 __`@Async`__ 를 붙여 사용

  ```java
  @Scheduled(fixeRate = 1000*10)
  @Async
  public void schedulerMethodExample() throws Exception{
      //스케쥴링할 비즈니스 로직 명시
  }
  ```

  

  
