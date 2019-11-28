# Android Architecture Components - ViewModel


### ViewModel
개인적으로 안드로이드 개발을 하면서 AAC는 디자인 패턴을 포함하여 제일 중요한 개념이라고 생각한다.  Android AAC에서 제공하는 ViewModel은 일반적인 ViewModel
과는 다르게, Android에서 편하게 사용할 수 있도록 만든 ViewModel이다. 이러한 AAC ViewModel은 Android 개발에 특화되어있으며, 명칭상 ViewModel일 뿐 실 제 ViewModel 과는 내부 동작 방법이 다르다.

### 일반적인 ViewModel
-> View와 ViewModel 간의 관계를 단절시키기 위함 ViewModel의 데이터가 수정되었더라도, View는 수정할게 없도록 설계할 수 있다(= 서로 의존하지 않게 구현) ViewModel은 용도에 따라서 최대한 구분하며, bolier plate code를 줄이는 방향으로 설계해야 한다. 한 개의 View에서는 N 개의 ViewModel을 가질 수 있으며, 반대로 한 개의 ViewModel은 N 개의 View에서 사용될 수 있다. Application 등 Android Lifecycle과 무관하게 동작해야 한다.

### AAC ViewModel
-> 화면 회전시 데이터 유지를 위한 구조로 ViewModel을 디자인하였다. Activity/Fragment에 따라서 ViewModel 유지를 하고 있다. 내부에서 Lifecycle을 사용할 수 있도록 초기화하고 있으며, onDestroy() 동작 시 ViewModel 내부의 onCleared() 호출을 통해 Release를 유도한다.

ViewModel, AAC-ViewModel 초기화 방법
ViewModel은 일반적인 객체 생성 방식을 사용한다. lifecycle이 필요한 경우 viewModel.doSomething()을 부르는 방법으로 접근한다.
```kotlin
 val viewModel = ViewModel()
 viewModel.doSomething()
```
AAC의 ViewModel은 ViewModelProviders을 이용한 초기화 한다.
```kotlin 
val viewModel = ViewModelProviders.of(this).get(ExampleViewModel::class.java)
viewModel.doSomething()
```

ViewModel 상속 클래스 생성하기
Android AAC ViewModel은 2개의 ViewModel을 상속을 제공한다. 기본 ViewModel의 내부에는 onCleared() 메소드가 포함되어있으며, Lifecycle 중 onDestroy()에서 onCleared가 호출된다.

ViewModel

```kotlin

public abstract class ViewModel {
  /**
    * This method will be called when this
    * ViewModel is no longer used and will be destroyed.
    * <p>
    * It is useful when ViewModel observes some data
    * and you need to clear this subscription to
    * prevent a leak of this ViewModel.
    */
    @SuppressWarnings("WeakerAccess")
    protected void onCleared() {
    }
}

```
```kotlin 

AndroidViewModel(application)
public class AndroidViewModel extends ViewModel {
  @SuppressLint("StaticFieldLeak")
  private Application mApplication;

  public AndroidViewModel(@NonNull Application application) {
     mApplication = application;
  }
  /**
    * Return the application.
    */
  @SuppressWarnings("TypeParameterUnusedInFormals")
  @NonNull
  public <T extends Application> T getApplication() {
    //noinspection unchecked
    return (T) mApplication;
  }
}
```
Application 필요 여부에 따라서 ViewModel과 AndroidViewModel을 상속받는 구현체를 사용하면 된다.

정리 -

다음 그림은 ViewModel과 액티비티의 생성, 화면 전환, 종료에 이르기까지의 수명주기를 보여준다.
 
 ![](https://miro.medium.com/max/1044/1*oW2OtsU4itFE-1njkwJ06w.png)
 
보통 ViewModel 인스턴스는 액티비티의 onCreate()에서 요청을 하는데, 아래와 같이 액티비티의 onCreate()가 여러 번 호출되는 상황에서도 ViewModel 스코프 는 일관되게 유지되는 것을 알 수 있습니다. ViewModel은 액티비티 스코프의 싱글톤 객체처럼 사용할 수 있기 때문에 프래그먼트들 사이에서 ViewModel을 이용해 데 이터를 쉽게 공유할 수 있습니다. 이는 프래그먼트 간 데이터 공유에 더 이상 중간자 역할로서의 액티비티가 필요하지 않다는 것을 의미하며 액티비티의 지나친 역할 수행 을 덜어낼 수 있음을 의미합니다. ViewModel은 액티비티 스코프가 완전히 종료되는 시점에 종료가 되고, 이때 onCleared() 함수가 호출됩니다. 이 콜백은 ViewModel 클래스의 유일한 함수이며 ViewModel의 리소스를 해제하기에 적합한 곳입니다.
