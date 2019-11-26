# RxJava의 구조

1. 구조 

 RxJava에서 데이터를 만들고 통지하는 생산좌와 통지된 데이터를 받아 처리하는 소비자로 구성된다. 또한 이 Publisher와 Subscriber 관계는 Reactive Streams 
 지원 유무에 따라 나뉘게 된다. 그 중에서 Reactive Streams를 지원하는 Flowable과 Subscriber는 배압기능을 가지고 있고, onSubscribe(), onNext(), 
 onError(), onComplete를 하고 소비자가 통지를 받으면 처리하게 됩니다. 
 하지만 Reactive Streams를 지원하지 않는 Observable과 Observer는 Flowable, Subscriber와 거의 비슷한 기능을 가직 있지마 앞서 말했다시피 배압기능은 가
 지고 있지 않아서 데이터 개수를 따로 요청하지 않습니다.
 
2. 연산자

 연산자는 쉽게 말해서 생산자로부터 통지된 데이터가 소비자에게 도착하기 전에 데이터를 변환, 삭제, 변경 해야할 때 필요한 것입니다. 예를들면 Flowable, 
 Observable 들이 가지고 있는 메소드 중에서 새로운 Flowable, Observable을 반환하고 최종 데이터를 통지할때까지 만들 수 있다. 이와 같이 연산자르 순차적으로 연
 결해 나가면서 최종 통지 데이터를 직관적으로 확인할 수 있게 된다. 아래의 예제는 1부터 5까지 데이터를 순차적으로 통지해 Flowable의 메소드인 filter 메소드를 이용하
 여 원하느 데이터르 골라내 후 다시 Flowable의 메소드인 map 메소드를 이용하여 데이터르 변환해 다시 통지합니다.
 
 
 
 

