# RxJava 기본 구조



## 1. 구조 


RxJava에서 데이터를 만들고 통지하는 생산좌와 통지된 데이터를 받아 처리하는 소비자로 구성된다. 또한 이 Publisher와 Subscriber 관계는 Reactive Streams 
지원 유무에 따라 나뉘게 된다. 그 중에서 Reactive Streams를 지원하는 Flowable과 Subscriber는 배압기능을 가지고 있고, onSubscribe(), onNext(), 
onError(), onComplete를 하고 소비자가 통지를 받으면 처리하게 됩니다. 하지만 Reactive Streams를 지원하지 않는 Observable과 Observer는 Flowable, Subscriber와 거의 비슷한 기능을 가직 있지마 앞서 말했다시피 배압기능은 가지고 있지 않아서 데이터 개수를 따로 요청하지 않습니다.
 
 
## 2. 연산자


연산자는 쉽게 말해서 생산자로부터 통지된 데이터가 소비자에게 도착하기 전에 데이터를 변환, 삭제, 변경 해야할 때 필요한 것입니다. 예를들면 Flowable, Observable 들이 가지고 있는 메소드 중에서 새로운 Flowable, Observable을 반환하고 최종 데이터를 통지할때까지 만들 수 있다. 이와 같이 연산자르 순차적으로 연결해 나가면서 최종 통지 데이터를 직관적으로 확인할 수 있게 된다. 아래의 예제는 1부터 5까지 데이터를 순차적으로 통지해 Flowable의 메소드인 filter 메소드를 이용하여 원하느 데이터르 골라내 후 다시 Flowable의 메소드인 map 메소드를 이용하여 데이터르 변환해 다시 통지합니다.
 
 
```kotlin
 
  Flowable<Integer> flowable =
                Flowable.just(1,2,3,4,5)
                .filter(data -> data % 2 == 0)
                .map(data -> data * 10);

  flowable
     .subscribe(data -> Log.e("출럭된 데이터", data.toString()));
     
  결과 :   
  
  2019-11-26 12:25:22.334 21985-21985/com.example.rxjavapractice E/출럭된 데이터: 20
  2019-11-26 12:25:22.334 21985-21985/com.example.rxjavapractice E/출럭된 데이터: 40
 
 ```
 
 위의 결과를 확인해보면 filter 메소드에 의해 just메소드 통지된 데이터중 짝수인 것만 다시 통지되었고, map메소드에 의해서 10배된 data들이 다시 통지되었다.
 subscribe()메소드로 구독 한 후의 출력된 데이터를 보면 알맞게 통지 처리되고 소비된 것을 확인할 수 있ㄷ.


## 3. 비동기처리 

 
RxJava에선 사용자가 직접 스레드를 관리할 필요 없이 사용할 수 있게 Scheduler를 제공합니. 데이터를 통지할때 소비할때에 모두 지정할 수 있다. 그리고 데이터를 주고 받을때에 이루어지는 처리를 외부 변수를 참조하여 바꾸지않게 주의해야한다. 아래 예제를 확인해보자.
 
 ```kotlin
 
  check = Check.True;

        Flowable<Long> flowable =
                Flowable.interval(300L, TimeUnit.MILLISECONDS)
                .take(5)
                .scan((sum,data) -> {
                    if(check == Check.True) return sum+data;
                    else return sum;
                });

        flowable.subscribe(data -> Log.e("출력된 데이터 : ", data.toString()));

        try {
            Thread.sleep(1300L);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        check = Check.False;


    }

    private enum Check {
        True, False;
    };
    
    결과 : 
    
    2019-11-26 12:48:08.553 22719-22765/com.example.rxjavapractice E/출력된 데이터 :: 0
    2019-11-26 12:48:08.852 22719-22765/com.example.rxjavapractice E/출력된 데이터 :: 1
    2019-11-26 12:48:09.153 22719-22765/com.example.rxjavapractice E/출력된 데이터 :: 3
    2019-11-26 12:48:09.454 22719-22765/com.example.rxjavapractice E/출력된 데이터 :: 6
    2019-11-26 12:48:09.752 22719-22765/com.example.rxjavapractice E/출력된 데이터 :: 6
   
 ```
 
 위의 결과를 확인해 보면 비동기 처리가 진행되는 동안 메인쓰레드에서 check를 Check.False상태로 바꿔준 후에 Flowable의 scan 메소드가 실행되지 않는 것을 확인
 할 수 있다. 이처럼 생산자와 소비자가 처리를 진행하는 동안에 외부 변수를 의도적으로 이용하는 것이 아니라면 잘못된 결과를 얻지 않도록 주의를 기울여야 한다.
