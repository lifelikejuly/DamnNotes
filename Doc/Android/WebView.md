# WebView

## 基本类
### WebChromeClient

### WebViewClient

### WebSettings

## JSBridge
WebView支持前端页面操作调用原生方法，通过addJavascriptInterface添加类，并
```
val webView: WebView = findViewById(R.id.webview)
webView.addJavascriptInterface(WebAppInterface(this), "Android")
```

