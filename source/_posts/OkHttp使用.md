---
title: OkHttp使用
date: 2017-05-05 11:07:03
tags: [随笔,Java]
---
OkHttp一个处理网络请求的开源项目,是安卓端最火热的轻量级框架,由移动支付Square公司贡献，用于替代HttpUrlConnection和Apache HttpClien<!-- more -->【android API23 6.0里已移除HttpClient,现在已经打不出来】，本文总结了android studio中OkHttp3的基本用法。

- Android 端用 Android Studio开发

anbroid 端项目 [GitHub 地址: https://github.com/xaguo/OkHttp3Demo.git](https://github.com/xaguo/OkHttp3Demo.git)

- 服务端用MyEclipse + struts2

服务端项目 [GitHub 地址: https://github.com/xaguo/Okhttp3DemoWeb.git](https://github.com/xaguo/Okhttp3DemoWeb.git)

使用示例

引入依赖、添加权限

	//引入okhttp依赖
	compile 'com.squareup.okhttp3:okhttp:3.6.0'
	//加入联网和读写权限
	<uses-permission android:name="android.permission.INTERNET" />
	<uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

## Get请求

	```java
	//1.拿到okHttpClient对象
	   OkHttpClient okHttpClient = new OkHttpClient();
	   //2.构造Request
	   Request.Builder builder = new Request.Builder();
	   Request request = builder.get().url("http://www.imooc.com").build();
	   //3.将Request封装为Call
	   Call call = okHttpClient.newCall(request);
	   //4.执行call
	   //Reponse response = call execute();
	   call.enqueue(new Callback() {
	       @Override
	       public void onFailure(Call call, IOException e) {
	           L.e("onFailure:" + e.getMessage());
	           e.printStackTrace();
	       }
	       @Override
	       public void onResponse(Call call, Response response) throws IOException {
	           L.e("onResponse");
	           final String res = response.body().string();
	           L.e(res);
	      	 // InputStream is = response.body().byteStream();
	           runOnUiThread(new Runnable() {
	               @Override
	               public void run() {
	                   mTvResult.setText(res);
	               }
	           });
	       }
	   });
	```
	
将Request封装为Call并执行Call
	
	```java
	OkHttpClient okHttpClient = new OkHttpClient.Builder()
	   .cookieJar(new CookieJar() {
	       private Map<String, List<Cookie>> cookieStore = new HashMap<>();
	       @Override
	       public void saveFromResponse(HttpUrl url, List<Cookie> cookies) {
	           cookieStore.put(url.host(), cookies);
	           }
	       @Override
	       public List<Cookie> loadForRequest(HttpUrl url) {
	           List<Cookie> cookies = cookieStore.get(url.host());
	           return cookies != null ? cookies : new ArrayList<Cookie>();
	           }
	       }).build();
	   
	   private String mBaseUrl = "http://192.169.1.107:8080/okhttpDemo/";
	   //将Request封装为Call并执行Call
	   private void executeRequest(Request request) {
	       //3.将Request封装为Call
	       Call call = okHttpClient.newCall(request);
	       //4.执行call
	       //Reponse response = call execute();
	       call.enqueue(new Callback() {
	           @Override
	           public void onFailure(Call call, IOException e) {
	               L.e("onFailure:" + e.getMessage());
	               e.printStackTrace();
	           }
	           @Override
	           public void onResponse(Call call, Response response) throws IOException {
	               L.e("onResponse");
	               final String res = response.body().string();
	               L.e(res);
	           // InputStream is = response.body().byteStream();
	               runOnUiThread(new Runnable() {
	                   @Override
	                   public void run() {
	                       mTvResult.setText(res);
	                   }
	               });
	           }
	```

Get请求1

	```java
	
	//Get
	public void doGet(View view) {
	    //1.拿到okHttpClient对象
	   // OkHttpClient okHttpClient = new OkHttpClient();
	    //2.构造Request
	    Request.Builder builder = new Request.Builder();
	    Request request = builder
	            .get()
	            .url(mBaseUrl+"login?username=xa&password=1234")
	            .build();
	    executeRequest(request);
	}
	```
## Post请求

post数据

	```java
	//post数据
	  	public void doPost(View view) {
	      //1.拿到okHttpClient对象
	      //OkHttpClient okHttpClient = new OkHttpClient();
	      //2.构造Request
	      Request.Builder builder = new Request.Builder();
	      //FormBody extends RequestBody
	      FormBody formbody = new FormBody.Builder().add("username", "xa").add("password", "123456").build();
	      Request request = builder
	              .url(mBaseUrl + "login")//mBaseUrl为服务器端的Url
	              .post(formbody)
	              .build();
	      executeRequest(request);
	  }
	```

Post一个字符串

```java
//Post一个字符串
public void doPostString(View view) {
   RequestBody requestBody = RequestBody.create(MediaType.parse("text/plain;chaset=UTF-8"),"{username:xa,password:654321}");
      //2.构造Request
   Request.Builder builder = new Request.Builder();
   Request request = builder
           .url(mBaseUrl + "postString")//mBaseUrl为服务器端的Url
           .post(requestBody)
           .build();
   executeRequest(request);
  }
```

Post文件

	```java
	//Post文件
	   public void doPostFile(View view) {
	       File file=new File(Environment.getExternalStorageDirectory(),"picture8.jpg");
	       if (!file.exists()) {
	           L.e(file.getAbsolutePath() + " not exist!");
	           return;
	       }
	       //mime type
	       RequestBody requestBody = RequestBody.create(MediaType.parse("image/jpeg"),file);
	       //2.构造Request
	       Request.Builder builder = new Request.Builder();
	       Request request = builder
	               .url(mBaseUrl + "postFile")//mBaseUrl为服务器端的Url
	               .post(requestBody)
	               .build();
	       executeRequest(request);
	   }
	```

## UpLoad 数据

	```java
	//UpLoad 数据//mBaseUrl为服务器端的Url
	   public void doUpload(View view) {
	       final File file=new File(Environment.getExternalStorageDirectory(),"picture8.jpg");
	       if (!file.exists()) {
	           L.e(file.getAbsolutePath() + " not exist!");
	           return;
	       }
	       MultipartBody requestBody = new MultipartBody.Builder().setType(MultipartBody.FORM)
	               .addFormDataPart("username","xa")
	               .addFormDataPart("password","445566")
	               .addFormDataPart("mPhoto","picture.jpg",RequestBody.create(MediaType.parse("image/jpeg"),file))
	               .build();
	       CountingRequestBody countingRequrstBody=new CountingRequestBody(requestBody, new CountingRequestBody.Listener() {
	           @Override
	           public void onRequestProgress(long byteWrited, long contentLength) {
	               L.e(byteWrited + "/" + contentLength);
	               final long finalbyteWrited=byteWrited;
	               final long finalcontentLength=contentLength;
	               runOnUiThread(new Runnable() {
	                   @Override
	                   public void run() {
	                       mTvResult.setText(finalbyteWrited + "/" + finalcontentLength);
	                   }
	               });
	           }
	       });
	       //2.构造Request
	       Request.Builder builder = new Request.Builder();
	       Request request = builder
	               .url(mBaseUrl + "uploadInfo")
	               .post(countingRequrstBody)
	               .build();
	       executeRequest(request);
	   }
	```

## 下载

下载图片

	```java
	//下载图片//mBaseUrl为服务器端的Url
	   public void doDownload(View view) {
	       Request.Builder builder = new Request.Builder();
	       Request request = builder
	               .get()
	               .url(mBaseUrl+"files/picture.jpg")
	               .build();
	       //3.将Request封装为Call
	       Call call = okHttpClient.newCall(request);
	       //4.执行call
	       //Reponse response = call execute();
	       call.enqueue(new Callback() {
	           private Response response;
	           @Override
	           public void onFailure(Call call, IOException e) {
	               L.e("onFailure:" + e.getMessage());
	               e.printStackTrace();
	           }
	           @Override
	           public void onResponse(Call call, Response response) throws IOException {
	               L.e("onResponse");
	               final long total = response.body().contentLength();
	               long sum=0L;
	               InputStream is = response.body().byteStream();
	               int len = 0;
	               //new File(Environment.getExternalStorageDirectory().toString()).mkdirs();
	               String path=Environment.getExternalStorageDirectory()+"/films";
	               File folder = new File(path);
	               if (!folder.exists()) {
	                   folder.mkdir();
	               }
	               File file = new File(path,"xa123.jpg");
	               byte[] buf = new byte[128];
	               FileOutputStream fos = new FileOutputStream(file);
	               while ((len = is.read(buf)) != -1) {
	                   fos.write(buf, 0, len);
	                   sum=sum+len;
	                   L.e(sum + "/" + total);
	                   final long finalSum = sum;
	                   runOnUiThread(new Runnable() {
	                       @Override
	                       public void run() {
	                           mTvResult.setText(finalSum + "/" + total);
	                       }
	                   });
	               }
	               fos.flush();
	               fos.close();
	               is.close();
	               L.e("download success!");
	               }
	       });
	   }
	```

下载文件

	```java
	//下载文件
	public void doDownloadImg(View view) {
	    Request.Builder builder = new Request.Builder();
	    Request request = builder
	            .get()
	            .url(mBaseUrl+"files/picture.jpg")
	            .build();
	    //3.将Request封装为Call
	    Call call = okHttpClient.newCall(request);
	    //4.执行call
	    //Reponse response = call execute();
	    call.enqueue(new Callback() {
	        private Response response;
	        @Override
	        public void onFailure(Call call, IOException e) {
	            L.e("onFailure:" + e.getMessage());
	            e.printStackTrace();
	        }
	        @Override
	        public void onResponse(Call call, Response response) throws IOException {
	            L.e("onResponse");
	            InputStream is = response.body().byteStream();
	            //BitmapFactory.Options
	            final Bitmap bitmap = BitmapFactory.decodeStream(is);
	            
	            runOnUiThread(new Runnable() {
	                @Override
	                public void run() {
	                    mImageView.setImageBitmap(bitmap);
	                }
	            });
	            L.e("download success!");
	        }
	    });
	}
	```

[参考资源](http://www.imooc.com/learn/764)