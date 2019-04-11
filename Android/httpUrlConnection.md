# Sample
```

```

# URL
Android 4.4以上使用HttpURLConnection底层使用OkHttp实现
```
    private static URLStreamHandler createBuiltinHandler(String protocol)
            throws ClassNotFoundException, InstantiationException, IllegalAccessException {
        URLStreamHandler handler = null;
        if (protocol.equals("file")) {
            handler = new sun.net.www.protocol.file.Handler();
        } else if (protocol.equals("ftp")) {
            handler = new sun.net.www.protocol.ftp.Handler();
        } else if (protocol.equals("jar")) {
            handler = new sun.net.www.protocol.jar.Handler();
        } else if (protocol.equals("http")) {
            handler = (URLStreamHandler)Class.
                    forName("com.android.okhttp.HttpHandler").newInstance();
        } else if (protocol.equals("https")) {
            handler = (URLStreamHandler)Class.
                    forName("com.android.okhttp.HttpsHandler").newInstance();
        }
        return handler;
    }
```
## HttpHandler
okhttp.HttpsHandler的内部实现就是实例化一个OkHttpClient对象做网络请求。
```
public static OkUrlFactory createHttpOkUrlFactory(Proxy proxy) {
        OkHttpClient client = new OkHttpClient();

        // Explicitly set the timeouts to infinity.
        client.setConnectTimeout(0, TimeUnit.MILLISECONDS);
        client.setReadTimeout(0, TimeUnit.MILLISECONDS);
        client.setWriteTimeout(0, TimeUnit.MILLISECONDS);

        // Set the default (same protocol) redirect behavior. The default can be overridden for
        // each instance using HttpURLConnection.setInstanceFollowRedirects().
        client.setFollowRedirects(HttpURLConnection.getFollowRedirects());

        // Do not permit http -> https and https -> http redirects.
        client.setFollowSslRedirects(false);

        // Permit cleartext traffic only (this is a handler for HTTP, not for HTTPS).
        client.setConnectionSpecs(CLEARTEXT_ONLY);

        // When we do not set the Proxy explicitly OkHttp picks up a ProxySelector using
        // ProxySelector.getDefault().
        if (proxy != null) {
            client.setProxy(proxy);
        }

        // OkHttp requires that we explicitly set the response cache.
        OkUrlFactory okUrlFactory = new OkUrlFactory(client);

        // Use the installed NetworkSecurityPolicy to determine which requests are permitted over
        // http.
        okUrlFactory.setUrlFilter(CLEARTEXT_FILTER);

        ResponseCache responseCache = ResponseCache.getDefault();
        if (responseCache != null) {
            AndroidInternal.setResponseCache(okUrlFactory, responseCache);
        }
        return okUrlFactory;
    }
```
# 