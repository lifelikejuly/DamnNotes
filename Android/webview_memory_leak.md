# 问题
webview存在内存泄露问题，之前使用webview并没有关注到webview存在许多问题，其中就包括内存泄露。原先以为在使用webview后在关闭页面对webview进行ondesotry操作可以将webview释放，其实还是存在内存泄露的情况。
```
if (mWebView != null) {
            mWebView.pauseTimers();
            mWebView.removeAllViews();
            mWebView.destroy();
            mWebView = null;
        }
```
# 解决
- 不在xml布局中创建webview，而是使用new实例化一个Webview传入是全局getApplicationContext上下文add到布局中。
- 为webview页面单独开辟一个进程，在结束使用后主动杀死该进程释放内存。

暂时还没有看源码解析一下webview为什么存在内存泄露的原因以及谷歌官方是否已经修复过这个问题

# 参考
* https://android-developers.googleblog.com/2009/01/avoiding-memory-leaks.html


