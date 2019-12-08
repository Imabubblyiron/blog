# 아키텍처 MVP와 MVVM



## MVP pattern
‘MVP 패턴’은 Model, View, Presenter의 앞글자를 따서 이름이 지어졌다. 이 패턴의 핵심 아이디어는 사용자 인터페이스(View)와 비즈니스 로직(Model)을 분 리하고, 서로간에 상호작용을 다른 객체(Presenter)에 위임해 서로의 영향을 최소화하는 것에 있다. 아래는 MVP의 각 component에 대한 정의이다.

### Model
내부적으로 쓰이는 데이터를 저장하고, 처리하는 역할을 합니다. 흔히 ‘비즈니스 로직’ 이라고 부르는 부분이다. 그리 View, Presenter등 다른 어떤 요소에도 의존적이지 않은 독립적인 영역이다.

### View
사용자 인터페이스(User Interface-UI)라 불리는 영역이다. 안드로이드에서는 Activity, Fragment를 대표적이 예로 볼 수 있다. Model에서 처리된 데이터를 Presenter를 통해 받아서 사용자에게 보여주며, 사용자 액션 및 Activity lifecycle 변화를 감지해서 Presenter에 보내는 역할을 한다. Presenter를 이용해 데이터를 주고받기 때문에 Presenter에 의존적이다.

### Presenter
Model과 View사이의 매개체이다. View에서 캐치한 유저 액션을 전달 받아서 Model의 로직을 호출하거나, Model의 로직으로부터 나온 결과를 전달 받아서 View에 보 내 UI변경을 야기하는 등 둘 사이의 소통의 역할을 하고, Model, View 모두에 의존적이다.

정리 - 
‘MVP패턴’을 이용해서 이와 같이 Model과 View간의 결합도를 낮추면, 새로운 기능을 추가하거나 변경할 필요가 있을 때 관련된 부분만 수정하면 되기 때문에 확장성이 좋 아지며, 테스트 코드를 작성하기 편리해지기 때문에 더 안전한 코드 작업이 가능해진다.

![](https://miro.medium.com/max/3402/1*FyYlXctoS7DUe3dZPur0Yw.png)


## MVVM pattern
'MVVM 패턴'은 Model-View-ViewModel의 약자이다. Model은 UI에 표시될 데이터 와 비즈니스 로직을 담당하고 View는 UI를 의미하며 ViewModel은 이벤트 처리 나, Model과의 인터랙션 등을 담당한다. 아래는 MVVM의 각 component에 대한 정의이다.

### Model
DataModel 이라고도 불린다. ViewModel 에서 데이터를 가져갈 수 있게 데이터를 준비하고 이벤트를 보낸다.

### View
Model의 존재를 알지 못한다. UI의 업데이트를 위해 ViewModel과 바인딩이 하게된다. 즉 ViewModel의 상태가 변경되면 구독하고있는 view가 그 이벤트를 받아 UI를 갱신한다.

### ViewModel
View 와 Model 사이의 글루코드. Model에서 제공받은 데이터를 UI에서 필요한 정보로 가공한뒤 View가 가져갈 수 있도록 데이터 변경에 대한 이벤트를 보내주는 역할. View의 존재를 알지 못한다.
하나의 ViewModel은 여러개의 View 를 가질 수 있다. (MVP에서는 Presenter와 View가 one to one을 유지해야 하는데, MVVM에서는 many to one이다) View에 영향을 끼칠 수 있는 Model의 상태도 관리한다. View 또는 액티비티 Context에 대한 레퍼런스를 가져서는 안된다. View는 ViewModel의 레퍼런스를 가지지만, ViewModel은 View에 대한 정보가 전혀 없어야 한다.

정리 - 
View, Model, ViewModel은 서로의 존재를 알지 못해야 한다. ViewModel에서 View에게 갱신을 알리기 위해서는, Observable 또는 DataBinding을 사용해야 한다. 즉 ViewModel의 데이터가 수정되더라도, View에서는 수정할 것이 없도록 둘을 분리해야 한다. Model에는 데이터와 관련한 소스만, View는 화면과 관련한 소스만 넣는다. View에서 만약 데이터에 의한 변경들이 있으면 ViewModel의 옵저버 등을 통해서 값이 변경되었음을 알고 변경할 수 있다.

![](https://miro.medium.com/max/3068/1*tSHvX51lF0BwYFbmFaobpg.png)

자세한 내용은 https://docs.microsoft.com/en-us/xamarin/xamarin-forms/enterprise-application-patterns/mvvm 참고한다.
