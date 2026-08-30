---
title: Android搭建局域网服务器
date: 2020-06-30 22:14:28
tags:
        - 服务器
        - Android
---


![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_07.jpg "Image")

### AndroidAsync
使用AndroidAsync:  [AndroidAsync](https://github.com/koush/AndroidAsync)
引入AndroidAsync
    implementation 'com.koushikdutta.async:androidasync:2.2.1'

###主要方法

#### 开启服务

```
   private AsyncHttpServer server = new AsyncHttpServer();
   public void startServer() {
    //主页
        server.get("/", new HttpServerRequestCallback() {
            @Override
            public void onRequest(AsyncHttpServerRequest request, AsyncHttpServerResponse response) {
                //需要新建目录,存放文件
                String filePath = Environment.getExternalStorageDirectory() + "/my_android_server/";
                String html = String.format(sendHtml, getFiles(filePath));
                Log.d(TAG, html);
                response.send(html);

            }
        });
        //下载文件
            server.get("/file", new HttpServerRequestCallback() {
            @Override
            public void onRequest(AsyncHttpServerRequest request, AsyncHttpServerResponse response) {
                String name = request.getQuery().getString("name");
                String filePath = Environment.getExternalStorageDirectory() + "/my_android_server/" + name;
                File file = new File(filePath);
                if (file.exists()) {
                    response.sendFile(file);
                } else {
                    Log.d(TAG, "file path = " + file.getAbsolutePath() + "not exist");
                }
            }
        });
    }
     server.listen(PORT_LISTEN_DEFALT);//设置端口号
```
#### 获取IP
```
    public static String getHostIp() {
        try {
            Enumeration<NetworkInterface> allNetInterfaces = NetworkInterface.getNetworkInterfaces();
            while (allNetInterfaces.hasMoreElements()) {
                NetworkInterface netInterface = allNetInterfaces.nextElement();
                Enumeration<InetAddress> addresses = netInterface.getInetAddresses();
                while (addresses.hasMoreElements()) {
                    InetAddress ip = addresses.nextElement();
                    if (ip instanceof Inet4Address
                            && !ip.isLoopbackAddress()
                            && !ip.getHostAddress().contains(":")) {
                        return ip.getHostAddress();
                    }
                }
            }
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }
```

#### 定义返回网页html
```
    private String sendHtml = "<!DOCTYPE html>\n" +
            "<html>\n" +
            "<head lang=\"en\">\n" +
            "    <meta charset=\"UTF-8\">\n" +
            "    <title>main</title>\n" +
            "    <meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\">\n" +
            "    <meta http-equiv=\"X-UA-Compatible\" content=\"IE=edge\">\n" +
            "<body>\n" +
            "%s</body>\n" +
            "</html>";

  String getFiles(String path) {
        StringBuilder files = new StringBuilder();
        File file = new File(path);
        File[] tempList = file.listFiles();
        if (null == tempList || tempList.length == 0) {
            Log.d(TAG, "文件夹不存在或者文件夹内无文件");
            return "请在根目录新建my_android_server并放入文件";
        }
        for (File value : tempList) {
            if (value.isFile()) {
                Log.d(TAG, value.getName());
                String fileName = value.getName();
                String fileURL = "<a href=\"http://%s:5000/file?name=%s\">%s</a><br/>\n";
                String ss = String.format(fileURL, getHostIp(), fileName, fileName);
                files.append(ss);
            }
//            if (value.isDirectory()) {
            //这里就不递归了，
//            }
        }
        return files.toString();
    }
```
#### 使用
在根目录新建my_android_server文件夹,在里面放入文件
局域网内打开“http://IP:端口号”即可打开网页,网页会显示文件夹内文件,点击文件即可下载

