# ansible
## ansible简介
- ansible是基于Python开发的自动化运维工具，可以实现批量系统配置、批量程序部署、批量运行命令等功能。ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块。
## absible特点
1. 部署简单，无需客户端，只要在主控端部署ansible环境即可。
2. 通过调用特定的模块完成特定任务。
3. 默认使用SSH协议对设备进行管理。
4. 可扩展性强，支持API及自定义模块，可通过Python轻松扩展。
5. 通过Playbooks来定制强大的配置、实现自动化管理。
6. 对云计算平台、大数据都有很好的支持。
7. 具有幂等性：一个操作在一个主机上执行一遍和执行N遍的结果是一样的。
## ansible组成
* Ansible：ansible的核心模块。
* Host Inventory：主机清单，也就是被管理的主机列表。
* Playbooks：ansible的剧本，可想象为将多个任务放置在一起，一块执行。
* Core Modules：ansible的核心模块。
* Custom Modules：自定义模块。
* Connection Plugins：连接插件，用于与被管控主机之间基于SSH建立连接关系。
* Plugins：其他插件，包括记录日志等。
## ansible安装
1. 启用yum仓库
```bash
yum install epel-release -y
```
2. 安装ansible
```bash
yum install ansible
```
3. 查看ansible版本
```bash
ansible --version
```
## ansible命令参数
* -v	详细模式，如果执行成功，输出详细结果
* -i	指定host文件路径，默认在/etc/ansible/hosts
* -f,-forks=NUM	NUM默认是整数5，指定fork开启同步进程的个数
* -m	指定使用的module名称，默认command模块
* -a	指定模块的参数
* -k	提示输入SSH密码，而不是使用基于ssh密钥认证
* -sudo	指定使用sudo获取root权限
* -K	提示输入sudo密码
* -u	指定移动端的执行用户
* -C	测试命令执行会改变什么内容，不会真正的去执行
可以使用ansible-doc -l列出所有模块，使用ansible-doc -s查看指定模块。
## ansible使用
### 基于用户、密码定义hosts
```bash
- vim /etc/ansible/hosts 
- 末尾添加如下内容：
- [web]
- 192.168.33.236   ansible_ssh_user=root ansible_ssh_pass=password 
- 测试连通性 （-i /etc/ansible/hosts可以省略）这里用到了ping模块：
- ansible -i /etc/ansible/hosts web-servers -m ping
```
### 基于ssh密钥来访问定义hosts
```bash
- 设置密钥
- ssh-keygen
- 拷贝密钥
- ssh-copy-id root@192.168.33.236
- 测试
- ssh root@192.168.33.236 或 ansible web -m ping
```
### command模块
作用：在远程节点上执行一个命令（默认模块可以忽略-m command）
* 参数	                       说明
* chdir	        在执行命令之前，通过 cd 进入到该参数指定的目录
* creates	    在创建一个文件之前，先判断文件是否存在，如果存在则跳过前面的东西，如果不存在则执行前面的动作
* free_form	    该参数即为输入的 linux 系统命令，实现远程执行和管理
* removes	    判断一个文件是否存在，如果存在则执行前面的动作，如果不存在则跳过前面的内容（和上面的 creates 是相反的）
* warn	        是否提供告警信息
使用 command 模块时，不得出现shell变量$name，也不得使用特殊符号 > < | ; & 等，如果需要使用前面的特殊符号则可以使用 shell 模块来实现。
### 示例
```bash
ansible web -m command -a "df -h"      #查看远程节点磁盘情况
ansible web -m command -a "uptime"     #查看远程节点负载信息
ansible web -m command -a "pwd chdir=/etc"     #先切换到/etc目录下，然后打印出当前的目录信息（chdir参数）
ansible web -m command -a "pwd creates=/hello" #判断/hello文件是否存在，存在则不执行前面的 pwd 命令,存在则执行 pwd 命令（creates参数）
ansible web -m command -a "cat ./test.txt removes=./test.txt"  #判断./test.txt文件是否存在,存在则删除，不存在则执行（removes参数）
ansible web -m command -a "chmod u+x /root/passwd warn=false"  #不提供告警信息（warn参数）
```






