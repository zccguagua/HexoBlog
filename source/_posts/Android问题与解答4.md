---
title: Android问题与解答4
date: 2018-12-05 23:21:02
tags: 
  - Android
categories:
  - Android

---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/girl_night.jpg "Image")

[1.判断是否快速点击](#fastclick)
[2.自定义位置](#margins)
[3.定时器](#timer)
[4.视频合并](#appendmp4)
[5.方法执行超过指定时间](#muchtime)


### <span id = "fastclick">1.判断是否快速点击</span>
```
private static final int MIN_DELAY_TIME= 1000; // 两次点击间隔不能少于1000ms
     private static long lastClickTime; 
    public static boolean isFastClick() {
 	boolean flag = true;
	long currentClickTime = System.currentTimeMillis();
	 if ((currentClickTime - lastClickTime) >= MIN_DELAY_TIME) { 
		flag = false;
	 } 
	lastClickTime = currentClickTime; 
	return flag; 
    }
```
### <span id = "margins">2.自定义位置</span>
[RelativeLayout.addRule()方法](https://www.cnblogs.com/Annoying/p/5402339.html)

```
   private RelativeLayout.LayoutParams videoParams；
   videoParams = (RelativeLayout.LayoutParams) video.getLayoutParams();
   videoParams.addRule(RelativeLayout.BELOW, R.id.recyclerTip_layout);
   videoParams.addRule(RelativeLayout.ALIGN_PARENT_LEFT, RelativeLayout.TRUE);
   videoParams.setMargins(10, 10, 0, 0);//左上右下
```

```
//设置位置
 Drawable helpImg = getResources().getDrawable(R.drawable.alarm);//一张图片
 //设置位置前两个是组件左上角在容器中的坐标，后两个是组件的宽度和高度 
        helpImg.setBounds(0, 50, 150, 200);
        //讲图片在main_help中固定位置
        main_help.setCompoundDrawables(null, helpImg, null, null);

```

### <span id = "timer">3.定时器</span>
#### service里设置开机启动
```
    @Override
    protected void startMainActivity() {
   //分机背光时间设置
        BackLightUtils.getInstance().openCallUploadTimer(getApplicationContext());
}
```
#### 定义类方法
```
public class TimerManager {

    public static final String TAG = "TimerManager";
    public static final String ACTION = "com.lonbon.lonbonbroadcast.manager.TestReceiver";
    private static final int TIME_INTERVAL = 1000 * 60 * 60 * 24;// 24h
    private Integer[] weeks;
    private int weeksIndex;
    private TimerManagerCallback timerManagerCallback;
    private static final String HOUR = "hour";
    private static final String MINUTE = "minute";
    private static final String ID = "id";
    private AlarmManager alarmManager;
    private PendingIntent paddingIntent;
    private TestReceiver testReceiver;
    private Context mContext;
    private IntentFilter filter;
    private boolean stopFlag = false;

    public void setAlarmManager(Context context, int hour, int minute) {
        Log.i(TAG, "setAlarmManager: ");
        mContext = context;

        //设置时间
        Calendar calendar = Calendar.getInstance();
        calendar.set(calendar.get(Calendar.YEAR), calendar.get(Calendar.MONTH), calendar.get
                (Calendar.DAY_OF_MONTH), hour, minute, 0);

        testReceiver = new TestReceiver();
        filter = new IntentFilter();
        filter.addAction(ACTION + hour + minute);
        mContext.registerReceiver(testReceiver, filter);

        alarmManager = (AlarmManager) mContext.getSystemService(Context.ALARM_SERVICE);
        Intent intent = new Intent(ACTION + hour + minute);
        intent.putExtra(HOUR, hour + "");
        intent.putExtra(MINUTE, minute + "");
        intent.putExtra(ID, minute + "");
        paddingIntent = PendingIntent.getBroadcast(context, hour + minute, intent, PendingIntent.FLAG_UPDATE_CURRENT);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            alarmManager.setExactAndAllowWhileIdle(AlarmManager.ELAPSED_REALTIME_WAKEUP, calMethod(calendar.getTimeInMillis()), paddingIntent);
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
            alarmManager.setExact(AlarmManager.RTC_WAKEUP, calMethod(calendar.getTimeInMillis()), paddingIntent);
        } else {
            alarmManager.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP, calMethod(calendar.getTimeInMillis()), TIME_INTERVAL, paddingIntent);
        }
        stopFlag = false;
    }

    /**
     * @param dateTime 传入的是时间戳（设置当天的年月日+从选择框拿来的时分秒）
     * @return 返回起始闹钟时间的时间戳
     */
    private long calMethod(long dateTime) {
        long time = 0;
        //weekflag == 0表示是按天为周期性的时间间隔或者是一次行的，weekfalg非0时表示每周几的闹钟并以周为时间间隔

        Calendar c = Calendar.getInstance();
        int week = c.get(Calendar.DAY_OF_WEEK);
        if (1 == week) {
            week = 7;
        } else if (2 == week) {
            week = 1;
        } else if (3 == week) {
            week = 2;
        } else if (4 == week) {
            week = 3;
        } else if (5 == week) {
            week = 4;
        } else if (6 == week) {
            week = 5;
        } else if (7 == week) {
            week = 6;
        }
        int weekflag = 0;
        if (weeks != null) {
            for (int i = 0; i < weeks.length; i++) {
                if (weeks[i] == null) {
                    continue;
                }
                if (weeks[i] == week && dateTime > System.currentTimeMillis()) {
                    weeksIndex = i;
                    break;
                }
                if (weeks[i] > week) {
                    weeksIndex = i;
                    break;
                } else {
                    weeksIndex = 0;
                }
            }
            if (weeksIndex < weeks.length) {
                if (weeks[weeksIndex] != null) {
                    weekflag = weeks[weeksIndex];
                }
            }
        }

        if (weekflag == week) {
            if (dateTime > System.currentTimeMillis()) {
                time = dateTime;
            } else {
                time = dateTime + 7 * 24 * 3600 * 1000;
            }
        } else if (weekflag > week) {
            time = dateTime + (weekflag - week) * 24 * 3600 * 1000;
        } else if (weekflag < week) {
            time = dateTime + (weekflag - week + 7) * 24 * 3600 * 1000;
        }
        return time;
    }

    public void setWeeks(String[] weeks) {

        Integer[] a = new Integer[weeks.length - 1];
        for (int i = 0; i < weeks.length; i++) {
            if (!weeks[i].isEmpty()) {
                a[i - 1] = Integer.valueOf(weeks[i]);
            }
        }
        this.weeks = a;
    }

    public void cancel() {
        if (alarmManager != null && paddingIntent != null) {
            alarmManager.cancel(paddingIntent);
        }
        if (!stopFlag && mContext != null) {
            mContext.unregisterReceiver(testReceiver);
            stopFlag = true;
        }
    }

    public interface TimerManagerCallback {
        void onReceive(TimerManager timerManager);
    }

    public class TestReceiver extends BroadcastReceiver {

        @Override
        public void onReceive(Context context, Intent intent) {
            if (timerManagerCallback != null) {
                timerManagerCallback.onReceive(TimerManager.this);
            }
            int hour = Integer.parseInt(intent.getStringExtra(HOUR));
            int minute = Integer.parseInt(intent.getStringExtra(MINUTE));
            Calendar calendar = Calendar.getInstance();
            calendar.set(calendar.get(Calendar.YEAR), calendar.get(Calendar.MONTH), calendar.get
                    (Calendar.DAY_OF_MONTH), hour, minute, 0);

            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
                alarmManager.setExactAndAllowWhileIdle(AlarmManager.ELAPSED_REALTIME_WAKEUP, calMethod(calendar.getTimeInMillis()), paddingIntent);
            } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
                alarmManager.setExact(AlarmManager.RTC_WAKEUP, calMethod(calendar.getTimeInMillis()), paddingIntent);
            } else {
                alarmManager.setRepeating(AlarmManager.ELAPSED_REALTIME_WAKEUP, calMethod(calendar.getTimeInMillis()), TIME_INTERVAL, paddingIntent);
            }

        }
    }

    public void setTimerManagerCallback(TimerManagerCallback timerManagerCallback) {
        this.timerManagerCallback = timerManagerCallback;
    }

    public TestReceiver getTestReceiver() {
        return testReceiver;
    }
}
```
#### 使用
```
    startSlaveTimer = new TimerManager();
        startSlaveTimer.setWeeks(new String[]{"", "1", "2", "3", "4", "5", "6", "7"});
        startSlaveTimer.setTimerManagerCallback(new TimerManager.TimerManagerCallback() {
            @Override
            public void onReceive(TimerManager timerManager) {
                try {
                    // 这里是开启分机背光
                    Log.i("222222", "定时器开启了分机背光");

                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        });
        startSlaveTimer.setAlarmManager(context, Integer.parseInt(slaveTime.getStartHour()), Integer.parseInt(slaveTime.getStartMinute()));
```
#### 取消
```
   if (startSlaveTimer != null) {
            startSlaveTimer.cancel();
            startSlaveTimer = null;
        }
```
### <span id = "appendmp4">4.视频合并</span>
```
在api23一下没有录制视频暂停功能，需要自己合并视频
1.引入 compile 'com.googlecode.mp4parser:isoparser:1.1.21'//视频处理
2.主要方法
//将两个mp4视频进行拼接
    public static void appendMp4(List<String> mMp4List,String outputpath){ 
     private static List<Movie> moviesList = new ArrayList<>();
     private static List<Track> videoTracks = new ArrayList<>(); 
     private static List<Track> audioTracks = new ArrayList<>(); 
try { 
	for (int i=0;i<mMp4List.size();i++) { 
	Movie movie=MovieCreator.build(mMp4List.get(i));
 	moviesList.add(movie); 
	} 
} catch (IOException e) {
 e.printStackTrace(); 
} 
for (Movie m : moviesList) { 
	for (Track t : m.getTracks()) { 
	if (t.getHandler().equals("soun")) { 
	audioTracks.add(t); 
} 
if (t.getHandler().equals("vide")) {
 	videoTracks.add(t);
 	}
 	} 
}
 Movie result = new Movie();
 try { 
	if (audioTracks.size() > 0) {
 		result.addTrack(new AppendTrack(audioTracks.toArray(new Track[audioTracks.size()]))); 
	} 
	if (videoTracks.size() > 0) { 
		result.addTrack(new AppendTrack(videoTracks.toArray(new Track[videoTracks.size()])));
 	} 
} catch (IOException e) {
 	e.printStackTrace(); 
} 
Container out = new DefaultMp4Builder().build(result);
 try {
 	FileChannel fc = new FileOutputStream(new File(outputpath)).getChannel(); 
	out.writeContainer(fc); 
	fc.close(); 
} catch (Exception e) { 
	e.printStackTrace();
 }
	 moviesList.clear();
	videoTracks.clear();
	audioTracks.clear();
 	 for (int i = 0; i < mMp4List.size(); i++) {
	      deleteSingleFile(mMp4List.get(i));
	   }
 } 

/**
* 删除单个文件
* @param filePathName 要删除的文件的文件名
* @return 单个文件删除成功返回true，否则返回false  
*/
    
private static void deleteSingleFile(String filePathName) {
 try {
	 File file = new File(filePathName);
	 // 如果文件路径所对应的文件存在，并且是一个文件，则直接删除
	  if (file.exists() && file.isFile()) {
		  if (file.delete()) {
		// Log.e(TAG, "删除单个文件" + filePathName + "成功！");
        }
   }
 } catch (Exception e) {
     e.printStackTrace();
  }
 }
```
### <span id = "muchtime">5.方法执行超过指定时间</span>
```
//执行超时的方法
   ExecutorService executorService = Executors.newSingleThreadExecutor();
                    Future<String> future = executorService.submit(task);
                    try {
                        //3秒内没有执行完则抛超时异常
                        String result = future.get(2, TimeUnit.SECONDS);
                        Log.d(TAG, "设置的result是：" + result);

                    } catch (InterruptedException e) {
                        Log.d(TAG, "超时异常1：" + e.toString());

                        e.printStackTrace();
                    } catch (ExecutionException e) {
                        Log.d(TAG, "超时异常2：" + e.toString());

                        e.printStackTrace();
                    } catch (TimeoutException e) {
                        Log.d(TAG, "超时异常3：" + e.toString());
                        //这里可以做超时后执行操作
//                        Looper.prepare();
                        Toast.makeText(getApplicationContext(), "摄像头出现问题，请检查摄像头", Toast.LENGTH_SHORT).show();
//                        Looper.loop();
                        kill_camera();
                        this.finish();

                    }

    Callable<String> task = new Callable<String>() {
        @Override
        public String call() throws Exception {
            start();//执行的任务
            return "success";
        }
    };
```
