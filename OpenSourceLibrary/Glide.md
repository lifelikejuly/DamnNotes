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

## With
Glide初始化获取到requestManagerRetriever然后再通过requestManagerRetriever获取到RequestManager。在这个过程中会创建SupportRequestManagerFragment，它是一个fragment对象。其中会有一个fragment的Map缓存之前的fragment，按需获取到之前的fragment调用onStart方法来重新启动。
```
private SupportRequestManagerFragment getSupportRequestManagerFragment(
      @NonNull final FragmentManager fm, @Nullable Fragment parentHint, boolean isParentVisible) {
    SupportRequestManagerFragment current =
        (SupportRequestManagerFragment) fm.findFragmentByTag(FRAGMENT_TAG);
    if (current == null) {
      current = pendingSupportRequestManagerFragments.get(fm);
      if (current == null) {
        current = new SupportRequestManagerFragment();
        current.setParentFragmentHint(parentHint);
        if (isParentVisible) {
          current.getGlideLifecycle().onStart();
        }
        pendingSupportRequestManagerFragments.put(fm, current);
        fm.beginTransaction().add(current, FRAGMENT_TAG).commitAllowingStateLoss();
        handler.obtainMessage(ID_REMOVE_SUPPORT_FRAGMENT_MANAGER, fm).sendToTarget();
      }
    }
    return current;
  }
```
那fragment的作用是什么呢？核心部分就在fragment中的RequestManager成员对象。大概也能知道Glide的图片请求是通过RequestManager去实现的。
```
 @NonNull
  private RequestManager supportFragmentGet(
      @NonNull Context context,
      @NonNull FragmentManager fm,
      @Nullable Fragment parentHint,
      boolean isParentVisible) {
    SupportRequestManagerFragment current =
        getSupportRequestManagerFragment(fm, parentHint, isParentVisible);
    RequestManager requestManager = current.getRequestManager();
    //获取到SupportRequestManagerFragment后判断它的requestManager是否为空
    if (requestManager == null) {
      // TODO(b/27524013): Factor out this Glide.get() call.
      Glide glide = Glide.get(context);
      //若requestManager为空则通过RequestManagerFactory创建新的
      requestManager =
          factory.build(
              glide, current.getGlideLifecycle(), current.getRequestManagerTreeNode(), context);
      current.setRequestManager(requestManager);
    }
    return requestManager;
  }
```

## Load
RequestManager调用load方法加载资源，通过RequestBuilder实例请求对象。

## Into

核心部分建立Request对象
```
Request request = buildRequest(target, targetListener, options, callbackExecutor);
```




## Class
### GlideBuilder
- sourceExecutor
- diskCacheExecutor
- animationExecutor
- memorySizeCalculator
- connectivityMonitorFactory
- bitmapPool
- arrayPool
- memoryCache
- diskCacheFactory
- engine
- requestManagerRetriever

### RequestManagerRetriever

### RequestManager

### RequestManagerFragment
是一个fragment，
### SingleRequest

### Engine



# Reference
* https://blog.csdn.net/sinyu890807/column/info/15318
* https://github.com/bumptech/glide

