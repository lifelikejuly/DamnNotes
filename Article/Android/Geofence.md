## Google优化
考虑在您的应用接收位置更新不频繁的情况下其后台运行用例是否根本无法成功。如果属于这种情况，您可以通过执行下列操作之一提高位置更新的检索频率：

将您的应用转至前台。
使用应用中的某个前台服务。激活此服务时，您的应用必须在通知区显示一个持续性的通知。
使用 Geofencing API 的元素（例如 GeofencingApi 接口），这些元素针对最大限度减少耗电进行了专门优化。
使用被动位置侦听器，它可以在后台应用加快位置请求频率时提高位置更新的接收频率。

> 如果您的应用需要访问的位置历史记录包含时间频繁更新，请使用批处理版本的 Fused Location Provider API 元素，例如 FusedLocationProviderApi 接口。当您的应用运行于后台时，此 API 会以高于非批处理版本 API 的频率接收用户的位置。但切记，您的应用批量接收更新的频率仍仅为每小时几次。

## 谷歌原生接口方法
### Geofencing （GeofencingClient）
* 后台应用可以高于接收 Fused Location Provider 更新的频率接收地理围栏转换事件。
* 地理围栏事件的平均响应时间是大约每两分钟一次。

(所以地理围栏响应频率是最短在两分钟一次)

### Fused Location Provider (FusedLocationProviderClient)
如果您的应用运行在后台，位置系统服务只会根据 Android 8.0 行为变更中定义的间隔，按每小时几次的频率为其计算新位置。即使您的应用请求进行更频繁的位置更新，也仍是如此

> Note: On Android 8.0 (API level 26) and higher, if an app is running in the background when it requests the current location, then the device calculates the location only a few times each hour. To learn how to adapt your app to these calculation limits, see Background Location Limits.

(所以更新自己的位置信息，一小时内只有几次)



官方Demo地址 https://github.com/googlesamples/android-play-location


（增加前台通知达到常驻后台监听实时定位信息）

## 高德SDK

SDK V3.2.0 版本后支持
启动的定位机制：通过“远离围栏时的超低频定位策略”来降低电量消耗，“离近围栏会逐渐提高定位频率”，保证有足够的定位精度来完成围栏位置检测。
需要注意，如果您希望程序在后台持续检测围栏触发行为，您务必要在应用本地服务中启动 GeoFenceClient，且确保这个本地服务一直存活。

从Android 8.0开始系统为实现降低功耗，对后台应用获取用户位置信息频率进行了限制，每小时只允许更新几次位置信息，详细信息请参考官方说明。按照官方指引，如果要提高位置更新频率，需要后台应用提供一个前台服务通知告知。如果您需要自己参考官方指引来完成设置，可以参考之前我们提供的github示例。