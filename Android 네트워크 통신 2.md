# Android 네트워크 통신 - OkHttp


Http/1.1 통신의 문제점 중 하나는 한 커넥션에 단 한번의 요청과 응답만을 허용한다는 것이다. 여러 요청을 병렬로 처리하려면 브라우저나 다른 클라이언트에서도 여러개의 소켓을 열어야 한다. 이것은 서버 단에서는 심각한 문제였기 때문에 이 문제를 해결하기 위해 HTTP프로토콜을 개선하는 작업을 하기 시작하였다. 후에 SPDY라는 프로토콜이 구현되었고, 이 SPDY는 HTTP/2의 기반이 되었다. 우리가 여기서 알아볼 OkHttp는 Spdy와 HTTP/2를 모두 지원하는 라이브러리로 자바에서 나름대로 편리하게 사용할 수 있다. 아래 예시를 들어 설명한다.



1) 먼저, 인터넷 환경 설정이 필요하다.

```kotlin

<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.sample">

    <uses-permission android:name="android.permission.INTERNET" />

        ...
        
    </application>


</manifest>

```

우선 인터넷사용을 위해서는 AndroidManifest.xml파일에 INTERNET 권한을 추가하도록 한다.



2) 먼저, 앱 수준의 Gradle에 의존성을 추가해준다.

```kotlin

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'  
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'

    //okhttp
    implementation 'com.squareup.okhttp3:okhttp:3.12.0'

```
    
의존성을 추가할때 직접 이런 방식으로 추가해도 되지만, 안드로이드 스튜디오를 활용해서도 의존성을 쉽게 추가할 수 있다. [File] -> [Project Structure]을 선택해서 열게되면 [Modules -> app]을 선택 하고 상단 탭에서 [Dependencies]를 선택한다. 그러고 +모양 단추를 누르고 okhttp정도로만 검색하여 검색 결과가 뜨면 선택한 후 OK버튼을 눌러서 쉽게 추가할 수도 있다.



3) 메소드 getInstance()를 통해서 내부에서 단 한번만 객체를 생성하도록 해서 싱글톤 패턴을 적용했다.


 ```kotlin

  public class HttpConnection {

        private OkHttpClient client;
        private static HttpConnection instance = new HttpConnection();
        
        public static HttpConnection getInstance() {
            return instance;
        }

        private HttpConnection() {
            this.client = new OkHttpClient(); 
        }

        public void request(String param1, String param2, Callback callback) {
            RequestBody body = new FormBody.Builder()
                .add("param1", param1)
                .add("param2", param2)
                .build();
            Request request = new Request.Builder()
                .url("http://www.sample.com/send")
                .post(body)
                .build();
            client.newCall(request).enqueue(callback);
        }

    }


```


4)  이번에는 SampleActivity에서 비동기 처리를 Asynctask 클래스가 아닌 스레드를 이용하여 진행한다.


```kotlin

 public class SampleActivity extends AppCompatActivity {

       private static final String TAG = "SampleActivity";
       private HttpConnection connection = HttpConnection.getInstance();

       @Override
       protected void onCreate(Bundle savedInstanceState) {
           super.onCreate(savedInstanceState);
           setContentView(R.layout.activity_test);

           send(); // 서버로 데이터를 전송하는 메소드
       }

       private void send() {
           //스레드를 이용하여 작업스레드에서 작업을 처리해주도록 한다.
           new Thread() {
               public void run() {
                   connection.request("data1","data2", callback);
               }
           }.start();;
       }

       private final Callback callback = new Callback() {
       
           @Override
           public void onFailure(Call call, IOException e) {
               Log.d(TAG, "exception : "+e.getMessage());
           }
           
           @Override
           public void onResponse(Call call, Response response) throws IOException {
               String body = response.body().string();
               Log.d(TAG, "result : "+body);
           }
       };
   }

```

