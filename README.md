## Docker的安装配置与应用

> # <span id="home">目录</span>
> 
> * [1. 安装前准备（*Ubuntu*）](#1)
>   * [1.1 乱码处理](#1-1)
>   * [1.2 换源](#1-2) 
> 
> * [2. 安装配置*Docker*](#2)
>   * [2.1 安装Docker](#2-1)
>   * [2.2 配置Swarm](#2-2)
>   * [2.3 安装Portainer](#2-3)
> 

本教程基于**Ubuntu 16.04.7**  
使用镜像ubuntu-16.04.7-server-amd64.iso默认步骤安装

<h1 id="1">1. 安装前准备（*Ubuntu*）</h1>

<h2 id="1-1">1.1 乱码处理</h2>

> [参考链接](https://blog.csdn.net/u013699869/article/details/52614400 "点击打开")

````
sudo vim /etc/default/locale
#将内容改为：
LANG="en_US.UTF-8"
LANGUAGE="en_US:en"
````

[返回目录](#home)

<h2 id="1-2">1.2 换源</h2>

> [参考链接](https://blog.csdn.net/u012308586/article/details/102953882 "点击打开")

````
#备份原文件
sudo cp /etc/apt/sources.list /etc/apt/sources.list_bak
#修改配置文件
sudo vim /etc/apt/sources.list
删除100行
100 dd
#写入模式
i
#粘贴以下内容
deb http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ xenial-backports main restricted universe multiverse
#保存修改
:wq
````

[返回目录](#home)

<h1 id="2">2. 安装配置*Docker*</h1>

<h2 id="2-1">2.1 安装Docker</h2>

> [阿里云官方镜像站](https://developer.aliyun.com/mirror/ "点击打开")
> 
> [Docker CE 镜像](https://developer.aliyun.com/mirror/docker-ce?spm=a2c6h.13651102.0.0.3e221b113eVagK "点击打开")

````
Ubuntu 14.04/16.04（使用 apt-get 进行安装）
# step 1: 安装必要的一些系统工具
sudo apt-get update
sudo apt-get -y install apt-transport-https ca-certificates curl software-properties-common
# step 2: 安装GPG证书
curl -fsSL https://mirrors.aliyun.com/docker-ce/linux/ubuntu/gpg | sudo apt-key add -
# Step 3: 写入软件源信息
sudo add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
# Step 4: 更新并安装Docker-CE
sudo apt-get -y update
sudo apt-get -y install docker-ce
````

[返回目录](#home)

<h2 id="2-2">2.2 配置Swarm</h2>

````
#初始化swarm
docker swarm init
````

[返回目录](#home)

<h2 id="2-3">2.3 安装Portainer</h2>

> [Docker Swarm](https://documentation.portainer.io/v2.0/deploy/ceinstallswarm/ "点击打开")

````
curl -L https://downloads.portainer.io/portainer-agent-stack.yml -o portainer-agent-stack.yml

docker stack deploy -c portainer-agent-stack.yml portainer
````

使用ip:9000访问portainer


[返回目录](#home)