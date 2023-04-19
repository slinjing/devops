k8s部署记录
最低要求：至少2台2核4G的服务器，系统环境CentOS7.6/7.7/7.8
环境准备：
master节点：192.168.32.146
worker节点：192.168.32.146  

安装后的软件版本为：
Kubernetes v1.19.x
calico 3.13.1
nginx-ingress 1.5.5
Docker 19.03.11

检查系统版本、主机名、CPU核心数-master、worker都执行
cat /etc/redhat-release
hostname    #输出结果不能为localhost
lscpu

修改主机名
hostnamectl set-hostname  k8s-master
hostnamectl set-hostname  k8s-worker
hostnamectl status 
echo "127.0.0.1   k8s-master " >> /etc/hosts
echo "127.0.0.1   k8s-worker " >> /etc/hosts

安装docker和kubelet-所有节点执行
export REGISTRY_MIRROR=https://registry.cn-hangzhou.aliyuncs.com
curl -sSL https://kuboard.cn/install-script/v1.19.x/install_kubelet.sh | sh -s 1.19.5

初始化master节点-master节点执行
#替换x.x.x.x为 master节点实际IP（请使用内网IP）
#export 命令只在当前 shell 会话中有效，开启新的 shell 窗口后，如果要继续安装过程，请重新执行此处的 export 命令
export MASTER_IP=192.168.32.146
#替换 dnserver 为 您想要的dnsName
export APISERVER_NAME=dnserver
#Kubernetes 容器组所在的网段，该网段安装完成后，由 kubernetes 创建，事先并不存在于您的物理网络中
export POD_SUBNET=10.10.10.1/24
echo "${MASTER_IP}    ${APISERVER_NAME}" >> /etc/hosts
curl -sSL https://kuboard.cn/install-script/v1.19.x/init_master.sh | sh -s 1.19.5

检查master初始化结果-master节点执行
#执行如下命令，等待 3-10 分钟，直到所有的容器组处于 Running 状态
watch kubectl get pod -n kube-system -o wide
#查看master节点初始化结果
kubectl get nodes -o wide

初始化worker节点
#获得join命令参数-在master节点上执行
kubeadm token create --print-join-command
#kubeadm token create 命令的输出
kubeadm join dnserver:6443 --token w0gtwg.av4is8jfofndr262     --discovery-token-ca-cert-hash sha256:d0740ad6aef477d5fe4facb00558a8dc78af62e3a4742f7967335bc4cbf0248c

初始化worker节点-worker节点执行
#替换 x.x.x.x 为 master 节点的内网 IP
export MASTER_IP=192.168.32.146
#替换dnserver为初始化master节点时所使用的 APISERVER_NAME
export APISERVER_NAME=dnserver
echo "${MASTER_IP}    ${APISERVER_NAME}" >> /etc/hosts
#替换为 master 节点上 kubeadm token create 命令的输出
kubeadm join dnserver:6443 --token 8yq08u.cijqmz3gh7uecj3s     --discovery-token-ca-cert-hash sha256:d0740ad6aef477d5fe4facb00558a8dc78af62e3a4742f7967335bc4cbf0248c



检查初始化结果
在 master 节点上执行
#只在 master 节点执行
kubectl get nodes -o wide
 
输出结果如下所示：
[root@demo-master-a-1 ~]# kubectl get nodes
NAME     STATUS   ROLES    AGE     VERSION
demo-master-a-1   Ready    master   5m3s    v1.19.x
demo-worker-a-1   Ready    <none>   2m26s   v1.19.x
demo-worker-a-2   Ready    <none>   3m56s   v1.19.x

安装Kuboard
kubectl apply -f https://addons.kuboard.cn/kuboard/kuboard-v3-swr.yaml

watch kubectl get pods -n kuboard

访问 Kuboard
在浏览器中打开链接 http://192.168.32.146:30080
用户名： admin
密码： Kuboard123


#deployment.yml
#定义deployment
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:latest
        ports:
        - containerPort: 80

#定义service
---
apiVersion: v1
kind: Service
metadata:
  name: nginx-service
spec:
  selector:
    app: nginx
  ports:
  - name: nginx
    protocol: TCP
    port: 80
    targetPort: 80
  type: NodePort

启动deployment-nginx.yml
kubectl apply -f /usr/docker/deployment-nginx.yml
查看deployment
kubectl get deployment
删除deployment
kubectl delete deployment/deployment-nginx.yml
查看service
kubectl get service
删除service
kubectl delete service/deployment-nginx.yml

#deployment.mysql
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql-deployment
  labels:
    app: mysql
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:latest
        ports:
        - containerPort: 3306
---
apiVersion: v1
kind: Service
metadata:
  name: mysql-service
spec:
  selector:
    app: mysql
  ports:
  - name: mysql
    protocol: TCP
    port: 3306
    targetPort: 3306
  type: NodePort