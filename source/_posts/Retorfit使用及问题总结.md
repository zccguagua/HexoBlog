---
title: Retorfit使用及问题总结
date: 2019-07-15 00:11:21
tags:
  - Android
categories:
  - Android
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/watermelon.jpg "Image")

### 1.创建请求基类方便处理返回结果
```
Retorfit 网络请求基类
public class BaseResponse<T> {
    public int status;
    public String msg;
    public T body;
    public boolean isSuccess(){
        return status == 200;
    }
    @Override
    public String toString() {
        final StringBuilder sb = new StringBuilder("{");
        sb.append("\"body\":")
                .append(body);
        sb.append('}');
        return sb.toString();
    }
}
```


### 2.请求方法
URL请求
```
 @GET
 Observable<WeatherApiBean> sevenDaysWeather(@Url String url);
```

GET 请求
```

 @GET("api/v1.0/version")
 Observable<BaseResponse<Version>> getTerminalVersion(@Header("token") String token
 , @Query("type") int type);
```

POST请求
```
 @FormUrlEncoded
 @POST("api/v1.0/terminal/login")
 Observable<BaseResponse<Map<String, Object>>> login(@Field("deviceType") String deviceType);
```
注意  @FormUrlEncoded不可和@Body连用

DELETE 请求
```
  @Headers("Content-Type:application/x-www-form-urlencoded")
  @HTTP(method = "DELETE", path = "api/v1.0/terminal/bindingAllElder", hasBody = true)
  Observable<Map<String, Object>> bindingAllElder(@Header("token") String token
  , @Query("sn") String sn);
```

### 3.配置证书及拦截器及证书
```
//将证书放到raw目录下
public static int[] certificates = new int[]{R.raw.zhengshu};
OkHttpClient.Builder builder = new OkHttpClient.Builder();
     //连接超时时间
     builder.connectTimeout(DEFAULT_TIME_OUT, TimeUnit.SECONDS);
     //写操作 超时时间
     builder.writeTimeout(DEFAULT_READ_TIME_OUT, TimeUnit.SECONDS);
     //读操作超时时间
     builder.readTimeout(DEFAULT_READ_TIME_OUT, TimeUnit.SECONDS);
     //配置https证书
     builder.sslSocketFactory(SslSocketFactory.getSSLSocketFactory(certificates));
     //配置https证书
     builder.hostnameVerifier(SslSocketFactory.getHostnameVerifier());
    //自定义公共拦截器
     HttpCommonInterceptor commonInterceptor = new HttpCommonInterceptor.Builder()
          //添加token
//        .addHeaderParams("token", MyApplication.getInstance().getToken())
          .addHeaderParams("Content-Type", "application/json;charset=UTF-8")
          .build();
     builder.addInterceptor(commonInterceptor);
```

项目域名备案期间，后台使用https+ip方式，并给出https证书.pem文件，要在retorfit框架加入证书。

证书类：
```
public class SslSocketFactory {
    private static Context mContext;
    public static javax.net.ssl.SSLSocketFactory getSSLSocketFactory(int[] certificates) {
        mContext = MyApplication.getContext();
        if (mContext == null) {
            throw new NullPointerException("context == null");
        }
        CertificateFactory certificateFactory;
        SSLContext sslContext = null;
        try {
            certificateFactory = CertificateFactory.getInstance("X.509");
            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
            keyStore.load(null, null);
            for (int i = 0; i < certificates.length; i++) {
                InputStream certificate = mContext.getResources()
                .openRawResource(certificates[i]);
                keyStore.setCertificateEntry(String.valueOf(i)
                , certificateFactory.generateCertificate(certificate));

                if (certificate != null) {
                    certificate.close();
                }
            }
            sslContext = SSLContext.getInstance("TLS");
            TrustManagerFactory trustManagerFactory = TrustManagerFactory.getInstance(
            TrustManagerFactory.getDefaultAlgorithm());

            trustManagerFactory.init(keyStore);
            sslContext.init(null, trustManagerFactory.getTrustManagers()
            , new SecureRandom());

        } catch (Exception e) {
            e.printStackTrace();
        }
        return sslContext.getSocketFactory();
}
    /**
     * 添加https支持
     */
    public static HostnameVerifier getHostnameVerifier() {
        HostnameVerifier hostnameVerifier = new HostnameVerifier() {
            @Override
            public boolean verify(String hostname, SSLSession session) {
                return true;
            }
        };
        return hostnameVerifier;
    }

}
```

注意：添加证书后会导致其他服务器的https无法访问，与后台沟通暂无解决方法，幸运的是其他https链接只使用一个，单独对这个链接使用未加证书的okhttp方法。备案成功后，即可去除证书，恢复原有访问方法。

### 4.请求参数复杂问题
当请求参数为json字符串时 如 ?params = "{"identity":"123","name":"网二"}";时出现请求错误，原因"{","}"等符号和中文会在请求中被转换格式。
解决方法：
(1)GET请求发送json参数包含{}和中文可以使用对参数转码成utf-8,url拼接地址的形式,使用url作为参数：@Url String url
```
String params="{"identity":"123","name":"网二"}"
   url=URL+"?params："+ URLEncoder.encode(params, "utf-8");//拼接url
```
(2)POST请求加入下面即可：
 ```
  @Headers("Content-Type:application/x-www-form-urlencoded; charset=utf-8")
```

### 5.链式请求

```
 /*链式请求*/
        loader.deviceRegister("sn", "username", "password")
                .map(new Function<Map<String, Object>, Observable<Map<String, Object>>>() {
                    @Override
                    public Observable<Map<String, Object>> apply(@NonNull Map<String, Object> map) throws Exception {
                        JSONObject object = new JSONObject(map);
                        String token = object.getString("token");
                        return loader.getOrgId(token);
                    }
                })
                .observeOn(Schedulers.newThread())
                .subscribe(new Consumer<Observable<Map<String, Object>>>() {
                    @Override
                    public void accept(@NonNull Observable<Map<String, Object>> o) throws Exception {
                        JSONObject object = new JSONObject(o.blockingFirst());
                        String getOrgId  = object.getString("orgId");

                    }
                }, new Consumer<Throwable>() {
                    @Override
                    public void accept(Throwable throwable) throws Exception {
                        Log.e(TAG, "deviceRegister:" + throwable.getMessage());
                    }
                });

```
代码参考[Retorfit](https://github.com/zccguagua/RetorfitDemo)