---
layout: post
title: Git使用Tips
category : git
tagline: "Supporting tagline"
tags : [git]
---

# git clone 重命名文件夹
```
git clone https://github.com/user/userApp.git name_you_want
```

# git 多账户下 push出现403错误
执行`git push origin master`时出现如下错误：
>remote: Permission to spetacular/asyntask.git denied to someone.
>fatal: unable to access 'https://github.com/spetacular/asyntask.git/': The requested URL returned error: 403

这是由于.git/config的[remote "origin"]配置错误。可以更改此处代码。格式可以为以下两种情况：
```
url=https://spetacular@github.com/spetacular/asyntask.git
url=ssh://git@github.com/spetacular/asyntask.git
```

如果不想更改全局配置，而只想临时使用：
```
git push https://spetacular@github.com/spetacular/asyntask.git master
```

# git 设置和取消网络代理
可设置为shadowsocks代理：
```
git config --global https.proxy http://127.0.0.1:1080

git config --global https.proxy https://127.0.0.1:1080

git config --global --unset http.proxy

git config --global --unset https.proxy
```

# git 更新特定tag
已经打过tag，但又发现需要微调，再次`git tag  1.0.0`时，会发生如下错误
>fatal: tag '1.0.0' already exists

1.删除本地tag:
```
 git tag -d 1.0.0
 ```
2.重新打tag：
 ```
 git tag  1.0.0
 ```
3.push到服务器
 ```
 git push --force origin refs/tags/1.0.0:refs/tags/1.0.0
 ```

