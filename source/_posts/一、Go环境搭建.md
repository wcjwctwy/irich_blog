---
title: 一、GO环境安装
date: 2018-11-30 15:44:44
tags: [Golang]
categories: [语言基础]
---

## GO环境安装
### 下载go
下载地址： https://golang.google.cn/dl/  
### 配置环境变量

配置GOPATH和PATH    
### GOPROXY配置
因为国内网络得原因，需要配置GOPROXY  
GOPROXY=https://goproxy.io
## 依赖管理工具安装
### vgo
下载地址：https://github.com/golang/vgo
### 简介

```
vgo是Go语言推出的第三方库管理工具，即将在Go语言新版本中使用。

相信大家都接触过其它语言的第三方库管理工具，比如Java的maven，PHP的composer，Python的pip，Node的npm等。vgo类似于这样的功能，方便Go语言项目管理第三方库。
```
==暂时没有在go环境中应用，需要手动安装 且go版本要在11及以上==
### 安装
下载zip包，解压 将vendor文件夹下面得所有文件复制到配置得$GOPATH/src下面

```
cd vgo/path
go build -o vgo.exe main.go
```
将编译得vgo.exe复制到$GOROOT/bin目录下即可


--------------------------------------------------------------------
### 3、安装开发工具dep  
//设置环境变量 使用vendor目录GO15VENDOREXPERIMENT=1
1.安装 

```
go get -u github.com/golang/dep/cmd/dep
```

2.安装验证
```
$ dep
dep is a tool for managing dependencies for Go projectsUsage: dep <command>Commands:  init    Initialize a new project with manifest and lock files
  status  Report the status of the project‘s dependencies
  ensure  Ensure a dependency is safely vendored in the project
  prune   Prune the vendor tree of unused packagesExamples:
  dep init                               set up a new project
  dep ensure                             install the project‘s dependencies
  dep ensure -update                     update the locked versions of all dependencies
  dep ensure -add github.com/pkg/errors  add a dependency to the projectUse "dep help [command]" for more information about a command.
```


```
git clone https://github.com/grpc/grpc-go.git D:/Users/wangcongjun/go/src/google.golang.org/grpc

git clone https://github.com/golang/net.git D:/Users/wangcongjun/go/src/golang.org/x/net

git clone https://github.com/golang/text.git D:/Users/wangcongjun/go/src/golang.org/x/text

go get -u github.com/golang/protobuf/proto

go get -u github.com/golang/protobuf/protoc-gen-go

git clone https://github.com/google/go-genproto.git D:/Users/wangcongjun/go/src/google.golang.org/genproto

cd D:/Users/wangcongjun/go/src/

go install google.golang.org/grpc
```



