# LogCollect
auth:坤哥
1、收集应用奔溃的log信息
 此工具主要及时收集产品log信息，上传到服务器，技术人员快速定位解决问题。主要收集error,ware,info（log.e(),System.out.println()）。
程序启动后，开始收集log信息，随后每2秒收集程序的log打印的信息
1.1>程序不奔溃
   技术可以进行一些产品流程操作，调用上传方法，就可以在服务器看到log信息
1.2>程序崩溃
   程序奔溃后，开启服务，log信息会自动上传到服务器,不用调用上传方法。
1.3>程序奔溃或者退出，储存在用户sd卡或者应用缓存的log信息会自动删除
2、使用
2.1 AndriodStudio 
2.1.1将logcollect.jar包放入libs目录下，依赖
2.1.2 App下的build.gradle里加入 compile 'org.xutils:xutils:3.3.36'
2.1.3创建一个BaseApplication，继承Application
  public class BaseApplication extends Application {
    public Context context;
    @Override
    public void onCreate() {
        super.onCreate();
        context = getApplicationContext();
        String url = "http://XXXXXXXXXXX";// 服务器地址
        x.Ext.init(this);//xutils3.3初始化
        //启动收集log日志
        LogcatHelper mLogcatHelper = LogcatHelper.getInstance(context,url);
        CrashHandler.getInstance().init(context, mLogcatHelper,url);
    }

}
2.1.4在AndroidManifest.xml清单文件添加权限，注册service
权限：
    <uses-permission android:name="android.permission.READ_PHONE_STATE" />
    <uses-permission android:name="android.permission.INTERNET" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS" />
    <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
    <uses-permission android:name="android.permission.READ_LOGS" />
  应用入口：
   <application
        android:name=".BaseApplication"
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
         <service android:name="com.winside.logcollector.service.UpLogService" />
    </application>
    
3、方法
 3.1 public static LogcatHelper getInstance(Context context,String url) 
接口功能：  创建奔溃前实例 
参数说明： 
Context 上下文
url     服务器地址
返回：对象实例 
3.2  public static CrashHandler getInstance() 
接口功能：  创建奔溃后实例 
        返回：对象实例 
3.3  public void init(Context context, LogcatHelper logHelper，String url) 
接口功能：  初始化，开始收集log
参数说明： 
Context      上下文
LogcatHelper 奔溃前实例
url          服务器地址 
       
3.4  com.winside.logcollector.capture.LogcatHelper
3.4.1  public void upLoadLog() {
      功能：上传log日志
       }
3.5 com.winside.logcollector.utils.DirFileUtils
3.5.1  public static void deleteLogFiles(Context context){
        功能：手动删除log文件
        如果应用没奔溃，一般在程序正常退出的时候，调用该方法
}

