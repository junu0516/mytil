# Spring에서 Scheduler 사용하기

> 회사에서 일을 하면서 Scheduler 라는 것을 처음 접하게 되었다. 회사 솔루션 특성상 클라이언트 프로그램에서 요청을 DB에 적재하면, 워커 프로그램에서 주기적으로 Scheduler을 돌리면서 DB에 새로운 요청정보가 있는지 살피고 요청이 들어오면 이를 수행해서 결과를 다시 클라이언트 프로그램으로 전달한다. 여기서는 회사에서 접한 Scheduler에 대해 정리해보도록 한다.



### Spring Scheduler

- 일정 시간 간격, 혹은 일정 시각에 특정 로직을 수행하기 위해 사용되는 것

- __`Spring Scheduler`__ 외에도 __`Spring Quartz`__ 라는 방식으로도 구현 가능(후자가 더 사용하기 복잡하다 ...)

- SpringBoot를 기준으로 우선 실행 클래스에 __`@EnableScheduling`__ 을 선언하여 사용

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

- 스케줄러는 기본적으로 __스레드 1개를 사용하여 동기식으로 실행되는 구조임__

  - 따라서 이전 스케줄이 끝나야 다음 스케줄이 시작되는 것
  - 스케줄러를 비동기식으로 실행하고 싶다면 __`@EnableAsync`__ 를 선언하여 사용

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
