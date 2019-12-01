# Android 네트워크 통신 - Retrofit


드디어 등장하는 Retrofit 라이브러리, 아마도 프로젝트를 조금 진행하다보면 서버와 통신하는 앱이라면 필수적으로 사용할 수도 있는 라이브러리라고 말할 수 있을 것이다. 보통 서버와 통신할 때는, 새로운 스레드를 만들어서 휴대폰 UI Thread와 분리하여 정보를 주고 받는다. 
전 포스팅에서 비동기 처리를 하기위해 우리는 AsyncTask와 Thread를 사용하였다. 하지만 네트워크 처리가 많아지면 이 번잡한 것들을 계속해서 사용해야하는가? 라고 생각해볼 수 있다. 
사람들은 당연히 이 문제를 해결하기위해 다른 방법들을 제시하여 주였고, 그것들중 하나가 바로 Retrofit이다. 
Retrofit 라이브러리를 사용하면 자체적으로 알아서 처리해주기 때문에 아주 편리하고, 또 사용이 굉장히 쉽다. 실제로 구현해보면 생각보다 어렵진 않고, 라이브러리 공식 문서에 사용법들이 잘 나와있다. 




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




2) 앱 수준의 Gradle에 의존성을 추가해준다.

```kotlin

dependencies {
    implementation fileTree(dir: 'libs', include: ['*.jar'])
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk7:$kotlin_version"
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'androidx.test:runner:1.2.0'  
    androidTestImplementation 'androidx.test.espresso:espresso-core:3.2.0'

    //okhttp
    implementation 'com.squareup.retrofit2:retrofit:2.5.0'
    implementation 'com.squareup.retrofit2:converter-gson:2.5.0'

```
    
여기에서 추가적으로 설명할 것은 Gson 라이브러리이다. OkHttp를 사용하다보면 Json으로 파싱을 하는데 Google에서 더편하게 사용하라고 Gson을 구현해 주었다. 생각보다 어렵지 않은 부분이라 검색으로도 충분히 정보를 얻을 수 있다.




3) 자바를 사용하는 사람들에겐 쉬운 작업인 모델클래스를 만들어준다.


 ```kotlin

  package com.example.retrofitactivity;

  public class User {

      public String userId;
      public String id;
      public String title;
      public String body;

      public User(String userId, String id, String title, String body) {
          this.userId = userId;
          this.id = id;
          this.title = title;
          this.body = body;
      }

      public String getUserId() {
          return userId;
      }

      public void setUserId(String userId) {
          this.userId = userId;
      }

      public String getId() {
          return id;
      }

      public void setId(String id) {
          this.id = id;
      }

      public String getTitle() {
          return title;
      }

      public void setTitle(String title) {
          this.title = title;
      }

      public String getBody() {
          return body;
      }

      public void setBody(String body) {
          this.body = body;
      }
  }
  
```



4)  이 부분에는 GET,POST,PUT,DELETE같은 필요한 함수를 선언하는 부분이다. 


- GetApi.java


```kotlin

public interface GetApi {

     public static final String BASE_URL = "http://jsonplaceholder.typicode.com";

     @GET("posts/{id}")
     Call<User> getDataUser(@Path("id") String id);

     @GET("/posts")
     Call<List<User>> getDataListUser(@Query("userId") String userId);

     @FormUrlEncoded
     @POST("/posts")
     Call<User> postData(@FieldMap HashMap<String, Object> param);

     //요청된 자원을 수정한다, PUT의 경우 내용 전체를 갱신하는 것을 의미한다.
     @PUT("/posts/{id}")
     Call<User> putData(@Path("id") String id, @Body User user);


     //요청된 자원을 수정할때 사용한다, PATCH는 해당 자원의 일부를 교채한다.
     @PATCH("/posts/{id}")
     Call<User> patchData(@Path("id") String id, @Body User user);

     // Call<ResponseBody> 에서 ResponseBody는 통신을 통해 되돌려 받는 값이 없을때 사용함.
     @DELETE("/posts/1")
     Call<ResponseBody> deleteData();
 }
 
```



