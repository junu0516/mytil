# Fork-Join framework

> 처음 회사에 와서 코드를 보고 제일 눈이 갔던 부분은 멀티스레딩과 관련된 부분이었다. 공부할 때마다 스레드가 무엇인지, synchronized 키워드가 무엇인지 등은 배웠지만 막상 이것이 현업에서 어떻게 고려되는 지에 대해서는 알 길이 없었는데, 회사 코드를 뜯어 보면서 미약하게나마 멀티스레딩을 고려해야 하는 상황에 대해 생각해볼 수 있었다. 
>
> 
>
> 자바에서 우선 멀티스레딩과 관련된 부분을 더 공부하고자 Coursera에서 Parallel Programming 강의를 듣고 있으며 TIL의 java 항목에 틈틈히 강의에서 배운 것들을 정리해보고자 한다.



[참고]

- [Paralell Programming in Java](https://www.coursera.org/learn/parallel-programming-in-java/supplement/wlDUr/1-2-lecture-summary)

- [참고 블로그1](https://velog.io/@vies00/Java-work-stealing-fork-join-xljtjnflly)
- [참고블로그2](https://ryukato.github.io/java/concurrency/2017/10/24/fork-join.html)

- [Fork/Join 공식문서](https://docs.oracle.com/javase/tutorial/essential/concurrency/forkjoin.html)

- [RecursiveAction 공식문서](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/RecursiveAction.html)



### Thread vs Thread Pool

![Thread pool(쓰레드 풀)](https://img1.daumcdn.net/thumb/R800x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F991DC8475BCD133D20)

- 스레드를 생성할 경우 동일한 메모리 영역 내에서 관리되기 때문에, 단일 프로세스만을 사용하는 것보다 성능 면에서 더욱 빠른 속도를 기대할 수 있음
- 하지만 멀티 스레딩 구조에서, 스래드의 생성 및 수거 또한 일정한 비용을 수반하기 때문에 이를 고려해야 함
- 따라서 스레드의 생성 및 수거에 드는 비용을 줄이고, 스레드의 재사용성을 높이기 위해 별도의 __`Thread Pool`__ 을 만들어 놓는 것
- 병렬 처리시 작업 큐(Queue)에 있는 작업을 미리 지정한 풀의 스레드들이 하나씩 맡아 처리하며, 작업의 수가 많아져도 스레드의 수는 일정하기 때문에 성능이 스레드의 생성 및 수거로 인해 성능이 저하되는 상황을 방지할 수 있음!

.

### Fork-Join Framework

![쓰레드풀 과 ForkJoinPool](https://img1.daumcdn.net/thumb/R720x0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F2628974A57BD30A21D66F0)

- 위의 서두에서 언급한 병렬 처리를 위해 Java7부터 추가된 기능으로, 작업을 일정한 개수로 나눈 후(split) 나뉜 작업들을 __`Thread Pool`__ 의 __`Worker Thread`__ 로 분배하여 병렬처리하고 __`join`__ 하여 이를 다시 합산함

  - __`join`__ 은 좀 더 쉽게 말해, 스레드들이 작업을 처리할 때까지 기다린 후, 처리 결과를 합치는 것을 의미
  - Child Level에서의 처리 결과를 합쳐 상위 Parent Level로 전달
  - 특별한 점은, 작업이 __`Thread Pool`__ 내의 여러 __`Worker Thread`__ 로 분배될 때, 특정 __Worker Thread__ 가 처리할 작업이 없을 경우에는, 다른 __Worker Thread__ 로부터 치할 작업을 훔쳐와서(__`Work-Strealing`__) 처리함

  .

- 자바 코드로 스레드 풀을 구현할 때는 __`ForkJoinPool`__ 클래스를 활용하며, 이는 __`ExecutorService`__ 를 구현한 __`AbstractExecutorService`__ 추상 클래스를 상속받고 있음([공식문서](https://docs.oracle.com/javase/7/docs/api/java/util/concurrent/ForkJoinPool.html))

  - 위에서 언급했듯이 다른 __`ExecutorService`__ 구현체와 달리, __`Work-Strealing`__ 알고리즘이 적용되었다는 차이가 있음
  - 

  .

- 자바 코드로 구현시 각각의 __Task__ 를 나타내는 클래스는 __`RecursiveAction`__ 과 __`RecursiveTask`__ 가 존재

  - __RecursiveAction__ : 리턴값이 존재하지 않는 작업을 위한 추상클래스로 __`compute()`__ 추상 메소드를 구현하여 사용

    - join의 결과는 따라서 항상 null이 됨

    ```java
    package java.util.concurrent;
    
    public abstract class RecursiveAction extends ForkJoinTask<Void> {
        private static final long serialVersionUID = 5232453952276485070L;
    
        protected abstract void compute();
    
        public final Void getRawResult() { return null; }
    
        protected final void setRawResult(Void mustBeNull) { }
    
        protected final boolean exec() {
            compute();
            return true;
        }
    }
    
    ```

    

  - __RecursiveTask__ : 리턴값이 존재하는 작업으로 RecursiveAction과 달리 __`compute()`__ 의 리턴타입이 void가 아님

    ```java
    
    public abstract class RecursiveTask<V> extends ForkJoinTask<V> {
        private static final long serialVersionUID = 5232453952276485270L;
        V result;
    
        protected abstract V compute();
    	
        public final V getRawResult() {
            return result;
        }
    
        protected final void setRawResult(V value) {
            result = value;
        }
    
        protected final boolean exec() {
            result = compute();
            return true;
        }
    }
    
    ```

    

  - __RecursiveAction__ , __RecursiveTask__ 의 구조를 보면 둘 다 __`ForkJoinTask<V>`__ 클래스를 다시 구현하고 있음을 알 수 있음(클래스 구조가 너무 길어서 여기서는 생략...)

    - __`ForkJoinPool`__ 에서 수행되는 작업을 위한 추상클래스로 [공식문서](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/RecursiveAction.html) 를 보면 이 클래스에 __`fork()`__ , __`join()`__ 등의 비동기처리를 위한 주요 메소드가 정의되있음을 알 수 있음
    - __fork()__ : 작업 분배를 위한 메소드
      - Arranges to asynchronously execute this task in the pool the current task is running in, if applicable, or using the [`ForkJoinPool.commonPool()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinPool.html#commonPool--) if not [`inForkJoinPool()`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinTask.html#inForkJoinPool--).
      - fork 명령을 작업을 분배(split) 하는 것과 동일한 개념으로 스레드풀에서 작업이 비동기적으로 수행되도록 재정렬하는 역할을 수행
      - 여기서 __`inForkJoinPool()`__ 은 현재 스레드가 ForkJoinPool 연산에 의해 실행될 경우 true를 리턴하는 메소드임
    - __join()__ :  연산의 결과를 리턴하는 메소드
      - Returns the result of the computation when it [`is done`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ForkJoinTask.html#isDone--).
      - 여기서 __`isDone()`__ 은 상위 추상클래스인 __`Future`__ 클래스에 정의된 메소드로, 작업의 연산이 끝나면 true를 리턴함
      - 따라서 join()은 위에서 처리 결과를 합친다는 join 명령의 개념과 유사하게, 분기된 각 작업의 연산이 끝나면 연산 결과를 리턴하며 이러한 연산 결과가 연속적으로 합쳐지는 것
    - __invoke()__ :  미리 정의된 스레드풀에 작업을 던져서 순차적으로 작업을 fork 하는 것

    

### RecursiveAction 을 활용한 병렬처리 예제

- RecursiveAction 클래스를 상속받은 후, __`compute()`__ 를 오버라이딩하여 병렬 처리 내용을 구현해보자

- 먼저 실행 클래스에 __`ForkJoinPool`__ 을 생성하고, 인자로 원하는 만큼의 스레드 수를 명시

  ```java
  public class Run {
  
  	public static void main(String[] args) {
  		//여기서는 스레드를 4개로 테스트
  		ForkJoinPool forkJoinPool = new ForkJoinPool(4);
  		
  	}
  }
  ```

  

- RecursiveAction 를 구현하여 원하는 작업 내용을 정의(후에 ForkJoinPool이 정의한 작업을 처리)

  - 위에서 설명한대로 __`RecursiveAction`__ 은 리턴값이 없는 작업이며, 작업의 실행은 __`compute()`__ 를 오버라이딩하여 구현함
  - 작업을 분할하고 싶으면 또다른 __`RecursiveTask`__ 를 생성한 후 __`fork()`__ 를 호출하여 다른 스레드가 하위 작업을 처리하도록 하면 됨

  ```java
  public class RecursiveActionTest extends RecursiveAction {
  	private long workers = 0;
      
      public RecursiveActionTest(long workers){
          this.workers = workers;
      }
  	
  	@Override
  	protected void compute() {
          
          String threadName = Thread.currentThread().getName();
          if(workers>16){
              System.out.println("["+LocalDateTime.now()+"]"
                                 	  +threadName
                                    +" & Splitting workers : "
                                    + this.workers);
              sleep(1000);
              List<RecursiveActionTest> subTasks = new ArrayList<>();
              subTasks.addAll(createSubTasks());
  
              for(RecursiveAction subTask : subTasks){
                  subTask.fork();
              }
          }else{
              System.out.println("["+LocalDateTime.now()+"]"
                                    +threadName
                                    +" & Currently running workers : "
                                    + this.workers);
          }
  	}
      
      private List<RecursiveActionTest> createSubTasks(){
          List<RecursiveActionTest> subTasks = new ArrayList<>();
          RecursiveActionTest subTask1 = new RecursiveActionTest(this.workers/2);
          RecursiveActionTest subTask2 = new RecursiveActionTest(this.workers/2);
          
          subTasks.add(subTask1);
          subTasks.add(subTask2);
          
          return subTasks;
      }
      
      private void sleep(int mills){
          try{
              Thread.sleep(mills);
          }catch(InterruptedException e){
              e.printStackTrace();
          }
      }
  }
  
  ```

  - 병렬 처리 확인을 원활히 하고자 작업 중간에 쉬도록 __sleep()__ 메소드를 호출하였음.
  - __`compute()`__ 는 workers의 크기가 16보다 클 경우에 작업을 각각 workers의 크기를 반으로 줄인 subTask로 나누고, 16 이하인 경우에는 나누지 않고 해당 스레드에서 작업을 처리하는 식으로 구현
  - 이후 나뉜 subTask에 대해서도 동일하게 검증하여 다시 하위 subTask로 __`fork()`__ 를 통해 나눠줌



- 실행 클래스에 최초 작업을 위한 __`RecursiveAction`__ 객체를 만든 후, 이를 미리 만들어둔 __`ForkJoinPool`__ 에 던져서 fork-join 작업을 수행

- 여기서는 스레드 4개를 정의했기 때문에, 이후부터는 4개의 스레드가 돌아가면서 fork를 통해 나뉜 하위 작업들을 각각 처리하게 될 것

  ```java
  public class Run {
  
  	public static void main(String[] args) {
  		
  		ForkJoinPool forkJoinPool = new ForkJoinPool(4);
  		
  		RecursiveActionTest recursiveActionTest = new RecursiveActionTest(128);
  		forkJoinPool.invoke(recursiveActionTest);
  		
  		try {
  			forkJoinPool.awaitTermination(5, TimeUnit.SECONDS);
  		}catch(InterruptedException e) {
  			e.printStackTrace();
  		}
  	}
  }
  ```

  - __`ForkJoinPool`__ : Fork/Join Framework를 구현하기 위한 스레드 풀 클래스를 선언

  - workers 크기가 128인 __`RecursiveAction`__ 객체를 하나 만든 후, __`invoke()`__ 를 통해 생성한 객체를 인자로 전달하여 처리함

  - 실행하면 아래와 같은 결과가 콘솔에 출력된 것을 확인할 수 있을 것

    ```java
    [2021-07-12T23:39:40.265]ForkJoinPool-1-worker-1 & Splitting workers : 128
    [2021-07-12T23:39:41.275]ForkJoinPool-1-worker-2 & Splitting workers : 64
    [2021-07-12T23:39:41.275]ForkJoinPool-1-worker-1 & Splitting workers : 64
    [2021-07-12T23:39:42.276]ForkJoinPool-1-worker-1 & Splitting workers : 32
    [2021-07-12T23:39:42.276]ForkJoinPool-1-worker-3 & Splitting workers : 32
    [2021-07-12T23:39:42.277]ForkJoinPool-1-worker-2 & Splitting workers : 32
    [2021-07-12T23:39:42.277]ForkJoinPool-1-worker-0 & Splitting workers : 32
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-0 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-3 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-1 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-3 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-1 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-2 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-3 & Currently running workers : 16
    [2021-07-12T23:39:43.280]ForkJoinPool-1-worker-0 & Currently running workers : 16
    ```

    - 최초에 정의한 작업의 workers 크기는 128이었고, 이를 2개 -> 4개 -> 8개로 각각 분기한 것
