#### ACTION_ACCESSIBILITY_SETTINGS

跳转系统的辅助功能界面

```java
Intent intent =  new Intent(Settings.ACTION_ACCESSIBILITY_SETTINGS);  
startActivity(intent);
```



#### ACTION_ADD_ACCOUNT

显示添加帐户创建一个新的帐户屏幕。【测试跳转到微信登录界面】

```java
Intent intent =  new Intent(Settings.ACTION_ADD_ACCOUNT);  
startActivity(intent);
```



#### ACTION_AIRPLANE_MODE_SETTINGS

飞行模式，无线网和网络设置界面

```java
Intent intent =  new Intent(Settings.ACTION_AIRPLANE_MODE_SETTINGS);  
startActivity(intent);
```



#### ACTION_WIRELESS_SETTINGS

无线网和网络设置界面

```java
Intent intent =  new Intent(Settings.ACTION_WIFI_SETTINGS);  
startActivity(intent);
```



#### ACTION_APN_SETTINGS

跳转 APN设置界面

```java
Intent intent =  new Intent(Settings.ACTION_APN_SETTINGS);  
startActivity(intent);
```



#### ACTION_APPLICATION_DETAILS_SETTINGS

根据包名跳转到系统自带的应用程序信息界面

```java
Uri packageURI = Uri.parse("package:" + "com.tencent.WBlog");
Intent intent =  new Intent(Settings.ACTION_APPLICATION_DETAILS_SETTINGS,packageURI);  
startActivity(intent);
```



#### ACTION_APPLICATION_DEVELOPMENT_SETTINGS

跳转开发人员选项界面

```java
Intent intent =  new Intent(Settings.ACTION_APPLICATION_DEVELOPMENT_SETTINGS);  
startActivity(intent);
```



#### ACTION_APPLICATION_SETTINGS

跳转应用程序列表界面

```java
Intent intent =  new Intent(Settings.ACTION_APPLICATION_SETTINGS);  
startActivity(intent);
```



#### ACTION_MANAGE_ALL_APPLICATIONS_SETTINGS

跳转到应用程序界面【所有的】

```java
Intent intent =  new Intent(Settings.ACTION_MANAGE_ALL_APPLICATIONS_SETTINGS);  
startActivity(intent); 
```



#### ACTION_MANAGE_APPLICATIONS_SETTINGS

跳转 应用程序列表界面【已安装的】

```java
Intent intent =  new Intent(Settings.ACTION_MANAGE_APPLICATIONS_SETTINGS);  
startActivity(intent);
```



#### ACTION_BLUETOOTH_SETTINGS

跳转系统的蓝牙设置界面

```java
Intent intent =  new Intent(Settings.ACTION_BLUETOOTH_SETTINGS);  
startActivity(intent);
```



#### ACTION_DATA_ROAMING_SETTINGS

跳转到移动网络设置界面

```java
Intent intent =  new Intent(Settings.ACTION_DATA_ROAMING_SETTINGS);  
startActivity(intent);
```



#### ACTION_DATE_SETTINGS

跳转日期时间设置界面

```java
Intent intent =  new Intent(Settings.ACTION_DATA_ROAMING_SETTINGS);  
startActivity(intent);
```



#### ACTION_DEVICE_INFO_SETTINGS

跳转手机状态界面

```java
Intent intent =  new Intent(Settings.ACTION_DEVICE_INFO_SETTINGS);  
startActivity(intent);
```



#### ACTION_DISPLAY_SETTINGS

跳转手机显示界面

```java
Intent intent =  new Intent(Settings.ACTION_DISPLAY_SETTINGS);  
startActivity(intent);
```



#### ACTION_DREAM_SETTINGS

```java
Intent intent =  new Intent(Settings.ACTION_DREAM_SETTINGS);  
startActivity(intent);
```



