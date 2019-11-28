# RxJava 람다식


람다식은 함수형 인터페이스를 구현하기 위해 Java 8에서 도입한 표현식이다. RxJava사용을 편하게하기 위해서 람다식을 도입했다고 한다. 그래서 우리는 람다식을 사용하면 개발 효울이 높아질수 있게 된다.

## 1. 기본 문법

람다식에서 사용하게되는 함수형 인터페이스는 구현해야 하는 메소드가 1개 뿐인 인터페이스이다. 보통 함수형 인터페이스와 익명클래스를 헷갈리곤 하기때문에 아래 예제를 보면서 차이점을 볼 필요가 있다.


- 람다식

```kotlin

Func<T, V> func =
    (T) -> {
        return new Func(T);
    }
    
```

- 익명 클래스

```kotlin

Func<T,V> func = new Func(T, V) {
    
        @Override
        public V dosomething(T data) {  
            return new V(T);
        }
};

``` 
 
 람다식과 익명 클래스로 구현한 코드를 보면 람다식을 이용하여 코드를 적게되면 구현해야 할 인터페이스나 메서드의 선언은 생략함과 동시에 인자와 실행문만 작성하면 된다. 뿐만 아니라 인자 타입이나 결과값 부분까지도 생략할 수 있게된다. 단순한 코드일수록 더 가독성 있게 코딩을 할 수 있다. 앞서 말했다시피 RxJava는 람다식을 이용하면 개발 효율이 올라간다는 장점이 있다. 또한 람의 t다식과 익명 클래스의 다른점이라고 한다면 각각에서 this가 가리키는 대상이 다르다는 것이다. 람다식의 this는 람다식을 구현한 클래스의 인스턴스를 나타내고, 익명클래스에서의 this는 이것을 구현한 인터페이스의 인스턴스를 가리키게 된다.

```kotlin

public void execute() throws Throwable {
    Action anonymous = new Action() {
        @Override
        public void run() throws Throwable {
            Log.e("Anonymous class : ", this.getClass().toString());
        }
    };

    Action lambda = () -> Log.e("Lambda func : ", this.getClass().toString());

    anonymous.run();
    lambda.run();
}

결과 : 

2019-11-28 22:28:32.025 24079-24079/com.example.rxjavapractice E/Anonymous class :: class com.example.rxjavapractice.MainActivity$1
2019-11-28 22:28:32.025 24079-24079/com.example.rxjavapractice E/Lambda func :: class com.example.rxjavapractice.MainActivity


```

위의 결과를 보면 첫번째 줄의 결과는 함수형 인터페이스의 인스턴스를 나타내고,  두번째 줄 람다식에서의 결과는 구현된 클래스의 인스턴스를 나타낸다.


## 2. 함수형 인터페이스


함수형 인터페이스는 구현해야 하는 메소드가 1개만 있기 때문에 인자의 유무, 타입의 유무 및 정보, 반환 값의 유무에 대한 정보가 필요하다.
 
1.  Function/Predicate
 
Function과 Predicate는 구현해야 할 메서드에 인자와 반환값이 있는 함수형 인터페이스이다. Function은 메서드의 반환값에 제한이 없고, Predicate는 메서드의 반환값이 Boolean이어야 한다.

2.  BooleanSupplier

BooleanSupplier은 인자가 없고 boolean만을 반환하는 메서드이다. 일반적은 Supplier은 다양한 타잆의 값을 반환하지만 RxJava에서는 Boolean으로 반환하는 BooleanSupplier만 제공된다.
    
3. Action/Consumer

Action은 구현해야 할 메서드에 인자가없는 run()같은 메서드를, Consumer는 반환 값이 없고 accept(T t)와 같이 인자를 가지는 것에 차이가 있다. 어떤 작용이 발생하는 메서드가 있는 함수형 인터페이스이다.
    
4. Cancellable

Action과 마찬가지로 인자, 반환값을 모두 가지지 않는 메서드를 가지고 있다. 취소 처리를 구현하는데에 사용한다.

