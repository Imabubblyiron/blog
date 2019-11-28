# Android Architecture Components - LiveData


개인적으로 LiveData를 안지는 오래됐지만 사용하는게 아직까지도 어렵다고 느껴진다. 생명주기를 공유하기 때문에 이것에 대해 좀더 집중하며 공부할 필요가 있다고 생각한다. 우선 LiveData는 RxJava와 매우 유사한 부분을 가지고 있다. LiveData는 Activity나 Fragment, Service 등 안드로이드 컴포넌트의 의 생명 주기를 인식하고 생명주기가 활성화 되어있어야지만 Observer를 업데이트한다. 

구글에서 언급하는 LiveData의 장점과 개인적으로 느끼는 단점은 아래와 같다.

### 장점


1. UI와 데이터 상태의 일치 보장
LiveData는 Observer 패턴을 따릅니다. LiveData는 생명주기에 변경이 일어날 때마다 Observer 객체에 알려줍니다. 그리고 이 Observer 객체를 사용하면 데이터의 변화가 일어나는 곳마다 일일이 UI를 변경하는 코드를 넣을 필요 없이, 통합적이고 확실하게 데이터의 상태와 UI를 일치시킬 수 있다.

2. 메모리 누출 없음
Observer 객체는 Lifecycle 객체와 결합되어 있어서 컴포넌트가 Destroy 될 경우 메모리를 해제한다. 이 점으로 보아 Rx 라이브러리들과의 큰 차이가 있다.

3. 생명주기를 수동으로 다룰 필요가 없음
LiveData가 생명주기에 따른 Observing을 자동으로 관리를 해주기 때문에 UI 컴포넌트는 그저 관련 있는 데이터를 관찰하기만 하면 된다.

4. 화면 구성이 변경되어도 데이터를 유지함
AAC의 대표적인 특징이라고 할 수 있는 점이다. 예를 들면 기기를 회전하여 화면이 변경될 경우에도 LiveData가 최신 상태의 데이터를 즉시 받아온다.

5. 리소스 공유
DI를 이용하여 LiveData를 확장하는 클래스를 만들어 Singleton으로 관리 할 경우 앱 전체에서 사용 가능한 LiveData 객체를 만들 수 있고, 이를 통해 자원을 공유할 수 있습니다. 보통 우리가 사용하는 MVVM에서 너무 많이 사용된다. 



### 단점 


1. 아키텍처를 사용해보지 않은 사람들에게는 러닝커브가 상당한 개념이라고 생각한다. 필자는 MVVM패턴을 이용하여 LiveData를 자주 쓰는 편이긴하지만 여전히 공부해야할 부분이 많다고 생각한다. 

2. LiveData를 이용하다보면 바뀌면 안될 데이터까지도 같이 바뀌게 될 때를 마주하게 된다. 이럴때 디버깅을 통하여 해답을 찾아내기가 상당히 어렵다. 그만큼 확실히 이해하고 사용해야 할 개념인 것 같다.

아래는 필자가 진행중인 프로젝트에서 BaseViewModel을 상속한 ViewModel안에서 LiveData를 이용하는 코드이다. 

module.kt -

```kotlin

val appModule : Module = module {
    single { SchedulerProvider() }
}

val viewModel : Module = module {

    factory { SomethingAdapter() }
    viewModel { MainViewModel(get()) }
}

val module = listOf(appModule, viewModel)

```

MainActivity.kt -

```kotlin

class MainActivity : BaseActivity<ActivityMainBinding>(), Navigator {

    private val mainViewModel : MainViewModel by viewModel()
    override fun getLayoutId(): Int = R.layout.activity_main

    override fun onResume() {
        super.onResume()
        mainViewModel.setNavigator(this)
        binding.setVariable(BR.mainModel, mainViewModel)
    }

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
    }
}

```

MainViewModel.kt -

```kotlin

 class MainViewModel(
     scheduler : SchedulerProvider
 ) : BaseViewModel<Navigator>(scheduler) {

     var list = MutableLiveData<ArrayList<Something>>()

 }
 
```

필자는 Dependency Injection(Koin)을 이용하여 LiveData를 사용하는 MainViewModel 클래스를 싱글톤 패턴으로 관리하여 앱 전체에서 사용가능한 객체로 사용하고 있다.
