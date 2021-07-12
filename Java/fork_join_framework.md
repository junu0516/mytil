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

```
 (다른 블로그에서 참고한 내용)
 자바에서 병렬처리를 하고자 할 경우에는, 보통 스레드를 별도로 만들면 된다. 하지만 스레드를 생성하고 제거하는 작업 또한 오버헤드가 크기 때문에, 오히려 이로 인해 시간이 더 걸릴 수 있다. 그렇다면 스레드의 개수를 일정하게 유지하면서, 별도로 작업 큐를 통해 작업 목록을 관리해보는 방식을 생각해볼 수 있겠지만, 이 또한 작업 큐에 대한 접근에서의 경쟁으로 인해 시간이 더 걸릴 수 있다.
 따라서 이런 경우에 일정 개수의 스레드를 유지하면서, 스레드마다 독립적으로 작업 큐를 관리하고 하나의 스레드의 작업 큐가 빌 경우에 다른 스레드의 작업 큐에서 작업을 가져오면 좀 더 효율적인 병렬처리가 가능할 것이다.
```

...

### Fork-Join Framework

![쓰레드풀 과 ForkJoinPool](https://img1.daumcdn.net/thumb/R720x0.q80/?scode=mtistory2&fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F2628974A57BD30A21D66F0)

- 위의 서두에서 언급한 병렬 처리를 위해 Java7부터 추가된 기능으로, 작업을 일정한 개수로 나눈 후(split) 나뉜 작업들을 __`Thread Pool`__ 의 __`Worker Thread`__ 로 분배하여 병렬처리하고 __`join`__ 하여 이를 다시 합산함

  - __`join`__ 은 좀 더 쉽게 말해, 스레드들이 작업을 처리할 때까지 기다린 후, 처리 결과를 합치는 것을 의미
  - Child Level에서의 처리 결과를 합쳐 상위 Parent Level로 전달

- 자바 코드로 구현시 각각의 __Task__ 를 나타내는 클래스는 __`RecursiveAction`__ 과 __`RecursiveTask`__ 가 존재

  - __RecursiveAction__ : 리턴값이 존재하지 않는 작업
  - __RecursiveTask__ : 리턴값이 존재하는 작업

  

- __`RecursiveAction`__ 을 통한 병렬처리 예제

  - RecursiveAction 클래스를 상속받은 후, __`compute()`__ 를 오버라이딩하여 병렬 처리 내용을 구현

  - RecursiveAction 구현 클래스

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
                System.out.println("["+LocalDateTime.now()+"]"+threadName+" & Splitting workers : "+ this.workers);
                sleep(1000);
            
                List<RecursiveActionTest> subTasks = new ArrayList<>();
                subTasks.addAll(createSubTasks());
    
                for(RecursiveAction subTask : subTasks){
                    subTask.fork();
                }
            }else{
                System.out.println("["+LocalDateTime.now()+"]"+threadName+" & Currently running workers : "+ this.workers);
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
    
    - 작업 중간에 쉬도록 __sleep()__ 메소드를 호출하였음.(쉬지 않고 바로 종료되면 병렬처리를 확인하기 힘들기 때문)
    - __`compute()`__ 는 workers의 크기가 16보다 클 경우에 작업을 subTask로 나누고, 16 이하인 경우에는 나누지 않고 해당 스레드에서 작업을 처리
    
    
    
  - 실행 클래스
  
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
  
      

