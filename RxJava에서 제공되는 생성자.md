# RxJava에서 제공되는 생성자

앞서 살펴본 예제에서 사용되는 생성자로는 Flowable, Observable을 예로 들어 사용했지만 다음 살펴볼 예제에서는 Single, Maybe, Completable 생성자를 사용한다. 이 클래스들은 통지 데이터가 1건 또는 1건도 없을때 사용하게 된다. 

## 1. Single(생성자) - SingleObserver(소비자) 


Single 생성자는 통지할 데이터가 반드시 1건이 있을때 사용한다. 아래는 Single에 대한 예시이다.
 
 ```kotlin
 
 public void execute() {
     Single<DayOfWeek> single = Single.create( emitter -> {
         emitter.onSuccess(LocalDate.now().getDayOfWeek());
     });

     single.subscribe(new SingleObserver<DayOfWeek>() {

         private Disposable disposable;

         @Override
         public void onSubscribe(Disposable d) {
             this.disposable = d;
         }

         @Override
         public void onSuccess(DayOfWeek dayOfWeek) {
             Log.e("SingleSample -> ", "Success : " +dayOfWeek );
         }

         @Override
         public void onError(Throwable e) {
             Log.e("SingleSample -> ", "Failed!");
         }
     });
 }

결과 : 

2019-11-28 09:36:09.367 31339-31339/com.example.rxjavapractice E/SingleSample ->: Success : THURSDAY

```

## 2. Maybe(생성자) - MaybeObserver(소비자)


Maybe 생성자는 데이터가 반드시 1건이 있거나 없을때 사용한다. 즉, 데이터가 있으면 해당하는 데이터를 통지하고 처리하며, 데이터가 없으면 완료를 통지하고 처리한다. 아래는 Maybe에 대한 예시이다.
 
 
```kotlin
 
 public void execute() {

     Maybe<DayOfWeek> maybe = Maybe.create(emitter -> {
         emitter.onSuccess(LocalDate.now().getDayOfWeek());
     });

     maybe.subscribe(new MaybeObserver<DayOfWeek>() {

         private Disposable disposable;
         @Override
         public void onSubscribe(Disposable d) {
             this.disposable = d;
         }

         @Override
         public void onSuccess(DayOfWeek dayOfWeek) {
             Log.e("MaybeSample -> ", "Success " + dayOfWeek);
         }

         @Override
         public void onError(Throwable e) {
             Log.e("MaybeSample -> " , "Failed!");
         }

         @Override
         public void onComplete() {
             //데이터가 1건도 없을때 완료 통지를 함.
             Log.e("MaybeSample -> ", "Completed");
         }
     });
 }

결과 : 2019-11-28 09:34:54.714 31202-31202/com.example.rxjavapractice E/MaybeSample ->: Success THURSDAY

 ```
 


## 3. Completable(생성자) - CompletableObserver(소비자)


Completable 생성자는 데이터는 통지하지 않고 완료만을 통지한다.

 
```kotlin

 public void execute() {

     Completable completable = Completable.create( emitter -> {
         emitter.onComplete();
     });

     completable
             .subscribe(new CompletableObserver() {
                 @Override
                 public void onSubscribe(Disposable d) {

                 }

                 @Override
                 public void onComplete() {
                     Log.e("CompletableSample -> ", "Completed");
                 }

                 @Override
                 public void onError(Throwable e) {

                 }
             });
 }
 
 결과 :
 
 2019-11-28 09:39:23.434 31591-31591/com.example.rxjavapractice E/CompletableSample ->: Completed


```

이 클래스들은 공통적으로 처리 중에 에러가 발생하면 에러를 통지하게된다.
