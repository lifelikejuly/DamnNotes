# AppDatabaseCrash
由于项目框架采用模块化的形式，App项目工程主要为工程业务，Room依赖库集成自Core模块。在App项目中重新创建另一个数据库的时候遇到了匪夷所思的问题，编译通过，运行时涉及到该数据库的操作就会崩溃。
```
 Caused by: java.lang.RuntimeException: cannot find implementation for com.tuya.home.camera.wirelessCamera.db.WirelessDatabase. WirelessDatabase_Impl does not exist
```
database采用抽象类实现，Room框架库会在预编译期间由抽象类生成一个实现类database_Impl，然而在App工程未能找到刚类文件。
最终在app工程gradle依赖库重新加入下面的依赖才解决，个人猜测该依赖未做为全局依赖使用
```
 annotationProcessor  'android.arch.persistence.room:compiler:1.1.1'
```