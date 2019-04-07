# HttpClient
在Android6.0版本删除了HttpClient类库，若想继续使用需要在build.gradle中引入
```
android{
    useLibrary 'org.apcahe.http.legacy'
}
```
# HttpURLConnection

