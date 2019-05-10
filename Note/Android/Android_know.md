# 知识点汇总

## 热启动和冷启动
* https://www.cnblogs.com/xunzhi/p/5794793.html
冷启动：后台没有该应用进程存在，Zygote会fork进程启动应用
热启动：后台有该应用进程
冷启动优化
* https://github.com/DanluTeam/ColdStart
## Handler
* https://www.jianshu.com/p/b4d745c7ff7a
## Binder
* http://gityuan.com/2014/01/01/binder-gaishu/


## View绘制过程

* https://segmentfault.com/a/1190000018702726


## RecyclerView和ListView

* https://segmentfault.com/a/1190000007331249

## 自定义控件指南
* https://www.gcssloop.com/category/customview

## 常用设计模式

### 单例模式
* http://www.cs.umd.edu/~pugh/java/memoryModel/DoubleCheckedLocking.html
* https://juejin.im/entry/58c2085261ff4b005d85ab4b
## 线程池、线程
* https://segmentfault.com/a/1190000015156978

## Gradle
* https://www.jianshu.com/p/2d887a9b69fb
## Gradle编译过程
* https://cloud.tencent.com/developer/article/1032349

## 混淆
* https://www.jianshu.com/p/e9d3c57ab92f?utm_campaign=haruki&utm_content=note&utm_medium=reader_share&utm_source=qq

## 进程保活
* https://www.cnblogs.com/dongweiq/p/5404331.html
* https://www.jianshu.com/p/dd01580743e7
* https://sq.163yun.com/blog/article/193428201588768768

## 模块化开发、组件化开发

* https://www.cnblogs.com/baronzhang/p/6861258.html

## mvc、mvp、mvvm
* https://www.zhihu.com/question/20148405
* https://www.jianshu.com/p/fc814a20c452
* https://blog.csdn.net/u012317510/article/details/80247756
## AIDL
* https://mp.weixin.qq.com/s?__biz=MzI4MTQyNDg3Mg==&mid=2247485680&idx=1&sn=676929493d35aa51d209b261f23ec285&chksm=eba82e77dcdfa761c4a799b68a828e374a52b593df33a1720bedc58ccc7a2cd45b61e9df64d0&mpshare=1&scene=1&srcid=&key=5472509901065d05799dc34958fe85dd1b94d1cfff673831f1c34938ba039f303735fe20f7c45147da4caadf8ba5c673e78badb4f0abe008d3e1bb3234044bd624157d61a3b143bfa41355dd386492ac&ascene=0&uin=MTA4NjMyMjQw&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.12.5+build(16F73)&version=12020710&nettype=WIFI&lang=zh_CN&fontScale=100&pass_ticket=8CQ0XUVEW3wgzNbJnC65SEOExJLmxNCyaNO18Px4eUM%3D
## 性能优化
启动速度优化、布局优化、内存优化、电量优化、apk大小优化、列表滑动优化

## Dalvik和ART
* https://www.jianshu.com/p/58f817d176b7


## Android中为什么主线程不会因为Looper.loop()里的死循环卡死

* https://www.zhihu.com/question/34652589

# 优化工具
traceView、Systrace、GPU过度绘制、LeakCanary、Lint

## LeakCanary
Debug.dumpHprofData获取Dump文件分析日志
* https://www.jianshu.com/p/261e70f3083f

（OOM、GC、CRASH）

## 图片压缩 
三级缓存 LRU

## IM



### OkHttp
拦截器
* https://github.com/whataa/pandora/blob/master/pandora-core/src/main/java/tech/linjiang/pandora/network/OkHttpInterceptor.java


## hybrid
* https://www.cnblogs.com/yexiaochai/p/4921635.html
* https://github.com/wendux/DSBridge-Android
* https://github.com/yexiaochai/hybrid

## webview相关
webview安全性：@JavaScriptInterface 和 白名单 url
* https://developer.android.google.cn/guide/webapps/webview



## 注解器
jdk 1.6引入的Java Annotation Processing Tool

## IOC

## surfaceview和textureview  
* https://chriszeng87.iteye.com/blog/2219159

## AMS

## WMS

## 应用打包流程
* https://www.cnblogs.com/sjm19910902/p/6416022.html
* https://www.cnblogs.com/xunbu7/p/7345912.html
* https://juejin.im/entry/58b78d1b61ff4b006cd47e5b

## 应用启动流程
* https://www.jianshu.com/p/12de32b31836

## 应用安装流程
* https://blog.csdn.net/thinkinwm/article/details/81286264
* https://blog.csdn.net/u012267215/article/details/88313259

## multidex打包

* https://segmentfault.com/a/1190000004053072

## kotlin语法糖
空判断、构造函数、扩展函数、
* https://www.runoob.com/kotlin/kotlin-loop-control.html



## view更新UI真的不能在子线程吗
* https://www.jianshu.com/p/5d1cb4548630

## Web页面跳转应用内部

* https://blog.csdn.net/qq_30740239/article/details/51969660

## scheme协议
* https://www.cnblogs.com/whoislcj/p/5825333.html

## fragment重叠问题
* https://www.jianshu.com/p/99ab423ae24f

## rtsp方案
* https://blog.csdn.net/findsafety/article/details/72637795

## 相机相关
* https://www.jianshu.com/p/779c3dc775e9

## opencv

## Hook
### Xposed
```
XposedHelpers.findAndHookMethod(ClipboardManager.class, "setPrimaryClip",
                ClipData.class, new XC_MethodHook(){
                    beforeHookedMethod()
                    afterHookedMethod()
                }
```

## 多媒体
* https://blog.51cto.com/ticktick/2046899
### 录音

### 摄像


