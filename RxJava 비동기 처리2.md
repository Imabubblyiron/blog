# RxJava 비동기처리2


## RxJava에서의 비동기처리2


RxJava에서는 개발자가 직접 비동기 처리를 하도록 설정하거나 연산자 내에서 시간을 다루는 작업을 하지 않는 한 생산자가 통지한 처리 작업을 실행하는 스레드 내에서 각 연산자의 처리 작업과 소비자의 처리 작업이 실행된다. 
RxJava에서는 각각의 처리 작업을 같은 스레드에서 처리하면 데이터를 통지하는 측은 데이터를 받아 처리하는 측의 처리속도에 영향을 받는다. 무엇보다도 데이터를 받고 처리하는데 시간이 오래걸린다면 통지하는 측에서도 처리속도에 영향을 미칠수 밖에 없게 된다. 아래 예시들을 한 번 확인해보자.

```kotlin

public void execute() {

    Flowable.interval(1000L, TimeUnit.MICROSECONDS)
    .doOnNext(
            data -> Log.e("data", System.currentTimeMillis() + "ms : "+ data))
    .subscribe(data -> Thread.sleep(2000L));
}

결과 : 

   2019-11-28 23:07:34.913 25386-25435/com.example.rxjavapractice E/data: 1574950054913ms : 0
   2019-11-28 23:07:36.915 25386-25435/com.example.rxjavapractice E/data: 1574950056915ms : 1
   2019-11-28 23:07:38.916 25386-25435/com.example.rxjavapractice E/data: 1574950058916ms : 2
   2019-11-28 23:07:40.918 25386-25435/com.example.rxjavapractice E/data: 1574950060917ms : 3

```

이 실행 결과를 보면 생산자는 1000 마이크로초마다 데이터를 통지하지만, 소비자 측은 Thread.sleep(2000L) 코드에 의해 2000밀리 초마다 데이터를 통지한다. 이와 같이 같은 스레드 내에서 스레드 작업을 처리할때는 데이터를 통지하는측 뿐만 아니라 받는 측도 고려해서 사용해야 한다.

```kotlin

private void execute() {
    Log.e("메인스레드 ", "시작");
    Flowable.just(1,2,3,4,5)
            .subscribe(new ResourceSubscriber<Integer>() {
                @Override
                public void onNext(Integer integer) {
                    String thread = Thread.currentThread().getName();
                    Log.e("thread",thread+ " : " + integer);
                }

                @Override
                public void onError(Throwable t) {
                    t.printStackTrace();
                }

                @Override
                public void onComplete() {
                    String thread = Thread.currentThread().getName();
                    Log.e("thread", " : 완료");
                }
            });
    Log.e("메인스레드 : ","종료");
}

결과 :

2019-11-28 23:16:43.985 25926-25926/com.example.rxjavapractice E/메인스레드: 시작
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: main : 1
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: main : 2
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: main : 3
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: main : 4
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: main : 5
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/thread: : 완료
2019-11-28 23:16:44.063 25926-25926/com.example.rxjavapractice E/메인스레드 : 종료

```

위의 예제는 메인 스레드에서 작동하므로 모든 데이터가 순차적으로 통지되고 처리된 후에 '종료'라는 문구를 출력한다.

```kotlin

private void execute() {
    
    Log.e("메인 스레드가 아닌 다른 스레드 ", "시작");
    
    Flowable.interval(500L, TimeUnit.MILLISECONDS)
            .subscribe(new ResourceSubscriber<Long>() {
                @Override
                public void onNext(Long aLong) {
                    
                    String thread = Thread.currentThread().getName();
                    Log.e("thread ",thread+ ": " + aLong);
                }

                @Override
                public void onError(Throwable t) {
                    t.printStackTrace();
                }

                @Override
                public void onComplete() {
                    String thread = Thread.currentThread().getName();
                    Log.e("thread ",thread + ": 완료");
                }
            });
    Log.e("메인 스레드가 아닌 다른 스레드 ","종료");
    try {
        Thread.sleep(1000L);
    } catch (InterruptedException e) {
        e.printStackTrace();
    }
}

결과 :

2019-11-28 23:20:04.437 26140-26140/com.example.rxjavapractice E/메인 스레드가 아닌 다른 스레드: 시작
2019-11-28 23:20:04.497 26140-26140/com.example.rxjavapractice E/메인 스레드가 아닌 다른 스레드: 종료
2019-11-28 23:20:04.999 26140-26175/com.example.rxjavapractice E/thread: RxComputationThreadPool-1: 0
2019-11-28 23:20:05.498 26140-26175/com.example.rxjavapractice E/thread: RxComputationThreadPool-1: 1
2019-11-28 23:20:05.998 26140-26175/com.example.rxjavapractice E/thread: RxComputationThreadPool-1: 2
2019-11-28 23:20:06.498 26140-26175/com.example.rxjavapractice E/thread: RxComputationThreadPool-1: 3

```

앞서 살펴본 예제의 just 메소드와는 다르게 interval메소드는 Flowable작업이 메인 스레드가 아닌 다른 별도의 스레드에서 실행되는 것을 확인 할 수 있다. 여기까지 아주 간단한 예제로만 RxJava에서 비동기처리 과정을 살펴보았는데 좀더 깊은 개념을 사용하는 예제를 보면 재미난 사실들이 많이 있다.
