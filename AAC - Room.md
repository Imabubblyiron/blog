# Android Architecture Components - Room



우선 Jetpack AAC(Android Architecture Components 이하 AAC)를 이용하기 위해서는 기본 안드로이드 개발지식이 필요하다.
기존 안드로이드의 SQL Lite는 사용은 사용해본 사람은 알다시피 많이 불편하여 ORMLite, greenDAO 등과 같은 ORM(Object-relational mapping)을 이용하여 컨트롤 해왔다.
하지만 지금 설명할 Room은 구글에서만든 공식 ORM이라고 할 수 있으며 여러가지 강력한 기능들을 지원하고 있다.(SQLite도 이용이 가능하다.)


참고 : https://developer.android.com/topic/libraries/architecture/room?hl=ko


1) 구성요소


- Database

Database 접근 지점을 제공하며 DAO를 관리합니다.
Annotation내에 사용할 Entity목록을 작성한다.

- DAO(Data Access Object)

Database에 접근하는 메소드들을 포함하며 Annotation으로 관리된다.
LiveData를 이용하면 Observable query를 이용할 수 있다.

- Entity

스키마를 의미한다.


2)  앱 수준의 모듈에 의존성을 추가한다.


```kotlin

apply plugin: 'kotlin-kapt'

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'

    implementation "androidx.room:room-runtime:2.1.0-alpha06"
    annotationProcessor "androidx.room:room-compiler:2.1.0-alpha06"
    kapt "androidx.room:room-compiler:2.1.0-alpha06"

```


3) DataBase

Room을 공부할 정도의 안드로이드에 조금은 익숙한 사람이면 싱글턴 패턴에 아마 알고있을 거라고 생각한다.]
RoomDatabase는 매우 비싼 비용이 드는 작업이라고 한다. 그냥 떠올려봐도 그럴 것 같다고 생각이 든다. 그렇지만 우리가 DB에 접근할일은 분명히 많을 것이다. 그렇기에 DB를 사용하는 것이기 때문에.. 이런일이 있을때마다 매우 비싼 비용이 드는 RoomDatabase 인스턴스를 생성할 수는 없을 것이다. 그렇기에 싱글턴 패턴을 이용하여 Room을 만들어 보도록 하자. 실제로 공식 문서에서도 이렇게 권장하고 있다.

참고 : If your app runs in a single process, you should follow the singleton design pattern when instantiating an AppDatabaseobject. Each RoomDatabase instance is fairly expensive, and you rarely need access to multiple instances within a single process.


```kotlin

import androidx.annotation.NonNull;
import androidx.room.Database;
import androidx.room.DatabaseConfiguration;
import androidx.room.InvalidationTracker;
import androidx.room.RoomDatabase;
import androidx.sqlite.db.SupportSQLiteOpenHelper;

@Database(entities = {Todo.class} , version = 1, exportSchema = false)
public abstract class AppDatabase extends RoomDatabase {

    public abstract TodoDao todoDao();
}

```


4) Dao

@Dao 어노테이션을 활용할때는 interface나 abstract로 구현해야 한다.
query 구문 또한 Annotation으로 작성되어야 하며, 문자열 안에 있는 query의 자동완성을 지원한다.
아래 정의된 메소드는 일반적으로 데이터베이스를 다룰때 사용하던 메소드 들이다.


```kotlin 


import androidx.lifecycle.LiveData;
import androidx.room.Dao;
import androidx.room.Delete;
import androidx.room.Insert;
import androidx.room.Query;
import androidx.room.Update;

import java.util.List;

@Dao
public interface TodoDao {
    //실제로 Todo에 어떤 동작을 제공할지 정의하는것

    //LiveData를 씌워서 관찰 가능한 객체가됨
    @Query("SELECT * FROM Todo")
    LiveData<List<Todo>> getAll();

    @Insert
    void insert(Todo todo);

    @Update
    void update(Todo todo);

    @Delete
    void delete(Todo todo);
}


```


5) Entity

Entity의 이름을 지정 할 수도 있지만, 지정하지 않을경우 default값으로 클래스의 이름이 지정되며 대소문자를 구분하지 않는다. 
생각해보면 우리가 일반적으로 사용하는 데이터클래스가 알고보면 Entity와 같은 느낌이라고 직관적으로 이해가 될 것이다.


```kotlin

import androidx.room.Entity;
import androidx.room.PrimaryKey;

@Entity
public class Todo {

    //sql 기반이기 때문에 id값필요, primary키
    @PrimaryKey(autoGenerate = true) // 알아서 하나씩 증가하는 id값
    private int id;
    private String title;

    public Todo(String title) {
        this.title = title;
    }

    public int getId() {
        return id;
    }

    public void setId(int id) {
        this.id = id;
    }

    public String getTitle() {
        return title;
    }

    public void setTitle(String title) {
        this.title = title;
    }


    @Override
    public String toString() {
        return "Todo{" +
                "id=" + id +
                ", title='" + title + '\'' +
                '}';
    }
}

```

마지막으로 다시 말하지만 RoomDatabase는 매우 비싼 비용이 드는 작업이라고 했다. 이 말은 즉, 인스턴스를 생성하기 위해서는 당연히 작업 스레드에서 진행해야 한다. 물론 메인스레드에서 진행할 수 있게 직접 설정 할 수는 있지만 연결고리를 생각하는 습관은 중요하다.
