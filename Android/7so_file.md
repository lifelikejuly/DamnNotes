# so文件找不到
原本项目做了gradle升级之后so文件找不到了！妈蛋明明在jniLibs目录下放置了so文件呀，见鬼了！
```
 loadLibrary msctuya error:java.lang.UnsatisfiedLinkError: dalvik.system.PathClassLoader[DexPathList[[zip file "/data/app/com.test.speech-1/base.apk", zip file "/data/app/com.test.speech-1/split_lib_dependencies_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_0_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_1_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_2_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_3_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_4_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_5_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_6_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_7_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_8_apk.apk", zip file "/data/app/com.test.speech-1/split_lib_slice_9_apk.apk"],nativeLibraryDirectories=[/data/app/com.test.speech-1/lib/x86, /data/app/com.test.speech-1/base.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_dependencies_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_0_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_1_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_2_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_3_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_4_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_5_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_6_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_7_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_8_apk.apk!/lib/x86, /data/app/com.test.speech-1/split_lib_slice_9_apk.apk!/lib/x86, /system/lib, /vendor/lib]]] couldn't find "libmsctuya.so"

```
然后找了半天资料并试验之后知道了问题出在哪里。
## 原因
AS下在Project选项卡中so文件应该是放置在libs下的，Android选项卡是放置在jniLibs下。另外主项目路径下so放置在根目录libs中而不是src/jniLibs下面
>我造成问题的原因在于Project选项卡我的so放在jniLibs中。

# 参考
* https://blog.csdn.net/smallwei2014/article/details/74475105
