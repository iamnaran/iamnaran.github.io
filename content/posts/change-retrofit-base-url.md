---
title: "Change Retrofit Base URL on Runtime"
date: 2023-02-27T13:06:38+08:00
description: "Switch between multiple base url like prod | dev in retrofit easily."
author: "Narayan Panthi"
tags: [ "Retrofit", "BaseURL", "Runtime","Android","HILT"]
theme: "light"
featured: true
cover: "https://miro.medium.com/v2/resize:fit:700/1*8IOK-CnOvbbyuYyCcwOPvA.png"
---


# Change Retrofit Base URL on Runtime.

Switch between multiple base url like prod | dev in retrofit easily.

![](https://miro.medium.com/v2/resize:fit:700/1*8IOK-CnOvbbyuYyCcwOPvA.png)

Hey there! Great to have you on board. In this article, we’ll walk through a quick and user-friendly technique to switch up the base URL in Retrofit.

# **Introduction**

**Retrofit** is a type-safe HTTP client for Android, Java and Kotlin developed by Square. The library provides a powerful framework for authenticating and interacting with APIs and sending network requests with  [OkHttp](http://square.github.io/okhttp/).

We instantiate the Retrofit instance by providing default base url.

return new Retrofit.Builder()
            .client(okHttpClient)
            .baseUrl(BuildConfig.PRODUCTION_BASE_URL)
            .addConverterFactory(-)
            .addCallAdapterFactory(-)
            .build();

**Problem Report**

Switching between URLs at runtime used to be a bit of a headache. Developers had to resort to using two separate Retrofit instances in their apps to deal with this challenge. To avoid the hassle of having multiple Retrofit instances, us developers came up with some cool tricks, like…

1.  With Retrofit Dynamic URL — Tedious
2.  Interceptor to change host URL with help of Shared Pref ✅
3.  Storing BASE_URL directly in Shared Pref —
4.  Reinit OkHttp /Retrofit—

And more..

_Here, We will implement approach no 2._

Let’s dive in by setting up dependencies.
```
ext {
      okHttpVersion = "4.8.1"
      retrofitVersion = "2.9.0"
      hiltVersion = "2.38.1"
}
// Implementation
// OkHttp
implementation "com.squareup.okhttp3:okhttp:$rootProject.okHttpVersion"
implementation "com.squareup.okhttp3:logging-interceptor:$rootProject.okHttpVersion"

// Retrofit2
implementation "com.squareup.retrofit2:retrofit:$rootProject.retrofitVersion"
implementation "com.squareup.retrofit2:converter-gson:$rootProject.retrofitVersion"
implementation "com.github.akarnokd:rxjava3-retrofit-adapter:3.0.0"
implementation "com.squareup.retrofit2:converter-protobuf:$rootProject.retrofitVersion"

// Hilt
implementation "com.google.dagger:hilt-android:$rootProject.hiltVersion"
annotationProcessor "com.google.dagger:hilt-android-compiler:$rootProject.hiltVersion"
annotationProcessor 'androidx.hilt:hilt-compiler:1.0.0'
```
> App.java
> with Dagger2 Hilt.

```
@HiltAndroidApp
public class App extends Application {

    @Override
    public void onCreate() {
        super.onCreate();
        Hawk.init(this).build();
        AppLog.init();
    }

    @Override
    protected void attachBaseContext(Context base) {
        super.attachBaseContext(base);
        MultiDex.install(this);
    }
}
```

Now, create a PreferenceHelper (SharedPrefManager) with two helper functions to read/write the status of current environment selection mode.
Store & Get Prod or Dev Environment  void  setProdEnvironmentStatus(boolean status); boolean  isProdEnvironment();
Then, create  **Interceptor**  to handle the switching environment work. 😮
For default base url, Retrofit is injected with DEVELOPMENT_BASE_URL assuming default selection for application.

```
@Provides
@Singleton
public Retrofit provideRetrofitClient(ProtoConverterFactory protoConverterFactory, RxJava3CallAdapterFactory rxJava3CallAdapterFactory, OkHttpClient okHttpClient) {return new Retrofit.Builder()
            .client(okHttpClient)
            .baseUrl(BuildConfig.DEVELOPMENT_BASE_URL)
            .addConverterFactory(protoConverterFactory)
            .addCallAdapterFactory(rxJava3CallAdapterFactory)
            .build();
}
```

**NOW**  provide  **HostSelectionInterceptor**  dependency to hilt in NetworkModule.java as,

```
@Provides
@Singleton
public HostSelectionInterceptor provideHostSelectionInterceptor(PreferenceHelper preferenceHelper) {
    return new HostSelectionInterceptor(preferenceHelper);
}
```

Then, provide this interceptor to our OkHttpClient Builder like below.

```
@Provides
@Singleton
public OkHttpClient provideOkHttpClient(@ApplicationContext Context context, HttpLoggingInterceptor httpLoggingInterceptor, HostSelectionInterceptor hostSelectionInterceptor) {

    long cacheSize = 5 * 1024 * 1024;
    Cache mCache = new Cache(context.getCacheDir(), cacheSize);
    if (BuildConfig.DEBUG) {
        return new OkHttpClient().newBuilder()
                .cache(mCache)
                .retryOnConnectionFailure(true)
                .connectTimeout(200, TimeUnit.SECONDS)
                .readTimeout(200, TimeUnit.SECONDS)
                .addInterceptor(httpLoggingInterceptor)
                .addInterceptor(hostSelectionInterceptor)
                .followRedirects(true)
                .followSslRedirects(true)
                .build();

    } else {
        return new OkHttpClient().newBuilder()
                .connectTimeout(200, TimeUnit.SECONDS)
                .readTimeout(200, TimeUnit.SECONDS)
                .retryOnConnectionFailure(true)
                .followRedirects(true)
                .followSslRedirects(true)
                .addInterceptor(hostSelectionInterceptor)
                .build();
    }

}
```

Now, We can  **INJECT**  **HostSelectionInterceptor**  in our viewmodel or activity by simply providing @**Inject**  annotation.

```
@Inject
    HostSelectionInterceptor hostSelectionInterceptor;
```

**Finally**, we can switch between these environment in just by changing selection mode status on shared Preference and recalling our  **setHostBaseUrl**  function in  **HostSelectionInterceptor**.

![](https://miro.medium.com/v2/resize:fit:500/0*xQnn65qsfYsvVNUm.gif)

Example on Spinner Switch for “DEV” & “PROD” environment.

```
private void doSetupHostSelection() {

    ArrayAdapter<String> arrayAdapter = new CustomSpinnerAdapter(this).spinnerAdapter();
    //Setting the ArrayAdapter data on the Spinner
    arrayAdapter.add("DEV");
    arrayAdapter.add("PROD");

   getViewDataBinding().spinnerHostSelection.setAdapter(arrayAdapter);

// selector
getViewDataBinding().spinnerHostSelection.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
        @Override
        public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {

            if (position == 0) {
                preferenceHelper.setProdEnvironmentStatus(false);
                hostSelectionInterceptor.setHostBaseUrl();

            } else {
                preferenceHelper.setProdEnvironmentStatus(true);
                hostSelectionInterceptor.setHostBaseUrl();
            }
        }

        @Override
        public void onNothingSelected(AdapterView<?> parent) {

        }
    });

}
```

This function  **setHostBaseUrl**  will do rest of the work.

```
public void setHostBaseUrl() {
    if (preferenceHelper.isProdEnvironment()) {
        this.host = HttpUrl.parse(BuildConfig.PRODUCTION_BASE_URL);
    } else {
        this.host = HttpUrl.parse(BuildConfig.DEVELOPMENT_BASE_URL);
    }
}
```

That’s all…. Thank you for reading…

> Note: The provided implementation is a demonstration illustrating how different URLs can be employed with Retrofit. The adoption of this approach depends on your unique requirements. Specifically, the application of these techniques varies based on how you intend to utilize and implement them in your code.

Grab the idea & Implement it in the best way!

For any confusion or suggestion fill in the comment section 🐧
