---
title: Android问题与解答2
date: 2018-09-25 00:17:44
tags: 
  - Android
categories:
  - Android
---
![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/highway.jpg "Image")

[1.循环遍历元素并设置](#circulation)
[2.EditText回车触发](#EditText)
[4.图片下载](#imageDownload)


### <span id="circulation">循环遍历元素并设置（直接子元素）</span>
```
    int count = line2.getChildCount();
                        for (int i = 0; i < count; i++) {
                            View view = line2.getChildAt(i);
                            if (view instanceof EditText) {
                                editOnOff((EditText) view, isChecked);
                            } else if (view instanceof TextView) {
                                colorView((TextView) view, isChecked);
                            }
                        }
    
    
        public static void editOnOff(EditText view, Boolean isChecked) {
    
            if (isChecked) {
                view.setEnabled(true);
                view.setFocusable(true);
                view.setFocusableInTouchMode(true);
                view.requestFocus();
            } else {
                view.setEnabled(false);
                view.setFocusable(false);
                view.setFocusableInTouchMode(false);
    
            }
        }
```
### <span id="EditText">EditText回车触发</span>

```
   upTime.setOnEditorActionListener(new TextView.OnEditorActionListener() {
               @Override
               public boolean onEditorAction(TextView v, int actionId, KeyEvent event) {
                   int ss = Integer.parseInt(v.getText() + "");
                   if (ss > 240) {
                       upTime.setText("240");
                   } else {
                       upTime.setText(ss);
                   }
   
   	upTime.setSelection(upTime.getText().length());
                   return false;
               }
           });
```


### <span id="imageDownload">图片下载</span>
[下载可以很简单](https://github.com/AriaLyy/Aria)
##### 1.启动图片下载线程
```
        private void onDownLoad(final Contacts contacts) {
            Log.i(TAG, "22222:启动图片下载线程");
            DownLoadImageService service = new DownLoadImageService(getActivity(),
                    contacts.getChildPhoto(), new ImageDownLoadCallBack() {
                @Override
                public void onDownLoadSuccess(File file) {
                    try {
                        File files = new Compressor(getActivity())//压缩图片的依赖
                                .setMaxWidth(120)
                                .setMaxHeight(90)
                                .setQuality(10)//图片质量
                                .setCompressFormat(Bitmap.CompressFormat.WEBP)
                                .setDestinationDirectoryPath(Environment.getExternalStoragePublicDirectory(
                                        Environment.DIRECTORY_PICTURES).getAbsolutePath())
                                .compressToFile(file);
    
                        contacts.setLocalPath(files.getAbsolutePath());
                        mContactsHelper.updateContacts(contacts);//保存路径进数据库
    
                        //Log.e(TAG, "22222压缩后大小:" + files.length());
                        //Log.e(TAG, "22222压缩后文件路径:" + files.getPath());
                        //Log.e(TAG, "22222压缩后文件绝对路径:" + files.getAbsolutePath());
                    } catch (IOException e) {
                        e.printStackTrace();
                    }
                }
                @Override
                public void onDownLoadSuccess(Bitmap bitmap) {
                    Log.i(TAG, "文件路径");
                }
                @Override
                public void onDownLoadFailed() {
                    // 图片保存失败
                    Log.i(TAG, "222222失败了");
                }
            });
            //启动图片下载线程
            new Thread(service).start();
        }
```
##### 2.图片下载服务类 
```
    public class DownLoadImageService implements Runnable {
        private String url;
        private Context context;
        private ImageDownLoadCallBack callBack;
        private File currentFile;
        public DownLoadImageService(Context context, String url, ImageDownLoadCallBack callBack) {
            this.url = url;
            this.callBack = callBack;
            this.context = context;
        }
    
        @Override
        public void run() {
            File file = null;
            Bitmap bitmap = null;
            try {
    //            file = Glide.with(context)
    //                    .load(url)
    //                    .downloadOnly(Target.SIZE_ORIGINAL, Target.SIZE_ORIGINAL)
    //                    .get();
                bitmap = Glide.with(context)
                        .load(url)
                        .asBitmap()
                        .into(Target.SIZE_ORIGINAL, Target.SIZE_ORIGINAL)
                        .get();
                if (bitmap != null){
                    // 在这里执行图片保存方法
                    saveImageToGallery(context,bitmap);
                }
            } catch (Exception e) {
                e.printStackTrace();
            } finally {
    //            if (file != null) {
    //                callBack.onDownLoadSuccess(file);
    //            } else {
    //                callBack.onDownLoadFailed();
    //            }
                if (bitmap != null && currentFile.exists()) {
                    callBack.onDownLoadSuccess(currentFile);
                } else {
                    callBack.onDownLoadFailed();
                }
            }
        }
    
        public void saveImageToGallery(Context context, Bitmap bmp) {
            // 首先保存图片
             File file = Environment.getExternalStoragePublicDirectory(Environment.DIRECTORY_PICTURES).getAbsoluteFile();//注意小米手机必须这样获得public绝对路径
            String fileName = "新建文件夹";
            File appDir = new File(file ,fileName);
            if (!appDir.exists()) {
                appDir.mkdirs();
            }
            String fm = System.currentTimeMillis() + ".jpg";
            currentFile = new File(appDir, fm);
    
            FileOutputStream fos = null;
            try {
                fos = new FileOutputStream(currentFile);
                bmp.compress(Bitmap.CompressFormat.JPEG, 100, fos);
                fos.flush();
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    if (fos != null) {
                        fos.close();
                    }
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
    
            // 其次把文件插入到系统图库
    //        try {
    //            MediaStore.Images.Media.insertImage(context.getContentResolver(),
    //                    currentFile.getAbsolutePath(), fileName, null);
    //        } catch (FileNotFoundException e) {
    //            e.printStackTrace();
    //        }
    
            // 最后通知图库更新
            context.sendBroadcast(new Intent(Intent.ACTION_MEDIA_SCANNER_SCAN_FILE,
                    Uri.fromFile(new File(currentFile.getPath()))));
        }
    }
```
### 增加闪烁动画封装
```
    class InitAnimation {
            private Button mButton;
            private String text;
    
            private int centerDrawable;
            private int backGroundDrawable;
            private BtnBgRes res;
            public ValueAnimator animator;
    
            public InitAnimation(Button button, String text, int centerDrawable, int backGroundDrawable, BtnBgRes res) {
    //要闪烁的按钮,按钮中间的文字(初始化文字),按钮中间的图标(初始化图标),背景(初始化背景),btnbgres(闪烁的多幅图片替换)
                this.text = text;
                this.mButton = button;
                this.centerDrawable = centerDrawable;
                this.backGroundDrawable = backGroundDrawable;
                this.res = res;
            }
    
            public boolean isAnimation() {
                boolean flag = false;
                if (animator != null && animator.isRunning()) {
                    flag = true;
                }
                return flag;
            }
    
            public void startAnimation() {
    //        main_help.setText(res.getTextState());
                mButton.setText("");
                Drawable helpImg = getResources().getDrawable(centerDrawable);
                helpImg.setBounds(0, 50, 150, 200);
                mButton.setCompoundDrawables(null, null, null, null);
    
                if (animator != null && animator.isRunning()) {
                    animator.cancel();
                }
                startDrawable = ContextCompat.getDrawable(getActivity(), res.getStartDrawable());
                endDrawable = ContextCompat.getDrawable(getActivity(), res.getEndDrawable());
                animator = ValueAnimator.ofObject(new BackgroundEvaluator(), startDrawable, endDrawable);
                animator.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
                    @Override
                    public void onAnimationUpdate(ValueAnimator animation) {
    //                Log.d(TAG, "Animation:---->start");
                        mButton.setBackground((Drawable) animation.getAnimatedValue());
                    }
                });
                animator.setDuration(1500);   //毫秒
                animator.setRepeatCount(ValueAnimator.INFINITE);  //无限循环
                animator.start();
    //        talkState.setText(res.getTextState());
    
            }
    
            public void stopAnimation() {
                if (isAnimation()) {
                    animator.cancel();
                }
                Drawable helpImg = getResources().getDrawable(centerDrawable);
    //            R.drawable.alarm
                mButton.setBackgroundResource(backGroundDrawable);
    //            R.drawable.bt_shape_red
                helpImg.setBounds(0, 50, 150, 200);
                mButton.setCompoundDrawables(null, helpImg, null, null);
                mButton.setText(text);
    //            getResources().getString(R.string.home_button_alarm_help);
            }
        }
```
##### 相关类
```
public class BtnBgRes {
    private String textState;
    private int startDrawable;
    private int endDrawable;

    public BtnBgRes(String textState, int startDrawable, int endDrawable) {
        this.textState = textState;
        this.startDrawable = startDrawable;
        this.endDrawable = endDrawable;
    }

    public String getTextState() {
        return textState;
    }

    public void setTextState(String textState) {
        this.textState = textState;
    }

    public int getStartDrawable() {
        return startDrawable;
    }

    public void setStartDrawable(int startDrawable) {
        this.startDrawable = startDrawable;
    }

    public int getEndDrawable() {
        return endDrawable;
    }

    public void setEndDrawable(int endDrawable) {
        this.endDrawable = endDrawable;
    }
}
```
##### 类描述：背景图片求值器
```
public class BackgroundEvaluator implements TypeEvaluator {
    @Override
    public Object evaluate(float fraction, Object startValue, Object endValue) {
        Drawable startDrawable = (Drawable) startValue;
        Drawable endDrawable = (Drawable) endValue;
        if (fraction <= 0.7) {
            return startDrawable;
        } else {
            return endDrawable;
        }
    }
}
```

