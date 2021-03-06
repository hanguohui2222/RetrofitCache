# RetrofitCache

[![](https://img.shields.io/badge/jcenter-retrofitcache_1.0.4-519dd9.svg)](https://bintray.com/yale8848/maven/retrofitcache/1.0.4)
[![](https://img.shields.io/badge/jcenter-retrofitcacherx2_1.0.4-519dd9.svg)](https://bintray.com/yale8848/maven/retrofitcacherx2/1.0.4)

RetrofitCache let retrofit2,okhttp3,rx add cache so easy. You can config cache strategy with each api by annotation.Also you can config mock data with each api easily.

## Why use?

- Api server do not config http cache strategy
- Third part lib config cache strategy not easy
- Add mock data to each api very easily

## Demo

- Not have cache

```
@GET("users")
Observable<HttpResult> test();
```

- Cache time 20 seconds

 ```
@Cache(time = 20)
@GET("users")
Observable<HttpResult> test();
 ```

- Cache time 20 minutes

 ```
@Cache(time = 20,timeUnit = TimeUnit.MINUTES)
@GET("users")
Observable<HttpResult> test();
 ```

- Default cache time 0 seconds

 ```
@Cache()
@GET("users")
Observable<HttpResult> test();
 ```

- Add mock data(if use value,assets,url same time, it will deal by this sort )

 ```
@Mock(value = "{\"data\":\"mockdata\"}") //mock ram data
@GET("users")
Observable<HttpResult> test();
 ```

 ```
@Mock(assets = "mock/mock.json") //mock data from assets
@GET("users")
Observable<HttpResult> test();
 ```

 ```
@Mock(url = "http://url.com/test") //mock data from new url
@GET("users")
Observable<HttpResult> test();
  ```


> Cache just with http Get method

## How to use

 - Add jcenter lib,notice chose different with you project

 ```
compile 'ren.yale.android:retrofitcachelib:1.0.4'   //retrofit2+okhttp3+rxjava1
compile 'ren.yale.android:retrofitcachelibrx2:1.0.4'   //retrofit2+okhttp3+rxjava2
 ```

 - Init in Android Application

 ```
RetrofitCache.getInatance().init(this);
 ```

Also can modify default config, default value time=0,timeUnit = TimeUnit.SECONDS

```
RetrofitCache.getInatance().init(this).setDefaultTimeUnit(TimeUnit.MINUTES).setDefaultTime(1);
```

 - OkHttpClient config cache dir

 ```
okhttp3.OkHttpClient.Builder clientBuilder=new okhttp3.OkHttpClient.Builder();
...
int cacheSize = 200 * 1024 * 1024;
File cacheDirectory = new File(mContext.getCacheDir(), "httpcache");
Cache cache = new Cache(cacheDirectory, cacheSize);
OkHttpClient client =  clientBuilder.cache(cache).build();
...

 ```

- okhttp add Interceptor

 ```
okhttp3.OkHttpClient.Builder clientBuilder=new okhttp3.OkHttpClient.Builder();
...
clientBuilder.addInterceptor(new CacheForceInterceptorNoNet());
clientBuilder.addNetworkInterceptor(new CacheInterceptorOnNet());
...

 ```


- Add retrofit object

```
Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(url)
                .client(getOkHttpClient())
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
RetrofitCache.getInatance().addRetrofit(retrofit);
```
-  **Add rx Observable compose**

```
api.test().compose(CacheTransformer.emptyTransformer())...

```


## Further


- setCacheInterceptorListener to set whether can cache by each api

```
RetrofitCache.getInatance().setCacheInterceptorListener(
                new CacheInterceptorListener() {
            @Override
            public boolean canCache(Request request,Response response) {
                String res = "";
                try {
                    res = response.body().string();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return true;
            }
});

```


- Enable mock data , if your release api ok,you can set false

```
RetrofitCache.getInatance().enableMock(false);
```


## Proguard

```
-keepattributes *Annotation*,InnerClasses

-dontwarn ren.yale.android.retrofitcachelib.**
-keep class ren.yale.android.retrofitcachelib.** { *; }

#retrofit2
-dontwarn retrofit2.**
-keep class retrofit2.** { *; }
-keepattributes Signature
-keepattributes Exceptions

-dontwarn org.robovm.**
-keep class org.robovm.** { *; }

#okhttp3
-dontwarn com.squareup.okhttp3.**
-keep class com.squareup.okhttp3.** { *;}
-keep class okhttp3.** { *;}
-keep class okio.** { *;}
-dontwarn sun.security.**
-keep class sun.security.** { *;}
-dontwarn okio.**
-dontwarn okhttp3.**

#rxjava
-dontwarn rx.**
-keep class rx.** { *; }

-dontwarn sun.misc.**
-keepclassmembers class rx.internal.util.unsafe.*ArrayQueue*Field* {
 long producerIndex;
 long consumerIndex;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueProducerNodeRef {
 rx.internal.util.atomic.LinkedQueueNode producerNode;
}
-keepclassmembers class rx.internal.util.unsafe.BaseLinkedQueueConsumerNodeRef {
 rx.internal.util.atomic.LinkedQueueNode consumerNode;
}

```

## Issues

[Issues](https://github.com/yale8848/RetrofitCache/issues)

## How CONTRIBUTING

[CONTRIBUTING](CONTRIBUTING.md)

## License

```
MIT License

Copyright (c) 2017 Yale

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
```