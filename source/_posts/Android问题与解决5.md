---
title: Android问题与解决5
date: 2019-10-13 14:09:00
tags:  
  - Android
categories:
  - Android
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/ins_01.jpg "Image")

[1.关于ping外网问题](#question_01)
[2.定时器的使用](#question_02)
[3.线程及超时方法](#question_03)
[3.线程池](#question_04)



### <span id="question_01">关于ping外网问题</span>

需求：判断外网是否连接。
问题：连接网线安卓设备中，在插上网线但是外网不通处于局域网时，isConnected依然返回true。
解决：需要判断外网是否通
方法：采用pingIP的方法判断外网 
```
Process process = Runtime.getRuntime().exec("ping -c 3 -w 100 8.8.8.8);
//-c 是指ping的次数 3是指ping 3次 ，-w 100  以秒为单位指定超时间隔，是指超时时间为100秒 
```
但是此方法使用一直无效，
在stackoverflow找到解决方法：加/system/bin/ping
Process process = Runtime.getRuntime().exec("/system/bin/ping -c 3 -w 100 www.baidu.com");

#### 主要方法：
```
    public static boolean executeCommand() {
        try {
            Process mIpAddrProcess = runtime.exec("/system/bin/ping -c 2 -w 100 www.baidu.com");
            int mExitValue = mIpAddrProcess.waitFor();
            if (mExitValue == 0) {
                return true;
            } else {
                return false;
            }
        } catch (InterruptedException ignore) {
            ignore.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return false;
    }
```

注意要在子线程执行耗时操作
```
exception:android.view.ViewRootImpl$CalledFromWrongThreadException: Only the original thread that created a view hierarchy can touch its views.
```



### <span id="question_02">定时器的使用</span>
```
   private Timer mTimer;
   private TimerTask mTimerTask;
	//使用：
	stopTimer();
    mTimer = new Timer();
    mTimerTask = new TimerTask() {
        @Override
        public void run() {
            if (count >= COUNT_NUM) {
                stopTimer();
            } else {
                count++;
                orderCheck();
            }
        }
    };
//d秒后开始，每p秒执行一次
    if (mTimer != null && mTimerTask != null)
        mTimer.schedule(mTimerTask, d * 1000, p * 1000);
```
停止方法		

```
	private void stopTimer() {
        count = 0;
        if (mTimer != null) {
            mTimer.cancel();
            mTimer = null;
        }
        if (mTimerTask != null) {
            mTimerTask.cancel();
            mTimerTask = null;
        }
    }
```


### <span id="question_03">线程及超时方法</span>

```
 private Thread thread, workThread;
    private class WorkThread extends Thread {
        @Override
        public void run() {
            //处理比较耗时的操作
            //final Message msg = new Message();//使用下面方法
            final Message msg = mHandler.obtainMessage();
            msg.what = MSG_NERWORK_ICON;
            msg.arg1 = 1;
            Callable<String> task = new Callable<String>() {
                @Override
                public String call() throws Exception {
                    //执行耗时代码
                    if ( IpUtil.executeCommand()) {
                        msg.arg1 = 1;
                    } else {
                        msg.arg1 = 0;
                    }
                    return "success";
                }
            };
            //设置超时时间，指定时间未执行完则中断
            ExecutorService executorService = Executors.newSingleThreadExecutor();
            Future<String> future = executorService.submit(task);
            try {
                //设置超时时间
                String rst = future.get(5, TimeUnit.SECONDS);
                System.out.println(rst);
            } catch (TimeoutException e) {
                msg.arg1 = 0;
                NbLogUtil.d(TAG, "ExecuteCommand Overtime");
            } catch (Exception e) {
                NbLogUtil.d(TAG, "exception:" + e.getMessage());
            } finally {
                executorService.shutdown();
            }
            mHandler.sendMessage(msg);//发送消息更新UI
        }

    }
```
	
### 注意：线程未执行完退出时会出现报错
	
```
	@Override
    public void onDestroy() {
        super.onDestroy();
        mHandler.removeCallbacksAndMessages(null);//未执行完的handler也要关闭
        try {
            workThread.interrupt();//中断线程
        } catch (Exception e) {
            NbLogUtil.d(TAG, "workThread Exception");
        }
    }
```
### <span id="question_04">创建基本线程池</span>

corePoolSize: 该线程池中核心线程的数量。

注意：线程池中存在核心线程与非核心线程，核心线程一旦创建会一直执行任务或等待任务到来，而非核心线程只在任务队列塞满任务时去执行多出的任务，并且非核心线程在等待一段时间后将会被回收，这个时间作为参数可调配，见下面的keepAliveTime参数。

maximumPoolSize：该线程池中最大线程数量。(区别于corePoolSize)

keepAliveTime:从字面上就可以理解，是非核心线程空闲时要等待下一个任务到来的时间，当任务很多，每个任务执行时间很短的情况下调大该值有助于提高线程利用率。注意：当allowCoreThreadTimeOut属性设为true时，该属性也可用于核心线程。

unit:上面时间属性的单位

workQueue:任务队列，后面详述。

threadFactory:线程工厂，可用于设置线程名字等等，一般无须设置该参数。

```
public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue,
                              ThreadFactory threadFactory) {
        ...
    }
    
    final ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(3,5,1,TimeUnit.SECONDS,
    new LinkedBlockingQueue<Runnable>(100));
```
使用 
 ```
 Runnable runnable = new Runnable() {
     @Override
     public void run() { }
     };
 threadPoolExecutor.execute(runnable);
```