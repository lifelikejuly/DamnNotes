# Android桌面插件开发的问题
安卓使用AppWidgetProvider桌面插件，桌面布局RemoteViews做了一些限制。在开发中发现加载网络大图时出现崩溃现象。
```
java.lang.IllegalArgumentException: RemoteViews for widget update exceeds maximum bitmap memory usage (used: 25165824, max: 12441600)
```
