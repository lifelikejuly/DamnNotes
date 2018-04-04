# 问题
开发中有时无意间加了不想上传到远程仓库的文件，在.gitignore为时已晚，远程仓库中文件在之前版本就已经上传存在，现在想删除怎么办呢。
# 几条指令
1. 在.gitingnore文件中加入忽略文件或忽略文件夹
```
/.idea/
```
2. git删除对应文件
```
git rm --cached -r .idea
```
3. 最后一顿commit和push就搞定了

# 参考
git指令了解一下
* https://git-scm.com/book/zh/v2/%E8%B5%B7%E6%AD%A5-%E5%91%BD%E4%BB%A4%E8%A1%8C
* https://blog.csdn.net/halaoda/article/details/78661334