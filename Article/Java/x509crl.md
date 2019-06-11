# Question
开发Pad过程中反馈应用内网络连接经常报无网络情况，当系统wifi是在连接状态并可用。开始怀疑是应用的网络被限制，但后来排查并不是。从源头上开始排查，网络请求使用的是OkHttp看log打印发现报错如下：
```
java.security.cert.CertificateNotYetValidException: certificate not valid till 20130502110036GMT+00:00
```
请求是https使用了证书校验，从日志中可以知道是认证中时间不匹配导致证书过期。

# Answer
查看设备系统时间默认为1993年1月1号，自动获取网络时间存在延迟或更新失败的情况，这就是导致网络请求失败的最终原因。那为什么请求明文报错信息是无网络呢,其实是http请求库封装没有单独处理这种异常导致一直以为是网络无法使用。最后修改设备的默认时间可以正常使用了。
```
 if (TextUtils.isEmpty(message) || 
 !message.contains("javax.net.ssl.sslhandshakeexception") && 
 !message.contains("java.security.cert.certpathvalidatorexception") && 
 !message.contains("com.android.org.bouncycastle.jce.exception.extcertpathvalidatorexception")) {
            errorMessage =  "Network error, please retry.";
            ......
           
```
# Summary
## X509Certificate
证书的检查时间的抽象接口，若存在错误并会抛出对应的异常。
```
public abstract void checkValidity(Date date)
        throws CertificateExpiredException, CertificateNotYetValidException;
```

## X509CertSelector

```

```