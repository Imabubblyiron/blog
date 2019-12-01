# DataBinding 사용


우선 안드로이드 스튜디오 안에서 DataBinding 라이브러리를 사용하도록 준비하는 방법을 알아보자.

 1) 빌드 환경
 
 DataBinding을 시작하려면 Android SDK Manager의 Support storage에서 라이브러리를 다운로드 해야한다. 자세한 내용은 공식 문서에서 확인할 수 있다.
 
 ```kotlin

android {
    compileSdkVersion 29
    buildToolsVersion "29.0.0"
    defaultConfig {
        applicationId "com.dfang.playfang"
        minSdkVersion 23
        targetSdkVersion 29
        versionCode 10
        versionName "1.3"
        testInstrumentationRunner "androidx.test.runner.AndroidJUnitRunner"
    }
    
    dataBinding {
        enabled = true
    }
}

```

위의 코드와 같이 앱 모듈의 build.gradle에 DataBinding을 위한 셋팅을 진행한다.
 
 2) 적용하기
 
 아래 xml 코드는 DataBinding 라이브러리를 사용하지 않았을때와 사용했을때의 차이를 보여준다.
 
- 적용전 xml 코드

```kotlin

<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="start"
        />
</android.support.constraint.ConstraintLayout>

```

- 적용후 xml 코드 : DataBinding을 사용하려면 레이아웃의 루트에 <layout></layout>을 추가하여 감싸준다.

```kotlin

<?xml version="1.0" encoding="utf-8"?>
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <data>

        <variable
            name="activity"
            type="{packageName}.MainActivity" />
            
    </data>

    <android.support.constraint.ConstraintLayout
        android:layout_width="match_parent"
        android:layout_height="match_parent">

        <Button
            android:id="@+id/button"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:text="@{activity.sample}"
            />
            
    </android.support.constraint.ConstraintLayout>
    
</layout>

```

- 적용후 코틀린 코드

```kotlin

@LayoutRes
protected abstract fun getLayoutId() : Int

override fun onCreate(savedInstanceState: Bundle?) {
    super.onCreate(savedInstanceState)
    binding = DataBindingUtil.setContentView(this, getLayoutId())
    initialize()
}

```

위의 코드를 보면 적용후 DataBindingUtil클래스를 이용하여 binding 인스턴스를 만들어낸다. 이 binding 인스턴스를 이용하여 레이아웃 내의 리소스들에 쉽게 접근할 수 있게 된다.


 3) Android Studio에서의 DataBinding 지원
 
 Android Studio에서는 DataBinding과 관련된 여러 기능들을 지원한다.
 
 - 구문에 강조표시 한다.
 
 - 표현식 언어에 구문 오류 플래그를 지정한다.
 
 - XML 코드를 완성한다.
 
 - 탐색 및 빠른 문서를 포함하는 참조
 
 안드로이드 스튜디오 오른쪽 상단에 보면 Layout Editor의 Preview창이 있을 것이다. 여기서 DataBinding의 기본 값이 표시되게 되는데 아래 예시를 들어 설명한다.
 
```kotlin

    <EditText
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:text="@{user.firstName, default=default_value}"
    />

```

위의 text에 @{}구문을 이용하여 default value를 설정해 준 코드이다. 프로젝트의 디자인 단계에서만 default value를 사용하고 싶으면 원래 하던데로 app:tools 속성을 이용하면 된다. 

참고 : https://developer.android.com/topic/libraries/data-binding
