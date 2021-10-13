# 前言

本项目为工作中积累的一些知识，目的是为了能够在工作中不断的提高自己。

但是据以往的经验，在积累的过程中往往会半途而废，因此除了提高自己的自觉性之外，还需要用科学的方式加以管理。

希望自己能够在这个积累的过程中不断的提高自己，未来能够取得更好的成就。

# 命名格式

## 文件命名格式

- 一级文件夹
- 二级文件夹
- 三级文件

## 文件中命名格式

最多三级目录

- 一级：大类
- 二级：小类 or 具体问题、技术点
- 三级：具体问题、技术点

# 项目目录



# Git提交问题

```
fatal: unable to access 'https://github.com/YangWorkForGuang/WorkAndStudy.git/': OpenSSL SSL_read: Connection was reset, errno 10054
```

可能是因为网络的问题导致每次都会出现这个问题

解决方法：`git push origin master`之前，输入`git config --global http.sslVerify "false"`

## 常用的git命令

git diff：查看哪些文件发生了哪些变化

git diff --name-only：只显示变化文件的文件名
