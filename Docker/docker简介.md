# docker简介
## docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器，可以打包、发布、运行任何的应用。
# docker架构
## docker分为docker client、dockerd、docker Registry
## docker client是用户与docker交互的主要方式，比如要使用docker run、docker stop时，由客户端将命令发送到dockerd执行，docker client可以与多个dockerd通信。
## dockerd侦听docker API的请求并管理docker对象，比如：镜像、容器、网络和卷。
## docker Registry用来存储镜像，任何人都可以通过docker pull或docker run从docker Registry中拉取镜像。

