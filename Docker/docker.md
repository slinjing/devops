# docker
## docker简介
docker是一个开源的引擎，可以轻松的为任何应用创建一个轻量级的、可移植的、自给自足的容器，可以打包、发布、运行任何的应用。
## docker安装(以centos为例)
官网有说明按以下步骤也可以：https://docs.docker.com/engine/install/centos/
### 安装
1. 安装过的先卸载旧版本
```bash
sudo yum remove docker-ce docker-ce-cli containerd.io docker-buildx-plugin \ 
docker-compose-plugin docker-ce-rootless-extras

rm -rf /var/lib/docker
rm -rf /var/lib/containerd
```
2. 安装依赖
```bash
yum install -y yum-utils device-mapper-persistent-data lvm2
```
3. 设置仓库
```bash
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
sed -i 's+download.docker.com+mirrors.aliyun.com/docker-ce+' /etc/yum.repos.d/docker-ce.repo
yum makecache fast
```
4. 安装docker
```bash
yum install -y docker-ce
```
5. 启动并加入自启
```bash
systemctl start docker
systemctl enable docker
```
6. 验证查看版本和docker基本信息
```bash
docker version
docker info
```
### 安装指定版本的Docker-CE
1. 查找Docker-CE的版本
```bash
yum list docker-ce.x86_64 --showduplicates | sort -r
2. 安装指定版本的Docker-CE
yum -y install docker-ce-[VERSION]
```
### 设置镜像加速
```bash
vim /etc/docker/daemon.json
{
    "registry-mirrors": [https://registry.docker-cn.com]
}
```
### docker基本操作
#### 镜像命令
```bash
#搜索镜像
#搜索名称包含nginx的所有镜像
docker search nginx
#搜索stars大于等于1000的nginx镜像
docker search -f stars=1000 nginx
#搜索nginx的前n个镜像
docker search ngin --limit n
#只搜索自动构建的镜像，AUTOMATED为[OK]的
docker search -f is-automated=true nginx
#只搜索官方镜像
docker search -f is-official=true nginx
#拉取镜像
#拉取最新版
docker pull nginx
docker pull nginx:latest
#其他版本可以去官网https://hub.docker.com/或docker search查询
docker pull nginx:stable-perl
#查看镜像
docker images
#保存镜像到本地
docker save -o nginx.tar nginx:latest
#删除镜像
docker rmi nginx:latest
#加载本地镜像
docker load -i nginx.tar
#复制镜像并修改名字和版本
docker tag nginx:latest docker_nginx:v1
#打包镜像
docker commit -a '' -m '' -c '' 2eca8c9865df#容器ID nginx:v2
-a '提交者' 
-m 'Dockerfile指令' 
-c '说明'
```

#### 容器命令
```bash
#运行容器
docker run [参数] image
#参数使用
#指定容器名字  --name = "Name"  
docker run --name nginx nginx:v2
-c   后面跟待完成的命令
#以后台方式运行并且返回ID，启动守护进程式容器  -d   
docker run -d nginx:v2
#使用交互方式运行容器，通常与t同时使用  -i   
#为容器重新分配一个伪输入终端。也即启动交互式容器 -t  
docker run -it nginx:v2
#物理机端口:容器端口映射  -p   
docker run -d -p 8080:80 nginx:v2
#随机指定端口  -P   
docker run -d -P nginx:v2
#给容器挂载存储卷  -v   
docker run -d  -v /root/:/etc nginx:v2
#查看容器日志
docker logs 2eca8c9865df#容器ID 
#启动容器
docker start 容器ID
#重启容器
docker restart 容器ID         
#停止容器
docker stop 容器ID
#列出所有容器
docker ps -a
#列出正在运行的容器
docker ps
#给容器重新命名
docker rename 容器ID 1name 2name
#查看容器内进程
docker top 容器ID
#杀掉容器
docker kill 容器ID
#删除容器（正在运行容器不能删除，除非加-f选项）
docker rm 容器ID
#进入容器
docker exec -it 容器ID bash
#查看docker镜像的变更历史
docker history 容器ID
```





