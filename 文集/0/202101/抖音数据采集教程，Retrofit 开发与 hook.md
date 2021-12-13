# 抖音数据采集教程，Retrofit 开发与 hook，抖音直播间数据抓取


# Retrofit
**文中所有 Retrofit 都是指的 Retrofit2**
> Retrofit其实我们可以理解为OkHttp的加强版，它也是一个网络加载框架。
> 底层是使用OKHttp封装的。准确来说,网络请求的工作本质上是OkHttp完成，

>**了解更多短视频直播数据采集分析接口请**[点击查看接口文档](https://docs.qq.com/doc/DU3RKUFVFdVhQbXlR) 

而 Retrofit 仅负责网络请求接口的封装。它的一个特点是包含了特别多注解，
方便简化你的代码量。
优点:

1. 超级解耦
1. 可以配置不同HttpClient来实现网络请求
1. 支持同步、异步和RxJava
1. 可以配置不同的反序列化工具来解析数据，如：json、xml
1. 请求速度快，使用非常方便灵活

# Retrofit 使用
配置依赖：
```
implementation 'com.squareup.retrofit2:retrofit:2.5.0'//Retrofit依赖
implementation 'com.squareup.retrofit2:converter-gson:2.5.0'//可选依赖，解析json字符所用
```

_
网络权限：
```
<uses-permission android:name="android.permission.INTERNET" />
```
_
步骤：

1. 定义接口类（封装URL地址和数据请求）
1. 实例化Retrofit
1. 通过Retrofit实例创建接口服务对象
1. 接口服务对象调用接口中的方法，获取Call对象
1. Call对象执行请求（异步，同步请求）

## 创建 Retrofit 实例

 
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612263581170-3a7f8127-5334-4455-b226-3bbe50d8c2a9.jpeg#align=left&display=inline&height=166&originHeight=166&originWidth=460&size=0&status=done&style=none&width=460)](https://static.zhangkunzhi.com/2020/11/05/16045436244620.jpg?x-oss-process=image/resize,h_400)

 
```
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://localhost:6666")
        .build();
```
_

## Get请求
```
//baseURL(从头开始到任意一个斜杠结束)
String baseURL="https://www.wanandroid.com/article/list/0/";
@GET("json?cid=60")
Call<ResponseBody> getData();
//获取Retrofit对象
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl(MyServer.baseURL)
        .build();
        
//通过Retrofit获取接口服务对象
MyServer server = retrofit.create(MyServer.class);
//接口对象调用其方法获取call对象
Call<ResponseBody> data = server.getData();
//和 okhttp 使用方法差不多，不同的是 android 系统回调方法执行在主线程
//call执行请求
data.enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        try {
            String json = response.body().string();
            Log.e(TAG, "onFailure: " +  response.body().string());
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {
        Log.e(TAG, "onFailure: " + t.getMessage());
    }
});
```
_

## Post请求
```
String URL = "http://apicloud.mob.com/appstore/health/";//必须以反斜杠结尾
public interface MyServer {
//POST("search?")    POST("search")相同
//@Field("key") String value post请求用来提交参数的
    //@FormUrlEncoded post请求提交form表单的时候如果有参数,需要填加这个注解,用来将提交的参数编码
    //post请求不提交参数,不要加,
    //如果有提交的参数,没有加@FormUrlEncoded
    //@Field和@FieldMap一样，@FieldMap只不过是把一个一个的参数,合成一个map
    @POST("search?")
    @FormUrlEncoded
    Call<ResponseBody> postData1(@Field("key") String appKey,@Field("name") String appKey);
    @POST("search")  
    @FormUrlEncoded
    Call<ResponseBody> postData2(@FieldMap Map<String,Object> map);
}
//POST异步
private void initPostEnqueue() {
    //1.创建Retrofit对象
    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(MyServer.URL)
            .build();
    //2.获取MyServer接口服务对象
    MyServer myServer = retrofit.create(MyServer.class);
    //3.获取Call对象
    //方式一
    Call<ResponseBody> call1 = myServer.postData1("908ca46881994ffaa6ca20b31755b675");
    //方式二
    //不用切换主线程了,因为Retrofit帮我们切过了
    //okHttpClient需要自己切换主线程
    Map<String,Object> map = new HashMap<>();
    map.put("appKey","908ca46881994ffaa6ca20b31755b675");
    Call<ResponseBody> call = myServer.postData2(map);
    //4.Call对象执行请求
    call.enqueue(new Callback<ResponseBody>() {
        @Override
        public void onResponse(Call<ResponseBody> call,Response<ResponseBody> response) {
            try {
                String result = response.body().string();
                Log.e("retrofit", "onResponse: "+result);
                tv.setText(result);//默认直接回调主线程
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        @Override
        public void onFailure(Call<ResponseBody> call, Throwable t) {
            Log.e("retrofit", "onFailure: "+t.getMessage());
        }
    });
}
```
_

# Retrofit 的注解

 
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612263581254-aa8ae0c5-19d0-4449-9b76-f59398fa1a94.jpeg#align=left&display=inline&height=400&originHeight=400&originWidth=704&size=0&status=done&style=none&width=704)](https://static.zhangkunzhi.com/2020/11/05/16045448596075.jpg?x-oss-process=image/resize,h_400)

 
```
//get
@GET("data/%E7%A6%8F%E5%88%A9/20/2")
Call<GetBean> getData();
//post
@POST("data/%E7%A6%8F%E5%88%A9/20/2")
Call<GetBean> getData2();
//field
@POST("register")
@FormUrlEncoded
Call<PostBean> getData3(@Field("username") String username,
                        @Field("password") String password,
                        @Field("phone") String phone,
                        @Field("verify") String verify);
//query
@GET("project/list/1/json")
Call<QueryBean> getData4(@Query("cid") int cid);
//fieldMap
@POST("register")
@FormUrlEncoded
Call<PostBean> getData5(@FieldMap Map<String, String> map);
//queryMap
@GET("project/list/1/json?")
Call<QueryBean> getData6(@QueryMap Map<String, Object> map);
//body
@POST("user/login")
Call<BodyBean> getData7(@Body RequestBody requestBody);
//path
@GET("data/%E7%A6%8F%E5%88%A9/20/{page}")
Call<GetBean> getData8(@Path("page") int page);
//url
@GET
Call<GetBean> getData9(@Url String url_query);
```
_

## 设置 headers

 
方式1:
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612263581405-5b06daa5-51ef-479b-8dcd-2ca4b68ad297.jpeg#align=left&display=inline&height=400&originHeight=400&originWidth=652&size=0&status=done&style=none&width=652)](https://static.zhangkunzhi.com/2020/11/05/16045452268291.jpg?x-oss-process=image/resize,h_400)
这是单个 headers 和 多个 headers 的普通添加方式
方式2:
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612263581273-5cf77844-8ff1-4ca7-8ea7-d463b06bc1cc.jpeg#align=left&display=inline&height=322&originHeight=322&originWidth=678&size=0&status=done&style=none&width=678)](https://static.zhangkunzhi.com/2020/11/05/16045453462587.jpg?x-oss-process=image/resize,h_400)
代码添加
方式3:
[![](https://cdn.nlark.com/yuque/0/2021/jpeg/97322/1612263581264-2529feef-8ada-4cde-b35b-2522e10a04ee.jpeg#align=left&display=inline&height=214&originHeight=214&originWidth=683&size=0&status=done&style=none&width=683)](https://static.zhangkunzhi.com/2020/11/05/16045454674736.jpg?x-oss-process=image/resize,h_400)

 

# 数据解析 (Converter)

- Retrofit 支持多种方式数据解析
- 需要在Geadle中添加依赖
```
Gson：implementation 'com.squareup.retrofit2:converter-gson:2.0.2'
Jackson：implementation 'com.squareup.retrofit2:converter-jackson:2.0.2'
```


对比 Okhttp

- 准确来说，Retrofit 是一个 RESTful 的 HTTP 网络请求框架的封装。
- 原因：网络请求的工作本质上是 OkHttp 完成，而 Retrofit 仅负责 网络请求接口的封装
- App应用程序通过 Retrofit 请求网络，实际上是使用 Retrofit 接口层封装请求参数、Header、Url 等信息，之后由 OkHttp 完成后续的请求操作
- 在服务端返回数据之后，OkHttp 将原始的结果交给 Retrofit，Retrofit根据用户的需求对结果进行解析

# Hook
> 因为他的下层是 okhttp， 所以 hook okhttp 那一套一样的可以 hook Retrofit



