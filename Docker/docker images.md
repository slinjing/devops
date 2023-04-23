# docker image管理
## 我们在使用docker的过程中基本离不开对镜像的操作，基本上分为以下⑤种情况：
### 从公共仓库拉取镜像
### 备份镜像
### 使用本地镜像
### 上传到私有仓库
### 拉取私有仓库镜像
## 下面将对五种情况分别演示
### 从公共仓库拉取镜像
#### 当我们需要从公共仓库拉取镜像时非常简单，直接使用docker pull命令docker会自动拉取最新的镜像，或者使用docker run命令，docker命令在执行时会先检查本地是否有要运行容器的镜像，如没有则会先拉取最新版镜像再运行容器。前提是你/etc/docker/目录下的daemon.json文件docker镜像源已经配置。
#### 演示
#### 从公共仓库拉取镜像
```bash
[root@k8s-master ~]#docker pull redis      #拉取镜像
[root@k8s-master ~]# docker images         #查看镜像可以看到已经拉取到redis的最新镜像
REPOSITORY         TAG       IMAGE ID           CREATED          SIZE
redis             latest   eca1379fe8b5        5 days ago         117MB

[root@k8s-master ~]# docker rmi redis       #先删除刚刚的镜像
[root@k8s-master ~]# docker run -d redis     #在本地没有镜像的情况下运行容器，可以看到步骤是先拉取镜像再运行容器
Unable to find image 'redis:latest' locally
latest: Pulling from library/redis
26c5c85e47da: Pull complete
39f79586dcf2: Pull complete
79c71d0520e5: Pull complete
60e988668ca1: Pull complete
873c3fc9fdc6: Pull complete
50ce7f9bf183: Pull complete
Digest: sha256:f50031a49f41e493087fb95f96fdb3523bb25dcf6a3f0b07c588ad3cdbe1d0aa
Status: Downloaded newer image for redis:latest
ff0ff534d058a0dfc4cf233e224f0ffad16c1c251055cedb4c5e9e6af38a75f0
[root@k8s-master ~]# docker ps | grep redis
ff0ff534d058    redis      "docker-entrypoint.s…"   13 seconds ago      Up 9 seconds        6379/tcp            sleepy_mcclintock
```
#### 备份镜像
```bash
#以刚刚的redis镜像为例，把镜像保存到本地
[root@k8s-master ~]# docker save -o /root/redis.tar redis  
[root@k8s-master ~]# ll /root/redis.tar    #成功备份到/root目录下，文件名为redis.tar
-rw------- 1 root root 120733696 Apr 23 17:27 /root/redis.tar
```
#### 使用本地镜像
```bash
#刚刚我们备份了一个镜像现在就来使用它
[root@k8s-master ~]# docker stop ff0ff534d058
[root@k8s-master ~]# docker rmi -f redis   #先停止容器再删除镜像
[root@k8s-master ~]# docker load -i redis.tar
```