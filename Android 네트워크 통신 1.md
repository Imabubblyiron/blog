# Android 네트워크 통신 - HttpURLConnection


안드로이드에서 네트워크 통신을 위한 가장 일반적인 방법은 HttpURLConnection을 이용하는 것이였다. 우선 기본 네트워크 관련 라이브러리를 사용하여 설명한다.
안드로이드에서는 네트워크 통신은 반드시 작업 스레드에서 실행시켜야하는 제약이 있다. 우리가 가장 일반적으로 알고 있는 것은 비동기 처리 클래스는 AsyncTask이다 아래 예시에 간단한 설명이 있으니 이것들을 통해 이해해 보도록 하자.




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



2) MyHttpURLConnection를 만들어 HttpURLConnection을 수행하는 기능을 구현한다.

 ```kotlin
 
public class MyHttpURLConnection {

    public String request(String _url, ContentValues _params){

        HttpURLConnection connection = null;
        StringBuffer params = new StringBuffer();

        // 보낼 데이터가 없을때
        if (_params == null)
            params.append("");
            
        // 보낼 데이터가 있을때
        else {
            
            boolean isAnd = false;
            String key;
            String value;

            for(Map.Entry<String, Object> parameter : _params.valueSet()){
                key = parameter.getKey();
                value = parameter.getValue().toString();

                // 파라미터가 두개 이상일때
                if (isAnd)
                    params.append("&");

                params.append(key).append("=").append(value);

                if (!isAnd)
                    if (_params.size() >= 2)
                        isAnd = true;
            }
        }

        // HttpURLConnection를 이용하여 web에있는 데이터를 가져온다
         
        try{
            URL url = new URL(_url);
            connection = (HttpURLConnection) url.openConnection();

            // connection을 설정한다
            urlConn.setRequestMethod("POST"); // URL 메소드 -> POST.
            urlConn.setRequestProperty("Accept-Charset", "UTF-8");
            urlConn.setRequestProperty("Context_Type", "application/x-www-form-urlencoded;cahrset=UTF-8");

            // parameter를 전닳하고 데이터를 읽어온다
            String strParams = params.toString(); 
            OutputStream os = urlConn.getOutputStream();
            os.write(strParams.getBytes("UTF-8")); // 출력 스트림에 출력.
            os.flush(); 
            os.close(); 

            // 연결이 되었는지 체크한다
            if (connection.getResponseCode() != HttpURLConnection.HTTP_OK)
                return null;

            // 요청한 URL의 출력물을 BufferedReader로 받아서 결과물을 읽어온다
            BufferedReader reader = new BufferedReader(new InputStreamReader(connection.getInputStream(), "UTF-8"));

            String line;
            String page = "";

            while ((line = reader.readLine()) != null){
                page += line;
            }

            return page;

        } catch (MalformedURLException e) { // for URL.
            e.printStackTrace();
        } catch (IOException e) { // for openConnection().
            e.printStackTrace();
        } finally {
            if (urlConn != null)
                urlConn.disconnect();
        }

        return null;
    }
}

```



3)  MainActivity에서 네트워크 처리를 위해 비동기로 처리한다.


```kotlin

public class MainActivity extends AppCompatActivity {

    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        //자바를 사용할때면 매일 하는 작업들..
        textView = (TextView) findViewById(R.id.textView);
        String url = "http:www.sample.com";

        // AsyncTask를 이용하여 통신을 시작한다
        NetworkTask networkTask = new NetworkTask(url, null);
        networkTask.execute();
    }

    public class NetworkTask extends AsyncTask<Void, Void, String> {

        private String url;
        private ContentValues values;

        public NetworkTask(String url, ContentValues values) {

            this.url = url;
            this.values = values;
        }

        @Override
        protected String doInBackground(Void... params) {

            String result;
            MyHttpURLConnection myHttpURLConnection = new MyHttpURLConnection();
            result = myHttpURLConnection.request(url, values); 

            return result;
        }

        @Override
        protected void onPostExecute(String s) {
            super.onPostExecute(s);
            
            textView.setText(s);
        }
    }
}

```

여기까지 기본서에 나와있을만한 예제로 설명을 하였다. 일단 해보면 알다시피 코드는 간단한 편이지만 그 과정이 꽤나 복잡하다. 여기서는 네트워크 통신은 작업스레드 에서 하는 것정도만 얻어가면 성공이다. 왜냐하면 후에 배우게 될 Retrofit과 OkHttp을 이용한 예제를 이해하기 위해서는 가장 일반적이고 기본적인 예제부터 살펴볼 필요가 있다고 생각하기 때문에 포스팅 한 글이다.
