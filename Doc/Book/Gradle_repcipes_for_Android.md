## Grdale for Android Basics 
Gradle is astate-of-the-art API.
Android Studio应用构建支持Gradle并且官方推出AndroidGradlePlugin插件功能让开发者进行自定义配置Gradle构建自己的应用。
[官方Gradle构建介绍](https://developer.android.com/studio/build/)
构建一个Android应用会生成settings.gradle,build.gradle,app/build.gradle三个gradle配置相关文件。
- settings.gradle 
配置应用子目录功能
```
include ':app"
```
- build.gradle
com.android.tools.build:gradle支持Android的gradle配置功能插件
buildscript用于编写配置插件下载的源仓库地址，通常jcenter是必备的还有自定义的私有Maven库以及Google仓库等。
allprojects配置包括顶级项目和子目录项目的仓库地址。
```
buildscript {
    repositories {
        jcenter() 
    }

    dependencies {
        classpath 'com.android.tools.build:gradle:2.0.0'
        // NOTE: Do not place your application dependencies here; they belong
        // in the individual module build.gradle files
    } 
}
allprojects {
    repositories {
        jcenter()   
    }
}
task clean(type: Delete) {
    delete rootProject.buildDir
}
```
- app/build.gradle
apply plugin配置使用的插件
compileSdkVersion 编译使用的版本号
targetSdkVersion是Android提供向前兼容的主要依据，表明该application已经兼容从minSdkVersion至tartgetSdkVersion之间所有api的变化。
minSdkVersion <= targetSdkVersion <= compileSdkVersion
versionName规范 < major >.< minor .< version > string
```
apply plugin: 'com.android.application'
android {
    compileSdkVersion 23
    buildToolsVersion "23.0.3"
    defaultConfig {
        applicationId "com.kousenit.myandroidapp"
        minSdkVersion 19
        targetSdkVersion 23
        versionCode 1
        versionName "1.0"
    }
    buildTypes {
        release {
            minifyEnabled false
            proguardFiles getDefaultProguardFile('proguard-android.txt'),
            'proguard-rules.pro'
        } 
    }
}

dependencies {
    compile fileTree(dir: 'libs', include: ['*.jar'])
    testCompile 'junit:junit:4.12'
    compile 'com.android.support:appcompat-v7:23.3.0'
}

```
- gradle-wrapper.properties
配置gradle版本
gradlew脚本，可以编写自己的gradle脚本自定义task
```
#Mon Dec 28 10:00:20 PST 2015
distributionBase=GRADLE_USER_HOME
distributionPath=wrapper/dists
zipStoreBase=GRADLE_USER_HOME
zipStorePath=wrapper/dists distributionUrl=https\://services.gradle.org/distributions/gradle-2.10-all.zip
```
类似task
```
./graldlew anDep 等于:app:androidDependencies
```
