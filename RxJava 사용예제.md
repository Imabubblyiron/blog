# RxJava 사용예제



## 1. 사용 환경 구축 


다음은 Android Studio에서 RxJava의 사용을 위해 환경을 구축하는 것이다. 안드로이드에서는 Gradle을 이용해서 의존성을 쉽게 추가할 수 있다. 다음은 Gradle(Module : app) 수준에서 RxJava의 추가하는 방법이다.
 
 
 ```kotlin
 dependencies {
 
     ...
     implementation 'io.reactivex.rxjava2:rxjava:2.2.10'
    
 }

```

## 2. Flowable 사용예제 (Reactive Streams지원, 배압기능 제공)

아래는 Flowable을 이용해 list 안에 넣은 데이터를 순차적으로 통지후 로그로 처리된 값들을 확인해 보는 예제이다.
 
 
```kotlin
 
 public void execute() {

      Flowable<String> flowable =
              Flowable.create(emitter -> {
              
                  String[] list = {"Kim", "Choi", "Moon"};
                  
                  for( String name : list ) {
                  
                      if(emitter.isCancelled()) {
                          return;
                      }
                      emitter.onNext(name);
                  }
                  emitter.onComplete();
              }, BackpressureStrategy.BUFFER);

      flowable.observeOn(Schedulers.computation())
              .subscribe(new Subscriber<String>() {
              
                  private Subscription subscription;
                  
                  @Override
                  public void onSubscribe(Subscription subscription) {
                      this.subscription = subscription;
                      this.subscription.request(1L);
                  }

                  @Override
                  public void onNext(String s) {
                      String thread = Thread.currentThread().getName();
                      Log.e("Flowable예제 -> ", thread+ " : "+s);
                      this.subscription.request(1L);
                  }

                  @Override
                  public void onError(Throwable t) {
                      t.printStackTrace();
                  }

                  @Override
                  public void onComplete() {
                      String thread = Thread.currentThread().getName();
                      Log.e("Flowable예제 -> " , thread + " : " + "완료");
                  }
              });

      try {
          Thread.sleep(1000);
      } catch (InterruptedException e) {
          e.printStackTrace();
      }
  }
  
  결과 -> 
  
  2019-11-28 01:06:12.622 30389-30426/com.example.rxjavapractice E/Flowable예제 ->: RxComputationThreadPool-1 : Kim
  2019-11-28 01:06:12.622 30389-30426/com.example.rxjavapractice E/Flowable예제 ->: RxComputationThreadPool-1 : Choi
  2019-11-28 01:06:12.622 30389-30426/com.example.rxjavapractice E/Flowable예제 ->: RxComputationThreadPool-1 : Moon
  2019-11-28 01:06:12.625 30389-30426/com.example.rxjavapractice E/Flowable예제 ->: RxComputationThreadPool-1 : 완료
  

 ```
 
 위의 결과를 확인해보면 list에 담겨있던 데이터들이 같은 스레드안에서 순차적으로 통지가 된 후에 처리가 되었고 통지된 데이터 모두 처리가 되면 완료라는 로그를 찍어주게 된다. Flowable을 create하는 과정에서 BackpressureStrategy.BUFFER로 데이터를 버퍼링하고 있고, 중간에 onNext() 메소드를 보게되면 this.subscription.request(1L)로 받을 데이터 개수를 요청하기도 한다. 


## 3. Observable 사용예제 (Reactive Streams 미지원, 배압기능 미제공)

아래는 Observable을 이용해 list 안에 넣은 데이터를 순차적으로 통지후 로그로 처리된 값들을 확인해 보는 예제이다.

 
```kotlin

 public void execute() {
     Observable<String> observable =
             Observable.create(new ObservableOnSubscribe<String>() {
                 @Override
                 public void subscribe(ObservableEmitter<String> emitter) throws Throwable {
                     String[] list = { "Kim", "Choi", "Moon"};

                     for(String name : list) {
                         if(emitter.isDisposed()) {
                             return;
                         }
                         emitter.onNext(name);
                     }
                     emitter.onComplete();
                 }
             });

     observable
             .observeOn(Schedulers.computation())
             .subscribe(new Observer<String>() {
                 @Override
                 public void onSubscribe(Disposable d) {
                     
                 }

                 @Override
                 public void onNext(String s) {
                     String thread = Thread.currentThread().getName();
                     Log.e("Observable예제 -> ",thread+ " : "+s);
                 }

                 @Override
                 public void onError(Throwable e) {
                     e.printStackTrace();
                 }

                 @Override
                 public void onComplete() {
                     String thread = Thread.currentThread().getName();
                     Log.e("Observable예제 -> " , thread + " : 완료" );
                 }
             });

     try {
         Thread.sleep(500L);
     } catch (InterruptedException e) {
         e.printStackTrace();
     }
 }
 
```

Flowable과 Observable 만들고 실행하는 과정을 비교해본다면 그렇게 많은 차이는 없다. 비교해보자면 데이터 버퍼링을 할수있는지 없는지로 배압 기능의 유무의 차이가 있고, Observable에서 onNext() 메소드를 보면 통지하는 데이터 개수를 따로 요청하지 않고 있다. 즉 배압 기능이 없어서 데이터를 받을 때만 처리하게 된다. 
