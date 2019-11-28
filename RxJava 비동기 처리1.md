# RxJava 비동기처리1


## 비동기처리


비동기 처리란 어떤 작업을 실행하는 동안 해당 작업이 끝나기를 기다리지 않고 다른 작업도 시작 할 수 있는 것을 말한다. 스레드는 일련의 작업을 수행하는 자기만의 작업공간 같은 것인데 비동기 처리를 진행하면서 중요한 개념중 하나이다. 보통 우리가 안드로이드에서 onCreate() 메소드 안에다가 코드를 적고 실행을하면 스레드라는 한 공간에서 순차적으로 코드가 실행된다. 하지만, 우리가 네트워크 통신이나 DB 검색과 같은 작업을 할때에 싱글 스레드로 작업을 하게 되면 이것들을 처리하는동안 다른 작업을 진행 할 수가 없다. 그래서 이때 필요한 것이 멀티스레드를 이용하는 것이고, 멀티 스레드를 이용하면 작업 처리를 대기하는동안 다른 작업들을 진행하게 되어 빨리 마칠수 있게 된다. 즉, 많은 작업을 하게 된다면 멀티스레드를 사용하면서 성능 향상을 기대할 수 있다. 물론 스레드를 생성하는 작업이나 실행 중인 스레드를 전환하는 작업들 자체가 부하가 걸리는 작업이기 때문에 무조건적으로 성능이 향상된다는 것은 보장할 수 없다.

그리고 비동기 처리 시 주의할 점들이 몇가지 있다.

1. 메모리 및 캐시

클래스 필드가 가리키는 값과 실제 메모리가 가리키는 값이 동일하지 않을 수 있다. 필드가 다루는 값은 메모리에서 캐시된 값으로 여기에서 값의 참조와 변경이 일어나게 된다. 하지만 메모리에 반영되기 전이라면 캐시 값과 메모리 값을 다르게 된다. 그럼에도 캐시를 사용하는 이유는 필드 값이 바뀔 때마다 실제 메모리에 매번 접근하기 보다는 캐시 값에 직접 접근 하는것이 성능에 유리하기 때문이다.


2. 원자성(Atomicty)

비동기 처리시에 작업 처리 도중 다른 작업이 끼어들 가능성이 있는지를 고려해야 한다.

```kotlin

public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        Counter counter = new Counter();

        Runnable runnable = () -> {

            for(int i=0; i<1000; i++) {
                counter.increment();
            }
            return;
        };

        ExecutorService executorService = Executors.newCachedThreadPool();

        Future<Boolean> futureFirst = executorService.submit(runnable, true);
        Future<Boolean> futureSecond = executorService.submit(runnable, true);

        try {
            if(futureFirst.get() && futureSecond.get()) {
                Log.e("count result : ", String.valueOf(counter.get()));
            }
        } catch (ExecutionException e) {
            e.printStackTrace();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        executorService.shutdown();
    }

    class Counter {

        private volatile int count;

        void increment() {
            count++;
        }

        int get() {
            return count;
        }
    }
}

결과 : 

2019-11-28 22:52:12.238 24828-24828/? E/count result :: 1621

```

위의 결과를 보면 count값이 2000이 나오길 기대했는데 1621이 나온걸 확인할 수 있다. 이 결과를 놓고 보면 원자성이 깨진걸 확인 할 수 있다. 실제로 count++ 하는 작업은 단순히 +1만 하는 것이 아니라 count 값을 가져오고 1을 더한 후에 계산된 결과를 다시 count값에 저장시키기 때문이다.  이러한 예제는 단순한 비동기처리를 설명하고자 다룬 예제이다. 다음 비동기처리2 에서 RxJava에서의 비동기처리 예제를 살펴보고자 한다.
