# 问题
一个项目应用发生崩溃的时候导致当前应用创建启动，而应用并不是重启设置Launcher的Activity。

# 解决
发现重启的Activity设置了android:stateNotNeeded=true，从官网文档中找到后发现
> “true”设置可确保 Activity 能够在未保留状态时重新启动。 例如，显示主屏幕的 Activity 可以使用该设置来确保其由于某种原因崩溃时不会被移除。


# 参考
* https://developer.android.com/guide/topics/manifest/activity-element#state

