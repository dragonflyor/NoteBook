

###1.三大核心
    SDKInitializer
    MapView
    BaiduMap

----
###2.准备工作
    获取API Key
    建立工程
    添加地图引擎到Andoid工程中
    添加权限
    初始化地图引擎
    引入布局（地图控件）

#######2.1获取APIkey   
    获取API Key
    地址：http://developer.baidu.com/map/android-mobile-apply-key.htm

		1、打开命令行输入cd .android 进入到android签名目录 如：C:\Users\lenovo\.android
		2、命令 "C:\Program Files\Java\jdk1.6.0_45\bin\keytool.exe" -list -v -keystore debug.keystore 密码是android
		数字签名;包名 分号是英文的  
		例如：48:8C:D9:43:56:81:C4:10:10:40:B6:C3:0A:A1:84:A9:65:65:9A:5A;baidumapsdk.demo
		生成的key：V3ITMGba32313GGCLFLALQdP

#######2.2添加地图引擎到Andoid工程中
    添加引擎到Andoid工程中
    添加jar包： baidumapapi_v3_3_0.jar和locSDK_5.0.jar
    添加.so文件：拷贝libBaiduMapSDK_v3_3_0_15.so 、 liblocSDK5.so到libs\armeabi目录下
    注： locSDK_5.0.jar和liblocSDK5.so为百度定位SDK所使用资源，开发者可根据实际需求自行添加。 

#######2.3权限设置
    <!-- 这个权限用于进行网络定位-->
    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION"></uses-permission>
    <!-- 这个权限用于访问GPS定位-->
    <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"></uses-permission>
    <!-- 用于访问wifi网络信息，wifi信息会用于进行网络定位-->
    <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"></uses-permission>
    <!-- 获取运营商信息，用于支持提供运营商信息相关的接口-->
    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE"></uses-permission>
    <!-- 这个权限用于获取wifi的获取权限，wifi信息会用来进行网络定位-->
    <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"></uses-permission>
    <!-- 用于读取手机当前的状态-->
    <uses-permission android:name="android.permission.READ_PHONE_STATE"></uses-permission>
    <!-- 写入扩展存储，向扩展卡写入数据，用于写入离线定位数据-->
    <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE"></uses-permission>
    <!-- 访问网络，网络定位需要上网-->
    <uses-permission android:name="android.permission.INTERNET" />
    <!-- SD卡读取权限，用户写入离线定位数据-->
    <uses-permission android:name="android.permission.MOUNT_UNMOUNT_FILESYSTEMS"></uses-permission>
    <!--允许应用读取低级别的系统日志文件 -->
    <uses-permission android:name="android.permission.READ_LOGS"></uses-permission>

#######2.4初始化地图引擎
	增加权限重点是处理位置信息权限
	Layout中添加MapView控件用于展示地图
	创建管理工具，并初使化
	SDKInitializer在initialize时校验key(permission check error)和网络状态(network error)，关于状态码信息我们可以在SDKInitializer查询
	注意：
	控制MapView的onResume、onPause、onDestroy ；
	SDKInitializer对象创建一个就可以；
	必须校验key，并且key值不能为空；
	Initialize方法接受的参数必须是global Application，不能传递Activity。

	代码：
	SDKInitializer.initialize(getApplicationContext()) ;

---

###3.注册广播接收者，监听网络状态 和 校验结果
	private void managerInit() {
		// TODO Auto-generated method stub
		SDKInitializer.initialize(getApplicationContext()) ;
		baiduSdkReceiver = new MyBaiduSdkReceiver();
		IntentFilter filter = new  IntentFilter();
		filter.addAction(SDKInitializer.SDK_BROADCAST_ACTION_STRING_NETWORK_ERROR);
		filter.addAction(SDKInitializer.SDK_BROADTCAST_ACTION_STRING_PERMISSION_CHECK_ERROR);
		//注册
		registerReceiver(baiduSdkReceiver, filter);
	}
	
	class MyBaiduSdkReceiver extends BroadcastReceiver {

		@Override
		public void onReceive(Context arg0, Intent arg1) {
			// TODO Auto-generated method stub
			String result = arg1.getAction();
			if(result.equals(SDKInitializer.SDK_BROADCAST_ACTION_STRING_NETWORK_ERROR)){
				//网络错误
				Toast.makeText(HelloWorld.this, "网络连接错误", 0).show();
			}else if(result.equals(SDKInitializer.SDK_BROADTCAST_ACTION_STRING_PERMISSION_CHECK_ERROR)){
				//校验失败
				Toast.makeText(HelloWorld.this, "百度地图服务校验失败", 0).show();
			}
		}
		
	}
