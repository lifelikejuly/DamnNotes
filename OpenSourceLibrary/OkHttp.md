# Sample
以前读过OkHttp的源码，重新再简单回顾一下可能有不同的感受。
```
        OkHttpClient okHttpClient = new OkHttpClient.Builder().build();
        Request request = new Request.Builder()
                .url("https://api.github.com/")
                .build();
        okHttpClient.newCall(request)
                .enqueue(new okhttp3.Callback() {
                    @Override
                    public void onFailure(okhttp3.Call call, IOException e) {
                    }
                    @Override
                    public void onResponse(okhttp3.Call call, okhttp3.Response response) throws IOException {
                    }
                });

        try {
            Response response = okHttpClient.newCall(request)
                    .execute();
            response.body();
        } catch (IOException e) {
            e.printStackTrace();
        }
```
## OkHttpClient
Http请求的主体对象通过Builder模式,通过看Buidler对象大致知道OkHttp网络请求库的基本可配置项。常用的配置项例如：连接超时时间、读超时时间、写超时时间、dns设置、proxy设置等。
```
public static final class Builder {
    Dispatcher dispatcher;//分发器
    @Nullable Proxy proxy;//代理
    List<Protocol> protocols;.//
    List<ConnectionSpec> connectionSpecs;
    final List<Interceptor> interceptors = new ArrayList<>();//拦截器集合
    final List<Interceptor> networkInterceptors = new ArrayList<>();//拦截器集合
    EventListener.Factory eventListenerFactory;
    ProxySelector proxySelector;
    CookieJar cookieJar;//Cookie管理
    @Nullable Cache cache;//缓存
    @Nullable InternalCache internalCache;
    SocketFactory socketFactory;
    @Nullable SSLSocketFactory sslSocketFactory;//
    @Nullable CertificateChainCleaner certificateChainCleaner;//证书
    HostnameVerifier hostnameVerifier;//主机名验证
    CertificatePinner certificatePinner;
    Authenticator proxyAuthenticator;
    Authenticator authenticator;
    ConnectionPool connectionPool;
    Dns dns;
    boolean followSslRedirects;
    boolean followRedirects;
    boolean retryOnConnectionFailure;
    int connectTimeout;//连接超时时间
    int readTimeout;//读时间
    int writeTimeout;//写时间
    int pingInterval;//Socket连接Ping间隔时间
}
```
## Request
Request是做Http请求类，同样和OkHttpClient使用Builder模式实例化。可配置项目：请求的Url、方法名（Post/Get/等）、请求头、请求body对象。
```
public static class Builder {
    @Nullable HttpUrl url;
    String method;
    Headers.Builder headers;
    @Nullable RequestBody body;

    /** A mutable map of tags, or an immutable empty map if we don't have any. */
    Map<Class<?>, Object> tags = Collections.emptyMap();
}
```
## RealCall
实例化OkHttpClient和Request对象，通过OkhttpClient的newRealCall方法执行网络请求，其中是有RealCall去执行该请求。
```
RealCall.newRealCall(this, request, false /* for web socket */);
```
执行网络请求有两只方式：execute、enqueue。第一种execute执行网络请求（请求是在当前线程进行），第二种则是添加到队列中等待请求是由线程池托管执行。需要注意的是这两种方式的执行是由dispatcher分发器负责处理，将RealCall请求添加到dispatcher中的Deque队列中等待执行。
- execute
dispatcher接收一个RealCall对象添加到runningSyncCalls同步执行队列中
```
/** Used by {@code Call#execute} to signal it is in-flight. */
  synchronized void executed(RealCall call) {
    runningSyncCalls.add(call);
  }
```
- enqueue
dispatcher接收一个RealCall对象添加到readyAsyncCalls异步准备执行队列中，然后执行promoteAndExecute方法查看runningAsyncCalls队列的任务执行情况。
```
void enqueue(AsyncCall call) {
    synchronized (this) {
      readyAsyncCalls.add(call);

      // Mutate the AsyncCall so that it shares the AtomicInteger of an existing running call to
      // the same host.
      if (!call.get().forWebSocket) {
        AsyncCall existingCall = findExistingCallWithHost(call.host());
        if (existingCall != null) call.reuseCallsPerHostFrom(existingCall);
      }
    }
    promoteAndExecute();
  }
```
遍历readyAsyncCalls队列的等待任务判断在执行的runningAsyncCalls队列是否满负荷工作状态若可以添加新请求则将readyAsyncCalls队列的任务添加到runningAsyncCalls并遍历AsyncCall集合去执行任务。
```
private boolean promoteAndExecute() {
    assert (!Thread.holdsLock(this));

    List<AsyncCall> executableCalls = new ArrayList<>();
    boolean isRunning;
    synchronized (this) {
      for (Iterator<AsyncCall> i = readyAsyncCalls.iterator(); i.hasNext(); ) {
        AsyncCall asyncCall = i.next();

        if (runningAsyncCalls.size() >= maxRequests) break; // Max capacity.
        if (asyncCall.callsPerHost().get() >= maxRequestsPerHost) continue; // Host max capacity.

        i.remove();
        asyncCall.callsPerHost().incrementAndGet();
        executableCalls.add(asyncCall);
        runningAsyncCalls.add(asyncCall);
      }
      isRunning = runningCallsCount() > 0;
    }

    for (int i = 0, size = executableCalls.size(); i < size; i++) {
      AsyncCall asyncCall = executableCalls.get(i);
      asyncCall.executeOn(executorService());
    }

    return isRunning;
  }
```
- AsyncCall
异步执行是会将RealCall封装成AsyncCall并增加responseCallback返回请求结果，然后再看AsyncCall的executeOn方法，请求任务会在ExecutorService去执行。
```
void executeOn(ExecutorService executorService) {
      assert (!Thread.holdsLock(client.dispatcher()));
      boolean success = false;
      try {
        executorService.execute(this);
        success = true;
      } catch (RejectedExecutionException e) {
        InterruptedIOException ioException = new InterruptedIOException("executor rejected");
        ioException.initCause(e);
        transmitter.noMoreExchanges(ioException);
        responseCallback.onFailure(RealCall.this, ioException);
      } finally {
        if (!success) {
          client.dispatcher().finished(this); // This call is no longer running!
        }
      }
    }
```
下面是AsyncCall负责返回执行结果Response的方法。在返回结果前还有一层getResponseWithInterceptorChain方法。这是OkHttp框架另外一个特性：结果拦截器，拦截器的内容放在后面再做介绍。
```
 @Override protected void execute() {
      boolean signalledCallback = false;
      transmitter.timeoutEnter();
      try {
        Response response = getResponseWithInterceptorChain();
        signalledCallback = true;
        responseCallback.onResponse(RealCall.this, response);
      } catch (IOException e) {
        if (signalledCallback) {
          // Do not signal the callback twice!
          Platform.get().log(INFO, "Callback failure for " + toLoggableString(), e);
        } else {
          responseCallback.onFailure(RealCall.this, e);
        }
      } finally {
        client.dispatcher().finished(this);
      }
    }
```
## connection
- CallServerInterceptor
- ConnectInterceptor
- Exchange
- Http1ExchangeCodec
- Http2ExchangeCodec
- RealBufferedSink
# Reference
* https://blog.piasy.com/2016/07/11/Understand-OkHttp/
* http://blog.allinfish.com/2018/02/07/%E8%AF%BB%E8%AF%BBOkHttp%E6%BA%90%E7%A0%81/
* https://github.com/square/okhttp