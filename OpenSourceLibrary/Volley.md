
# Sample
使用的是apache网络请求库
```
// Instantiate the RequestQueue.
RequestQueue queue = Volley.newRequestQueue(this);
String url ="http://www.google.com";

// Request a string response from the provided URL.
StringRequest stringRequest = new StringRequest(Request.Method.GET, url,
            new Response.Listener<String>() {
    @Override
    public void onResponse(String response) {
        // Display the first 500 characters of the response string.
        textView.setText("Response is: "+ response.substring(0,500));
    }
}, new Response.ErrorListener() {
    @Override
    public void onErrorResponse(VolleyError error) {
        textView.setText("That didn't work!");
    }
});

// Add the request to the RequestQueue.
queue.add(stringRequest);
```
# Volley

## Request
网络请求类，设置请求类型、请求url、请求回调。
```
public Request(int method, String url, @Nullable Response.ErrorListener listener) {
        mMethod = method;
        mUrl = url;
        mErrorListener = listener;
        setRetryPolicy(new DefaultRetryPolicy());

        mDefaultTrafficStatsTag = findDefaultTrafficStatsTag(url);
    }
```
## RequestQueue
添加请求类，将请求加入到队列中。
```
public <T> Request<T> add(Request<T> request) {
        // Tag the request as belonging to this queue and add it to the set of current requests.
        request.setRequestQueue(this);
        synchronized (mCurrentRequests) {
            mCurrentRequests.add(request);
        }

        // Process requests in the order they are added.
        request.setSequence(getSequenceNumber());
        request.addMarker("add-to-queue");

        // If the request is uncacheable, skip the cache queue and go straight to the network.
        if (!request.shouldCache()) {
            mNetworkQueue.add(request);
            return request;
        }
        mCacheQueue.add(request);
        return request;
    }
```
## CacheDispatcher
CacheDispatcher是缓存调度线程，若Request请求如果需要缓存则判断请求队列中是否有已有请求，已存在相同请求就加入到mWaitiongRequests队列中,否则加入到网络请求队列中。
## NetworkDispatcher
NetworkDispatcher是一个网络调度Thread，处理请求队列的Request，具体的网络请求方法在BasicNetwork去处理。
## BasicNetwork
处理Request的方法
```
@Override
    public NetworkResponse performRequest(Request<?> request) throws VolleyError {
        long requestStart = SystemClock.elapsedRealtime();
        while (true) {
            HttpResponse httpResponse = null;
            byte[] responseContents = null;
            List<Header> responseHeaders = Collections.emptyList();
            try {
                // Gather headers.
                Map<String, String> additionalRequestHeaders =
                        getCacheHeaders(request.getCacheEntry());
                httpResponse = mBaseHttpStack.executeRequest(request, additionalRequestHeaders);
                int statusCode = httpResponse.getStatusCode();

                responseHeaders = httpResponse.getHeaders();
                // Handle cache validation.
                if (statusCode == HttpURLConnection.HTTP_NOT_MODIFIED) {
                    Entry entry = request.getCacheEntry();
                    if (entry == null) {
                        return new NetworkResponse(
                                HttpURLConnection.HTTP_NOT_MODIFIED,
                                /* data= */ null,
                                /* notModified= */ true,
                                SystemClock.elapsedRealtime() - requestStart,
                                responseHeaders);
                    }
                    // Combine cached and response headers so the response will be complete.
                    List<Header> combinedHeaders = combineHeaders(responseHeaders, entry);
                    return new NetworkResponse(
                            HttpURLConnection.HTTP_NOT_MODIFIED,
                            entry.data,
                            /* notModified= */ true,
                            SystemClock.elapsedRealtime() - requestStart,
                            combinedHeaders);
                }

                // Some responses such as 204s do not have content.  We must check.
                InputStream inputStream = httpResponse.getContent();
                if (inputStream != null) {
                    responseContents =
                            inputStreamToBytes(inputStream, httpResponse.getContentLength());
                } else {
                    // Add 0 byte response as a way of honestly representing a
                    // no-content request.
                    responseContents = new byte[0];
                }

                // if the request is slow, log it.
                long requestLifetime = SystemClock.elapsedRealtime() - requestStart;
                logSlowRequests(requestLifetime, request, responseContents, statusCode);

                if (statusCode < 200 || statusCode > 299) {
                    throw new IOException();
                }
                return new NetworkResponse(
                        statusCode,
                        responseContents,
                        /* notModified= */ false,
                        SystemClock.elapsedRealtime() - requestStart,
                        responseHeaders);
            } catch (SocketTimeoutException e) {
                attemptRetryOnException("socket", request, new TimeoutError());
            } catch (MalformedURLException e) {
                throw new RuntimeException("Bad URL " + request.getUrl(), e);
            } catch (IOException e) {
                int statusCode;
                if (httpResponse != null) {
                    statusCode = httpResponse.getStatusCode();
                } else {
                    throw new NoConnectionError(e);
                }
                VolleyLog.e("Unexpected response code %d for %s", statusCode, request.getUrl());
                NetworkResponse networkResponse;
                if (responseContents != null) {
                    networkResponse =
                            new NetworkResponse(
                                    statusCode,
                                    responseContents,
                                    /* notModified= */ false,
                                    SystemClock.elapsedRealtime() - requestStart,
                                    responseHeaders);
                    if (statusCode == HttpURLConnection.HTTP_UNAUTHORIZED
                            || statusCode == HttpURLConnection.HTTP_FORBIDDEN) {
                        attemptRetryOnException(
                                "auth", request, new AuthFailureError(networkResponse));
                    } else if (statusCode >= 400 && statusCode <= 499) {
                        // Don't retry other client errors.
                        throw new ClientError(networkResponse);
                    } else if (statusCode >= 500 && statusCode <= 599) {
                        if (request.shouldRetryServerErrors()) {
                            attemptRetryOnException(
                                    "server", request, new ServerError(networkResponse));
                        } else {
                            throw new ServerError(networkResponse);
                        }
                    } else {
                        // 3xx? No reason to retry.
                        throw new ServerError(networkResponse);
                    }
                } else {
                    attemptRetryOnException("network", request, new NetworkError());
                }
            }
        }
    }
```
## HttpClientStack
```
static HttpUriRequest createHttpRequest(
            Request<?> request, Map<String, String> additionalHeaders) throws AuthFailureError {
        switch (request.getMethod()) {
            case Method.DEPRECATED_GET_OR_POST:
                {
                    // This is the deprecated way that needs to be handled for backwards
                    // compatibility.
                    // If the request's post body is null, then the assumption is that the request
                    // is
                    // GET.  Otherwise, it is assumed that the request is a POST.
                    byte[] postBody = request.getPostBody();
                    if (postBody != null) {
                        HttpPost postRequest = new HttpPost(request.getUrl());
                        postRequest.addHeader(
                                HEADER_CONTENT_TYPE, request.getPostBodyContentType());
                        HttpEntity entity;
                        entity = new ByteArrayEntity(postBody);
                        postRequest.setEntity(entity);
                        return postRequest;
                    } else {
                        return new HttpGet(request.getUrl());
                    }
                }
            case Method.GET:
                return new HttpGet(request.getUrl());
            case Method.DELETE:
                return new HttpDelete(request.getUrl());
            case Method.POST:
                {
                    HttpPost postRequest = new HttpPost(request.getUrl());
                    postRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                    setEntityIfNonEmptyBody(postRequest, request);
                    return postRequest;
                }
            case Method.PUT:
                {
                    HttpPut putRequest = new HttpPut(request.getUrl());
                    putRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                    setEntityIfNonEmptyBody(putRequest, request);
                    return putRequest;
                }
            case Method.HEAD:
                return new HttpHead(request.getUrl());
            case Method.OPTIONS:
                return new HttpOptions(request.getUrl());
            case Method.TRACE:
                return new HttpTrace(request.getUrl());
            case Method.PATCH:
                {
                    HttpPatch patchRequest = new HttpPatch(request.getUrl());
                    patchRequest.addHeader(HEADER_CONTENT_TYPE, request.getBodyContentType());
                    setEntityIfNonEmptyBody(patchRequest, request);
                    return patchRequest;
                }
            default:
                throw new IllegalStateException("Unknown request method.");
        }
    }
```
# Reference
* https://developer.android.com/training/volley/index.html