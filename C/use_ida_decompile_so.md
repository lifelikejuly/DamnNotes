# 做个小偷
在Android项目中有时会用到so库，还想知道so里面具体的逻辑代码。这时候就需要一个反编译工具去满足我们贪婪的欲望。使用IDA可以满足这个需要。
# IDA Pro使用
> 这里使用的是Mac V7版本 ![下载地址](https://pan.baidu.com/s/1pLv9hov#list/path=%2F579910%20IDA_Pro_7_Mac)

* 打开IDA Pro软件
* 将需要反编译的so文件拖入工程      
打开之后可以看到so文件反编译后看到的页面如下
![](./file/ide_so_decompile.jpg)
IDA View-A呈现的是对应汇编代码；Function Window是各个函数方法；Hex View是展示的是机器码。
* 反编译成c文件需要用到hex-ray插件     
![](./file/use_hexray.jpg)
![](./file/decompile_to_c_file.jpg)
> 很多教程说使用F5就能反编译出c文件，但我好像没有成功过。

生成c文件成功后就能看到源码内容了
![](./C/file/the_ida_c_file.jpg)
对比原来Android项目中写的c文件
![](./file/the_jni_file.jpg)

# 附件
![原项目C文件](./file/com_learnncode_learnndk_JniFunction.c)
![原项目so文件](./file/libJniFunction.so)
![反编译C文件](./file/libJniFunction.c)
# 参考资料
* https://blog.csdn.net/lanyang123456/article/details/56497264
* https://blog.csdn.net/pengyan0812/article/details/43988171
* http://www.woodmann.com/crackz/Tutorials/Flores1.htm 
* https://pan.baidu.com/s/1pLv9hov#list/path=%2F579910%20IDA_Pro_7_Mac
* https://www.cnblogs.com/suliang-com/p/6866435.html        (Android项目中c文件生成so文件)
