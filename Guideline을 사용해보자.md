# Guideline을 사용해보자


이번 글에서는 안드로이드에서 특정 개체의 크기를, 현재 보고 있는 액티비티 화면의 비율로 조절하는 방법에 대해서 설명할 것이다. 안드로이드 OS를 기반으로 만들어진 스마트폰은 종류가 꽤나 많다. 종류가 많은 만큼 기기마다 크기가 화면 비율이 다를 수 있다. 그래서 특정 픽셀값의 절대치로 개체의 크기를 지정할 경우 보는 사람마다 크기가 달라보인다는 한계점이 있다. 보고있는 화면의 특정 비율, 예를 들어 화면 30% 만큼만 개체를 띄우고 싶을 경우에 아래와 같은 방법 1)을 사용하면 된다. 또한 레이아웃 내의 뷰들은 트리구조로 구성되어 있는데 이 구조가 복잡함에도 불구하고 여러가지들의 뷰들을 어느 기점을 기준을 잡고 구성하고 싶다면 아래와 같은 방법 2)와 같이 구성할 수 있다. 


1) 화면의 비율을 이용한 예제


```kotlin

<android.support.constraint.ConstraintLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent">
 
    <android.support.constraint.Guideline
        android:id="@+id/guideline"
        android:layout_width="wrap_content"
        android:layout_height="1dp"
        android:orientation="horizontal"
        app:layout_constraintGuide_percent="0.5" />
 
    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="0dp"
        app:layout_constraintBottom_toTopOf="@+id/guideline"
        app:layout_constraintTop_toTopOf="parent">
 
        <ImageView
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:scaleType="fitXY"
            android:src="@drawable/default_image" />
 
    </LinearLayout>
 
 
</android.support.constraint.ConstraintLayout>

```

2) 복잡한 트리 구조속의 여러가지 뷰들에게 기준을 적용시키고 싶을때


 ```kotlin

<androidx.core.widget.NestedScrollView
android:layout_width="match_parent"
android:layout_height="0dp"
app:layout_constraintStart_toStartOf="parent"
app:layout_constraintTop_toTopOf="parent"
app:layout_constraintBottom_toTopOf="@id/register_complete"
app:layout_constraintEnd_toEndOf="parent">

<androidx.constraintlayout.widget.ConstraintLayout
    android:id="@+id/_page_group"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#FFFFFF"
    android:layout_marginBottom="24dp">

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/_start_guide"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:orientation="vertical"
        app:layout_constraintGuide_begin="24dp" />

    <androidx.constraintlayout.widget.Guideline
        android:id="@+id/_end_guide"
        android:layout_width="0dp"
        android:layout_height="0dp"
        android:orientation="vertical"
        app:layout_constraintGuide_end="24dp" />

    <androidx.constraintlayout.widget.ConstraintLayout
        android:id="@+id/_register_group"
        android:layout_width="0dp"
        android:layout_height="wrap_content"
        app:layout_constraintStart_toEndOf="@id/_start_guide"
        app:layout_constraintEnd_toStartOf="@id/_end_guide"
        app:layout_constraintTop_toBottomOf="@id/_logo_bg"
        android:layout_marginTop="32dp">
        
        ...
```

위의 두가지 예제는 어떻게보면 비슷한 의미의 예제가 될 수 있겠다. 그리고 코드를 보면 Guideline의 사용 방법에 대하여 접근하기는 쉬울 것이라고 생각한다. 개인적으로 느낀점 중 하나는 복잡해지는 코드안에서도 유연하게 기준을 잡을 수 있고, Guideline을 사용하면서 코드가 조금 더 깔끔해진 느낌도 있다.
