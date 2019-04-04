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
RequestManager调用load方法加载资源,通过加载String类型资源分析Requestmanager是如何处理资源。asDrawable()是RequestBuilder对象，通过它处理String地址资源最终希望获取到Drawable类型资源。
```
  @NonNull
  @CheckResult
  @Override
  public RequestBuilder<Drawable> load(@Nullable String string) {
    return asDrawable().load(string);
  }
```
接着看它的load方法处理的是loadGeneric方法。配置传入的资源类型Model以及设置model已经装载的标记位。

## Into
装载资源之后最后就是将希望获取到的资源装载到希望的地方，通常做法当然是会把图片资源显示在ImageView上，into方法也是在RequestBuilder中,先找到into的入口方法,入参资源类型和线程池对象。
```
@NonNull
  public <Y extends Target<TranscodeType>> Y into(@NonNull Y target) {
    return into(target, /*targetListener=*/ null, Executors.mainThreadExecutor());
  }
```
在继续看代码会发现核心部分是建立Request对象，资源请求应该就是在这里了。
```
Request request = buildRequest(target, targetListener, options, callbackExecutor);
```
Request是接口真正实现它的是SingleRequest对象，实现了Request方法，资源的加载功能可以看begin方法实现。
```
@Override
  public synchronized void begin() {
    assertNotCallingCallbacks();
    stateVerifier.throwIfRecycled();
    startTime = LogTime.getLogTime();
    //如果资源为空则执行加载失败功能
    if (model == null) {
      if (Util.isValidDimensions(overrideWidth, overrideHeight)) {
        width = overrideWidth;
        height = overrideHeight;
      }
      // Only log at more verbose log levels if the user has set a fallback drawable, because
      // fallback Drawables indicate the user expects null models occasionally.
      int logLevel = getFallbackDrawable() == null ? Log.WARN : Log.DEBUG;
      onLoadFailed(new GlideException("Received null model"), logLevel);
      return;
    }

    if (status == Status.RUNNING) {
      throw new IllegalArgumentException("Cannot restart a running request");
    }

    // If we're restarted after we're complete (usually via something like a notifyDataSetChanged
    // that starts an identical request into the same Target or View), we can simply use the
    // resource and size we retrieved the last time around and skip obtaining a new size, starting a
    // new load etc. This does mean that users who want to restart a load because they expect that
    // the view size has changed will need to explicitly clear the View or Target before starting
    // the new load.
    if (status == Status.COMPLETE) {
      onResourceReady(resource, DataSource.MEMORY_CACHE);
      return;
    }

    // Restarts for requests that are neither complete nor running can be treated as new requests
    // and can run again from the beginning.

    status = Status.WAITING_FOR_SIZE;
    if (Util.isValidDimensions(overrideWidth, overrideHeight)) {
      onSizeReady(overrideWidth, overrideHeight);
    } else {
      target.getSize(this);
    }

    if ((status == Status.RUNNING || status == Status.WAITING_FOR_SIZE)
        && canNotifyStatusChanged()) {
      target.onLoadStarted(getPlaceholderDrawable());
    }
    if (IS_VERBOSE_LOGGABLE) {
      logV("finished run method in " + LogTime.getElapsedMillis(startTime));
    }
  }
```
省略多余代码分析正常的资源加载过程，首先执行target.onLoadStarted(getPlaceholderDrawable());先是显示占位图然后开始加载需要的资源。
```
@Override
  public void onLoadStarted(@Nullable Drawable placeholder) {
    super.onLoadStarted(placeholder);
    setResourceInternal(null);
    setDrawable(placeholder);
  }
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
是一个fragment，要知道Glide加载资源就是在fragment中进行的。下载资源为了知道当前上下文是否存在避免内存泄露的情况必须知道自己的生命周期。那么知道生命周期的办法就是通过在fragment去监听，通过添加隐藏的fragment来实现生命周期与资源加载上下文同步。例如当前加载资源的页面被销毁也能通知到该fragment的生命周期从而停止资源加载。
### SingleRequest

### Engine

### RequestFutureTarget



# Reference
* https://blog.csdn.net/sinyu890807/column/info/15318
* https://blog.csdn.net/songzi1228/article/details/84426165
* https://github.com/bumptech/glide

