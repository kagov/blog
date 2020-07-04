---
title: Android Retrofit Configuration
date: 2017-06-30 21:42:07
tags:
  - android
  - retrofit
---
[Retrofit](http://square.github.io/retrofit/) is a simple yet powerful rest client for Android. In this post<!--more--> we are going to see a simple configuration for Retrofit2 containing a simple GET and a POST request. If you want to jump to the code right away it can be found [here](https://github.com/kagov/retrofit_config).

In order to make a standard REST call in android we need a Http client and [okhttp](http://square.github.io/okhttp/) is a famous library that provides that. Retrofit is built on top of okhttp both are provided by [Square](http://square.github.io/). Retrofit2 uses okhttp3 which also supports Http2.

## Lets begin

We will start by adding the key ingredients in our build.gradle file.

``` gradle
dependencies {
    // other dependencies

    // retrofit
    compile 'com.squareup.retrofit2:retrofit:2.3.0'
    // gson converter for json to pojo
    compile 'com.squareup.retrofit2:converter-gson:2.0.2'
    // for enabling request logs in the console
    compile 'com.squareup.okhttp3:logging-interceptor:3.6.0'
}
```

## Retrofit builder

The builder is the main object of retrofit and all the API calls happen throught that. The bulder method specifies the base url, the logging mechanism and the client used for the API call. The client in this case is okhttp and it has additional configurations like logging, socket timeout etc. The builder method will loek something like this.

{{< codeblock "archives.java" "java"  >}}
public static Retrofit builder(final String baseUrl) {
       return new Retrofit.Builder()
               .baseUrl(baseUrl)
               .addConverterFactory(GsonConverterFactory.create())
               .client(getClient())
               .build();
   }
   private static OkHttpClient getClient() {
        HttpLoggingInterceptor logging = new HttpLoggingInterceptor();
        logging.setLevel(HttpLoggingInterceptor.Level.BODY);
        return new OkHttpClient()
                .newBuilder()
                .addInterceptor(logging)
                .connectTimeout(5, TimeUnit.MINUTES).build();
    }
{{< /codeblock >}}
The getClient method returns a new instance of Okhttp client with a connection timeout if 5 minutes.

## The services

Retrofit requires you to write an interface that will contain abstract definitions of all your API calls and the library provides an implementation at runtime. FOr this example the interface will contain two methods a GET and a POST call.

{{< codeblock "archives.java" "java"  >}}
  @GET("/api/users")
  Call<GetModel> getUsers(@Query("page") String page);
  @POST("/api/users")
  Call<PostModel> createUser(@Body PostModel request);
{{< /codeblock >}}

This is an asynchronous call which returns a Call object of generic type. In the last step we will see how exactly to call this method.

## The Call

Inside your activity or fragment or any other class where you want to call the API you can create an instance of the interface you created in the prevous step and call the required method. It woulf something like this.

{{< codeblock "archives.java" "java"  >}}
// base url for illustration purposes only
String BASE_URL = "https://reqres.in";
ApiService service = ApiBuilder.builder(BASE_URL).create(ApiService.class);
    Call<GetModel> response = service.getUsers("2");
    response.enqueue(new Callback<GetModel>() {
         @Override
         public void onResponse(Call<GetModel> call, Response<GetModel> response) {
           getRes.setText(response.body().toString());
         }
         @Override
         public void onFailure(Call<GetModel> call, Throwable t) {
         }
    });
{{< /codeblock >}}
Finally you can create a simple layout file with a couple of buttons and textviews for visualizing the response.

{{< codeblock "view.xml" "xml"  >}}
<Button
    android:id="@+id/get_btn"
    android:layout_width="wrap_content"
    android:layout_margin="10dp"
    android:text="GET"
    android:layout_height="wrap_content"/>
<TextView
    android:id="@+id/get_res"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:layout_margin="15dp"/>
{{< /codeblock >}}

In order for all of this to work make sure you have mentioned the internet permisions in the manifest file. I believe that this post would've given you a basic idea about Retrofit2 and its usage in any project. In another post we will see how to send files to the server in the form of multipart data.
