# ADB


# keyTool

获取应用的md5、sha1等信息
```
keytool -printcert -file platform.x509.pem 
keytool -printcert -jarfile xxx.apk //检查apk
keytool -printcert -file xxxx.RSA //解压apk获取rsa文件
```