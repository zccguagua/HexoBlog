---
title: Android问题与解答3
date: 2018-10-08 00:42:31
tags: 
        - Android
categories:
  - Android
---

![](https://zccguagua.oss-cn-hangzhou.aliyuncs.com/images/blog/cover/yellowClothes.jpg "Image")

[1.GreenDao2.0的使用](#greendao)
[2.自定义拖动条](#SeekBar)
[3.自定义字体](#changeText)

### <span id = "greendao"> GreenDao2.0的使用</span>

#### 注意事项
1.Android 数据库建表时int 型使用long 

#### 1.导入依赖包
compile 'de.greenrobot:greendao: 2.0.0'
compile 'de.greenrobot:greendao-generator: 2.0.0'

#### 2.GreenDaoGenerator添加表
``` 
public class GreenDaoGenerator {
    //model类包包
    private static final String DEFAULT_MODEL_PACKET = "com.lonbon.smartcarer.db.model";
    //数据库包
    private static final String DEFAULT_DAO_PACKET = "com.lonbon.smartcarer.db.dao";
    //项目Java文件夹
    private static final String PATH = "nnv_smartcarer/src/main/java";
    //数据库版本
    private static final int VERSION = 6;

//在addEntity添加好表后点击下面的运行按钮即可在上面设置好的路径目录生成对应的类
 ▲   public static void main(String args[]) throws Exception {
        Schema schema = new Schema(VERSION, DEFAULT_MODEL_PACKET);
        schema.setDefaultJavaPackageDao(DEFAULT_DAO_PACKET);
        schema.enableKeepSectionsByDefault();
        addEntity(schema);
        File file = new File(PATH);
        if (!file.exists()) {
            file.mkdirs();
        }
        new DaoGenerator().generateAll(schema, PATH);
    }
    private static void addEntity(Schema schema) {
        //配置表
        Entity config = schema.addEntity("Config");//表名
        config.setTableName("CONFIG");//表名
        config.addLongProperty("id").primaryKey();//1.Android 数据库建表时int 型使用long 
        config.addStringProperty("section").notNull();
        config.addStringProperty("key").notNull();
        config.addStringProperty("value");
        config.addBooleanProperty("isChanged");
        config.addStringProperty("last_modify");
    }
}
```

#### 3.在helper文件夹新建helper类,sql语句
```
public class ContactsHelper {
    private static DaoMaster daoMaster;
    private static DaoSession daoSession;
    private static ContactsHelper instance;
    private ContactsDao mContactsDao;
    private ContactsHelper(Context context) {
        if (daoMaster == null) {
//            DaoMaster.OpenHelper helper = new LBDevOpenHelper(BaseApplication.getAppContext(),
//                    DB_NAME, null);
            DaoMaster.OpenHelper helper = new MyOpenHelper(context,
                    DbManager.DB_NAME, null);
            SQLiteDatabase writableDatabase = helper.getWritableDatabase();
            daoMaster = new DaoMaster(writableDatabase);
        }
        if (daoSession == null) {
            daoSession = daoMaster.newSession();
        }
        try {
            mContactsDao = daoSession.getContactsDao();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    public static ContactsHelper getInstance(Context context) {
        if (instance == null) {
            instance = new ContactsHelper(context);
        }
        return instance;
    }
    /*添加数据*/
    public void addContacts(Contacts contacts) {
        mContactsDao.insertOrReplace(contacts);
    }
    /*通过id查找*/
    public Contacts findContactsById(Long id) {
        QueryBuilder<Contacts> builder = mContactsDao.queryBuilder().where(ContactsDao.Properties.ContactId.eq(id));

        if (builder.list() == null || builder.list().size() == 0) {
            return null;
        } else {
            return builder.list().get(0);
        }
    }
        /*通过手机号查找,返回图片地址*/
    public String findContactsByPhone(String num) {
        QueryBuilder<Contacts> builder = mContactsDao.queryBuilder().where(ContactsDao.Properties.RelativePhone.eq(num));

        if (builder.list() == null || builder.list().size() == 0) {
            return null;
        } else {
            return builder.list().get(0).getLocalPath();
        }
    }
    /*查找所有*/
    public List<Contacts> queryAllContacts() {
        return mContactsDao.loadAll();
    }
    /*通过id删除*/
    public void deleteContacts(String id) {
        DeleteQuery<Contacts> buildDelete = mContactsDao.queryBuilder().where(ContactsDao.Properties.ContactId.eq(id)).buildDelete();
        buildDelete.executeDeleteWithoutDetachingEntities();
    }
    /*更新*/
    public void updateContacts(Contacts contacts) {
        mContactsDao.update(contacts);
    }
    /*删除所有*/
    public void deleteAllContacts() {
        mContactsDao.deleteAll();
    }
    /*通过id删除*/
    public void updateLocalPath(String id, String path) {
        DeleteQuery<Contacts> buildDelete = mContactsDao.queryBuilder().where(ContactsDao.Properties.ContactId.eq(id)).buildDelete();
        buildDelete.executeDeleteWithoutDetachingEntities();
    }
    //降序排序offset忽略前多少数据,可用于翻页)
    mCallRecordDao.queryBuilder().orderDesc(CallRecordDao.Properties.CallDate).offset(0 * 6).limit(6).list();
    //greenDao 复杂条件查询
    QueryBuilder<ContactInfo> builder = mContactInfoDao.queryBuilder()
       .where(new WhereCondition.StringCondition("PHONE_NUM is not NULL GROUP BY PHONE_NUM"))
       .orderDesc(ContactInfoDao.Properties.Id);	
    //查询是否是true
    QueryBuilder<SettingButton> builder = mSettingButtonDao.queryBuilder()
       .where(SettingButtonDao.Properties.IsShow.eq("TRUE"));
}
```

#### 4.使用
```
   private ContactsHelper mContactsHelper;
   mContactsHelper = ContactsHelper.getInstance(mContext);
   String filePath = mContactsHelper.findContactsByPhone(mData.get(position).getRelativePhone());
```
### <span id = "SeekBar"> 自定义拖动条</span>
```
 <SeekBar
              android:id="@+id/volume"
              style="@style/BcSeekBarStyle"
              android:layout_width="match_parent"
              android:layout_height="wrap_content"
              android:layout_gravity="left|center"
              android:max="100"
              android:progress="10" />
```
```
 <style name="BcSeekBarStyle" parent="@android:style/Widget.SeekBar">
        <item name="android:indeterminateOnly">false</item>
        <item name="android:indeterminateDrawable">
            @android:drawable/progress_indeterminate_horizontal
        </item>
        <item name="android:progressDrawable">@drawable/lb_seekbar_horizontal</item>//进度条
        <item name="android:thumb">@mipmap/lb_seekbar_button</item>//按钮
        <item name="android:maxHeight">10dp</item>
        <item name="android:thumbOffset">0dip</item>
    </style>
```
进度条
```
<?xml version="1.0" encoding="UTF-8"?>
<layer-list
    xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:id="@android:id/background" android:drawable="@mipmap/seek_bkg" />//背景
    <item android:id="@android:id/secondaryProgress">
        <clip>
            <shape>
                <corners android:radius="2.0dp" />
                <gradient android:startColor="#80ffd300" android:endColor="#a0ffcb00" android:angle="270" android:centerY="0.75" android:centerColor="#80ffb600" />
            </shape>
        </clip>
    </item>
    <item android:id="@android:id/progress">
        <clip android:drawable="@mipmap/lb_seek" />//进度
    </item>
</layer-list>
```


### <span id = "changeText"> 自定义字体</span>

方法1.此方法多次使用出现卡顿

```
	在main下新建assets/fonts,将字体放到fonts文件夹下
        AssetManager assets = getActivity().getAssets();
        Typeface fromAsset = Typeface.createFromAsset(assets, "fonts/PingFang Regular.ttf");
        online_order_text.setTypeface(fromAsset);
```

方法2.缓存方法（注意：可能存在style属性无效）
```
public class FontCache {

    private static HashMap<String, Typeface> fontCache = new HashMap<>();

    public static Typeface getTypeface(String fontname, Context context) {
        Typeface typeface = fontCache.get(fontname);
        if (typeface == null) {
            try {
                typeface = Typeface.createFromAsset(context.getAssets(), fontname);
            } catch (Exception e) {
                return null;
            }
            fontCache.put(fontname, typeface);
        }

        return typeface;
    }

}
```
 自定义平pingfang字体
 
```
public class PingFangTextView extends android.support.v7.widget.AppCompatTextView {

    public PingFangTextView(Context context) {
        super(context);

        applyCustomFont(context);
    }

    public PingFangTextView(Context context, AttributeSet attrs) {
        super(context, attrs);

        applyCustomFont(context);
    }

    public PingFangTextView(Context context, AttributeSet attrs, int defStyle) {
        super(context, attrs, defStyle);

        applyCustomFont(context);
    }

    private void applyCustomFont(Context context) {
        Typeface customFont = FontCache.getTypeface("fonts/PingFang Regular.ttf", context);
        setTypeface(customFont);
    }
}
```

