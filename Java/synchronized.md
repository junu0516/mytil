# synchronized 키워드의 역할

![Synchronization in java and thread synchronization in java - JavaGoal](https://javagoal.com/wp-content/uploads/2020/09/31.png)

[공식문서 설명](https://docs.oracle.com/javase/tutorial/essential/concurrency/syncmeth.html)

### synchronized 키워드와 스레드 동기화

- 스레드가 하나의 자원을 사용하고자 할 때, 해당 스레드만 제외하고 나머지 스레드는 자원에 접근 못하도록 막는 것을 동기화라고 함

- 자바에서의 synchronized는 __객체에 대한 동기화__ 로 이루어지며, 이는 동기화된 블록에는 __한 시점에는 하나의 스레드만이 접근 및 실행__ 되도록 조정함
- 쉽게 말해 synchronized 키워드를 통해 임계 영역을 설정하여 한 번에 하나의 스레드만 접근하도록 하는 것
- __자바에서의 멀티 스레드__ 와 유의점
  - 자바 프로그램은 기본적으로 __`메인 스레드`__ 가 __`main()`__ 메소드를 실행시키면서 시작되며, 마지막 코드를 실행하거나 __`return`__ 을 만나면 종료됨
  - __`JVM`__ 이 생성한 메인 스레드는, 별도의 작업 스레드를 만들어 이를 병렬로 실행시킬 수 있음
  - 다시말해, 멀티 스레드를 생성할 수 있음을 의미하며, __멀티스레딩에서는 실행 중인 스레드가 하나라도 존재하면 프로세스가 종료되지 않음__
  - 하지만 여러 스레드가 객체를 공유할 경우, 동기화가 제대로 이루어지지 않으면 상태가 예측한 것과 다르게 변경될 수 있음
  - 따라서 __`임계 영역(Critical Section)`__ 을 지정하기 위해 __`synchronized`__ 키워드를 사용하는 것

.

### syncrhonized 키워드 사용하기

- 특정 공유자원에 대해 한 번에 하나의 스레드만 접근하도록 synchronized로 임계영역을 설정
- __`Runnable`__ 인터페이스를 상속받은 __MyThread__ 클래스와 공유자원이 될 __SharedResource__ 클래스를 생성한 후, __AsyncRun__ 이라는 실행 메소드에서 스레드를 실행시킬 것

```java
public class MyThread implements Runnable {
	private String threadName; //스레드 이름
	private SharedResource resource; //공유 자원
	
    //공유자원은 외부로부터 생성자 주입
	public MyThread(String threadName, SharedResource resource) {
		this.threadName = threadName;
		this.resource = resource;
	}
	
	public void setThreadName(String threadName) {
		this.threadName = threadName;
	}
	
	public String getThreadName() {
		return this.threadName;
	}
	
	@Override
	public void run() {
		resource.count(this.threadName);
	}

}

```

```java
public class SharedResource {
	
	public synchronized void count(String threadName) {
		System.out.println(threadName+" starts running");
		for(int i=0;i<5;i++) {
			System.out.println(threadName+" counts "+i);
		}
		System.out.println(threadName+" ends up running");
	}
}

```

```java
public class AsyncRun {
	//공유자원을 static으로 선언
	static SharedResource resource = new SharedResource();
	
    //실행 메소드
	public static void main(String[] args) throws InterruptedException {
		Thread thread1 = new Thread(new MyThread("thread1",resource));
		Thread thread2 = new Thread(new MyThread("thread2",resource));
		Thread thread3 = new Thread(new MyThread("thread3",resource));
		
		thread1.start();
		thread2.start();
		thread3.start();
	}
}
```



- 우선 __SharedResource__ 객체의 __count()__ 메소드가 임계영역 처리가 되어있지 않다면, 실행 결과는 아래와 같음

  - __join()__ 이 없기 때문에, 스레드의 실행이 매번 뒤섞여서 나타남

  ```
  thread2 starts running
  thread1 starts running
  thread3 starts running
  thread1 counts 0
  thread2 counts 0
  thread1 counts 1
  thread1 counts 2
  thread1 counts 3
  thread1 counts 4
  thread3 counts 0
  thread1 ends up running
  thread2 counts 1
  thread3 counts 1
  thread3 counts 2
  thread3 counts 3
  thread3 counts 4
  thread3 ends up running
  thread2 counts 2
  thread2 counts 3
  thread2 counts 4
  thread2 ends up running
  ```



- 임계영역으로 지정하면 결과는 아래와 같이, __count()__ 메소드를 하나의 스레드가 순서대로 실행하는 것을 확인할 수 있음

  - __count()__ 메소드는 한 번에 하나의 스레드만 접근 가능하기 때문에 결과적으로 카운팅이 모두 끝나야 다음 스레드가 카운팅을 시작하는 것!

  ```
  thread1 starts running
  thread1 counts 0
  thread1 counts 1
  thread1 counts 2
  thread1 counts 3
  thread1 counts 4
  thread1 ends up running
  thread3 starts running
  thread3 counts 0
  thread3 counts 1
  thread3 counts 2
  thread3 counts 3
  thread3 counts 4
  thread3 ends up running
  thread2 starts running
  thread2 counts 0
  thread2 counts 1
  thread2 counts 2
  thread2 counts 3
  thread2 counts 4
  thread2 ends up running
  ```



- __synchronized__ 키워드를 아래와 같이 메소드 내의 특정 블록에만 선언하여 임계영역으로 지정할 수도 있음

  - 아래의 경우 __count()__ 메소드에서 반복문의 실행 부분만을 임계영역 처리하였음

  ```java
  	
  	public void count(String threadName) {
  		System.out.println(threadName+" starts running");
  		synchronized(this) {
  			for(int i=0;i<5;i++) {
  				System.out.println(threadName+" counts "+i);
  			}			
  		}
  		System.out.println(threadName+" ends up running");
  	}
  }
  
  ```

  - 결과는 아래와 같이 반복문 부분만 임계영역으로 처리되어 스레드 하나씩 순서대로 실행됨을 알 수 있음
    - 반복문 이외의 부분은 실행할 때마다 스레드 별 순서가 각기 다르게 나올 것

  ```
  thread3 starts running
  thread1 starts running
  thread2 starts running
  thread3 counts 0
  thread3 counts 1
  thread3 counts 2
  thread3 counts 3
  thread3 counts 4
  thread3 ends up running
  thread2 counts 0
  thread2 counts 1
  thread2 counts 2
  thread2 counts 3
  thread2 counts 4
  thread2 ends up running
  thread1 counts 0
  thread1 counts 1
  thread1 counts 2
  thread1 counts 3
  thread1 counts 4
  thread1 ends up running
  
  ```

  