5) GetApi.java 파일에서 만든 메소드들을 자세하게 구현하는 부분이다. 필자는 단지 결과를 확인해보고자 executeRetrofit() 메소드에 모든 메소드를 다 실행시켜 주었다. 이런식으로 코드를 짜는건 좋지 않은 방법이라는 걸 모두 알테니 그냥 넘어가겠다.

- Retrofit.java

```kotlin

public class Retrofit {

    private static final String TAG = "Retrofit";
    private retrofit2.Retrofit retrofit;

    public void executeRetrofit(final TextView textView) {
        retrofit = new retrofit2.Retrofit.Builder()
                .baseUrl(GetApi.BASE_URL)
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        GetApi getApi = retrofit.create(GetApi.class);

        Call<User> call = getApi.getDataUser("10");
        call.enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                if (response.isSuccessful()) {
                    User user = response.body();
                    if (user != null) {
                        logBody("Executed getDataUseR()", user);
                    }
                }
            }


            @Override
            public void onFailure(Call<User> call, Throwable t) {
            }
        });

        Call<List<User>> listCall = getApi.getDataListUser("10");
        listCall.enqueue(new Callback<List<User>>() {
            @Override
            public void onResponse(Call<List<User>> call, Response<List<User>> response) {
                List<User> users = response.body();

                for(User user : users) {
                    textView.append("Title : " +user.title +"\n");
                }
            }

            @Override
            public void onFailure(Call<List<User>> call, Throwable t) {
            }
        });

        Call<User> call1 = getApi.putData("10", new User("200","10","title","body"));
        call1.enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                if(response.isSuccessful()) {
                    User user = response.body();
                    if (user != null) {
                        logBody("Executed putData()",user);
                    }
                }
            }

            @Override
            public void onFailure(Call<User> call, Throwable t) {
            }
        });


        Call<User> call2 = getApi.patchData("1", new User("1","23","title","body"));
        call2.enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                if(response.isSuccessful()) {
                    User user = response.body();
                    if(user!=null) {
                        logBody("Executed patchData()", user);
                    }
                }
            }

            @Override
            public void onFailure(Call<User> call, Throwable t) {

            }
        });


        HashMap<String, Object> input = new HashMap<>();
        input.put("userId", 1);
        input.put("title", "Title created");
        input.put("body", "Body created");
        Call<User> call3 = getApi.postData(input);
        call3.enqueue(new Callback<User>() {
            @Override
            public void onResponse(Call<User> call, Response<User> response) {
                if(response.isSuccessful()) {
                    User user = response.body();
                    if(user!=null) {
                        logBody("Executed postData()", user);
                    }
                }
            }

            @Override
            public void onFailure(Call<User> call, Throwable t) {

            }
        });


        Call<ResponseBody> call4 = getApi.deleteData();
        call4.enqueue(new Callback<ResponseBody>() {
            @Override
            public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
                if(response.isSuccessful()) {
                    ResponseBody body = response.body();
                    if( body != null) {
                        logResponseBody("Executed deleteData()", body);
                    }
                }
            }

            @Override
            public void onFailure(Call<ResponseBody> call, Throwable t) {

            }
        });
    }

    public void logBody(String string, User user) {
        Log.e(TAG , string);
        Log.e(TAG,user.getId());
        Log.e(TAG,user.getUserId());
        Log.e(TAG,user.getTitle());
        Log.e(TAG,user.getBody());
        Log.e(TAG,"====================");
    }

    public void logResponseBody(String string, ResponseBody body) {
        Log.e(TAG, string);
        Log.e(TAG, body.toString());
        Log.e(TAG,"====================");
    }
}

```



6) Retrofit의 편리한점

Retrofit 은 파라메터, 쿼리, 헤더 등의 매핑작업, 결과 처리작업 등 반복되는 작업들을 편리 하게 처리할 수 있게끔 구현된 라이브러리이다. 
Retrofit을 사용하지 않고 okhttp만을 이용해서도 작업이 가능하나, Url 매핑, 파라메터 매핑등의 귀찮은 작업 들이 많아지기 때문에, okhttp와 함께 사용한다.
