# 服务

## 一、服务基本用法
* 服务中的代码默认在主线程运行，如果需要在服务中处理耗时逻辑，就需要在服务的每个具体方法中开启子线程，并记得服务执行完毕后自动停止（stopSelf()）


1. 快捷方式创建服务，重写onCreate,onStartCommmand（启动服务时调用）,onDestory方法 手动创建需要在Manifest中声明
2. 在活动中通过intent启动或终止服务 startService(),stopService()


 ```java
 //MyService服务
  public class MyService extends Service {

    public MyService() { }

  @Override

  public IBinder onBind(Intent intent) {
   // TODO: Return the communication channel to the service.
   throw new UnsupportedOperationException("Not yet implemented");
  }

  @Override

  //创建服务时调用
  public void onCreate() {
   super.onCreate();
   Log.d("MyService","oncreate");
  }

  @Override
  //服务启动时调用
  public int onStartCommand(Intent intent, int flags, int startId) {
   Log.d("MyService","onstartCommand");
   return super.onStartCommand(intent, flags, startId);
  }


  @Override
  public void onDestroy() {
   super.onDestroy();
   Log.d("MyService","ondestory");
    }
 }

```


```java
//MainActivity
@Override
    public void onClick(View v) {
        switch (v.getId()){
            case R.id.start_service:
                Intent startIntent = new Intent(this,MyService.class);
                startService(startIntent);
                break;
            case R.id.stop_setvice:
                Intent stopIntent = new Intent(this,MyService.class);
                stopService(stopIntent);
                break;
            default:
                break;
        }
    }
````



## 二、活动与服务通信

1. 在服务内创建实现了想要的功能的类，获取实例
2. 实现服务内的onBind方法，返回服务内功能类的实例，作为对活动类开放的接口
3. 在活动中创建onServiceConnected类，重写onServiceConnected与onServiceDisconnected方法，这两个方法分别在活动与服务绑定与解绑成功时调用。在绑定成功方法中通过**向下转型**获取服务内功能类的实例并执行功能方法
4. 在活动中通过 `bindService(bindIntent, connection,BIND_AUTO_CREATE);` `unbindService(connection);` 实现活动与服务的绑定与解绑



```java
//MyService
public class MyService extends Service {

    private DownloadBinder mBinder = new DownloadBinder();

    class DownloadBinder extends Binder{

        public void startDownload(){
            Log.d("MyService","startDownload");
        }

        public int getProgress(){
            Log.d("MyService","getProgress");
            return 0;
        }

    }

    public MyService() {
    }

    @Override
    //面向活动的开放接口，提供服务内的功能类的实例
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        return mBinder;
        }

    @Override

    //创建服务时调用
    public void onCreate() {
        super.onCreate();
        Log.d("MyService","oncreate");
    }

    @Override
    //服务启动时调用
    public int onStartCommand(Intent intent, int flags, int startId) {
        Log.d("MyService","onstartCommand");
        return super.onStartCommand(intent, flags, startId);
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d("MyService","ondestory");
    }
}
```



```java
//MainActivity
    private MyService.DownloadBinder downloadBinder;

    private ServiceConnection connection = new ServiceConnection() {
        @Override
        //活动与服务成功绑定时调用
        public void onServiceConnected(ComponentName name, IBinder service) {
            //向下转型获取实例
            downloadBinder = (MyService.DownloadBinder)service;
            //执行服务内功能类的功能方法
            downloadBinder.startDownload();
            downloadBinder.getProgress();
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {
        }
    };
```



```java
//onClick
            case R.id.bind_service:
                //绑定活动与服务，第三个参数表示自动创建服务
                Intent bindIntent = new Intent(this, MyService.class);
                //调用bindService后回调服务中的onBind方法
                bindService(bindIntent, connection,BIND_AUTO_CREATE);
                break;
            case R.id.unbind_service:
                unbindService(connection);
                break;
```


## 三、使用前台服务

* 利用构建的通知实现系统状态栏显示前台服务


  1. 在服务中创建Intent作为通知的点击事件活动
  2. 包装Intent为PendingIntent
  3. 构建一条通知，加入包装好的pendingIntent
  4. startForeground(1,notification)启动前台，第一个参数是通知的id



```java
//MyService
Intent intent = new Intent(this,MainActivity.class);
        //作为通知的启动活动的点击事件，包装intent
        PendingIntent pi = PendingIntent.getActivity(this,0,intent,0);

        //构建一条通知
        Notification notification = new NotificationCompat.Builder(this)
                .setContentText("this is content text")
                .setContentTitle("title")
                .setWhen(System.currentTimeMillis())
                .setContentIntent(pi)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setLargeIcon(BitmapFactory.decodeResource(getResources(),R.mipmap.ic_launcher))
                .build();
        //显示通知的方法：用通知manager.notify()
        //这里是让服务变为一个前台(foreground)服务，并在系统状态栏显示出来
        startForeground(1,notification);//第一个参数是通知的id
```

## 四、最常用:IntentService

* 此服务在运行结束后自动停止
* 重写的方法onHandleIntent默认在子线程中运行

1. 创建MyIntentService继承自IntentService
2. 构造函数、重写onHanleIntent、onDestory等必要方法
3. 在Manifest中注册活动



```java
//MyIntentService
public class MyIntentService extends IntentService {

    public MyIntentService(){
        super("MyIntentService");
    }

    @Override
    //抽象方法，此方法在子线程中运行
    protected void onHandleIntent(@Nullable Intent intent) {
        Log.d("MyIntentService","Thread id is " + Thread.currentThread().getId());
    }

    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d("MyIntnetService","onDestory executed");
    }
}

```

```java
//onClick
case R.id.start_intent_service:
                Log.d("MainActivity","Thread id is " + Thread.currentThread().getId());
                Intent intentService = new Intent(this,MyIntentService.class);
                startService(intentService);
                break;

```

```xml
//Manifest, application标签内
<service android:name=".MyIntentService"/>
```
