# WIFI
安卓官方提供了Wifi系统服务接口。特别注意使用wifi功能要求申请权限并且ACCESS_FINE_LOCATION属于危险权限需要动态申请。wifi功能为什么需要定位权限，原因在于wifi也属于定位方式的一种，开启wifi可以提高定位精准度,所以在6.0以及高版本的系统记得对权限做判断处理。
```
 <uses-permission android:name="android.permission.ACCESS_WIFI_STATE"/>
 <uses-permission android:name="android.permission.CHANGE_WIFI_STATE"/>
 <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION"/>
```

- 常用接口说明
```
WifiManager wifiManager = (WifiManager) getSystemService(Context.WIFI_SERVICE); //获取Wifi系统服务
wifiManager.setWifiEnabled(boolean bool)//WiFi开关
wifiManager.startScan() //扫描WiFi
wifiManager.getScanResults() //获取WiFi列表
int netId = wifiManager.addNetwork(wifiConfiguration); //添加wifi并获取netId
wifiManager.disconnect();//断开wifi
wifiManager.reconnect();//重连wifi
wifiManager.enableNetwork(netId, true);//连接wifi
List<WifiConfiguration> existingConfigs = wifiManager.getConfiguredNetworks(); //获取wifi设置中已经连接的wifi信息
wifiManager.removeNetwork(netId);//忘记某个wifi连接
wifiManager.getConnectionInfo();//获取当前连接wifi信息，也可能是空
```
- WIFI状态变化监听
常用的状态监听如下，但在实际开发中发现NETWORK_STATE_CHANGED_ACTION并不是特别适用获取连接指定wifi的结果。例如想要判断连接某个wifi失败并没有连接失败的状态。
> 所以关于在连接指定wifi对象时自行做了一个超时处理若在设定时间内未连接上指定wifi则认定为连接失败。（目前未知原生API是否有提供这样的判断，暂且这样做处理）
```
WifiManager.SCAN_RESULTS_AVAILABLE_ACTION // wifi扫描结果获取
WifiManager.WIFI_STATE_CHANGED_ACTION //wifi状态变化监听（开启和关闭）
WifiManager.NETWORK_STATE_CHANGED_ACTION //网络状态变化监听(连接、连接中、断开连接等)
```
