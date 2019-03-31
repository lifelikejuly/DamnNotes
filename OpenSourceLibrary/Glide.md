# Glide
> 4.9.0
## Use
```
ImageView imageView = new ImageView(this);
Glide.with(this)
        .load(URL)
        .into(imageView);
```

## Init
Glide的with方法传参可以是Activity、FramentActivity、Fragment、Context、View，通过传递以上对象获取到RequestManagerRetriever对象。
```
Glide.get(context).getRequestManagerRetriever()
```
在获取RequestManagerRetriever之前初始化Glide，在初始方法中省略跳过关于GeneratedAppGlideModule相关的内容，直接看GlideBuilder的对象。
```
private static void initializeGlide(@NonNull Context context) {
    initializeGlide(context, new GlideBuilder());
}
private static void initializeGlide(@NonNull Context context, @NonNull GlideBuilder builder) {
        ...
        // builder对象创建glide
        Glide glide = builder.build(applicationContext);
    for (com.bumptech.glide.module.GlideModule module : manifestModules) {
      module.registerComponents(applicationContext, glide, glide.registry);
    }
    if (annotationGeneratedModule != null) {
      annotationGeneratedModule.registerComponents(applicationContext, glide, glide.registry);
    }
    applicationContext.registerComponentCallbacks(glide);
    Glide.glide = glide;
}
```
在GlideBuilder中创建各种功能接口：Bitmap池、Array池、内存缓存、资源线程池、本地缓存线程池、请求配置和请求管理工厂。
### Class
-  RequestManager



# Reference
* https://blog.csdn.net/sinyu890807/column/info/15318
* https://github.com/bumptech/glide

