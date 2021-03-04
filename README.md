## Docker的安装配置与应用

> # <span id="home">目录</span>
> 
> * [1. 安装前准备（*Ubuntu*）](#1)
>   * [1.1 乱码处理](#1-1)
>   * [1.2 设置root密码及sftp](#1-2)
>   * [1.3 apt换源](#1-3)
> 
> * [2. 安装配置*Docker*](#2)
>   * [2.1 安装Docker](#2-1)
>   * [2.2 配置Swarm](#2-2)
>   * [2.3 安装Portainer](#2-3)
> 
> * [3. 安装配置*GitLab*](#3)
>   * [3.1 安装GitLab](#3-1)
>   * [3.2 配置GitLab](#3-2)
> 
> * [4. 安装配置*Registry*](#4)
>   * [4.1 安装Registry](#4-1)
>   * [4.2 配置Registry](#4-2)
> 
> * [5. 安装配置*IDEA*](#5)
>   * [5.1 安装IDEA](#5-1)
>   * [5.2 配置IDEA编译java](#5-2)
> 
> * [6. 配置jar通过GitLab CI生成镜像](#6)
>   * [6.1 配置CI](#6-1)
> 

本教程基于**Ubuntu 16.04.7**  
使用镜像ubuntu-16.04.7-server-amd64.iso默认步骤安装

| 端口 | 应用 |
| :-: | :-: |
| 9000 | Portainer |
| 31000 | GitLab Web |
| 31001 | GitLab 22 |
| 31002 | GitLab 443 |

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

<h2 id="1-2">1.2 设置root密码及sftp</h2>

> [参考链接](http://i.lckiss.com/?p=4762 "点击打开")

````
#改 root 密码
sudo passwd root
#编辑配置文件（修改 PermitRootLogin yes）
sudo vi /etc/ssh/sshd_config
#重启服务
sudo systemctl restart ssh
````

[返回目录](#home)

<h2 id="1-3">1.3 apt换源</h2>

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

<h1 id="3">3. 安装配置*GitLab*</h1>

<h2 id="3-1">3.1 安装GitLab</h2>

> [参考链接](https://docs.gitlab.com/omnibus/docker/README.html#troubleshooting "点击打开")


````
#拉取镜像（当前版本镜像2.2G，若通过Portainer下载时间较久切无法查看进度，推荐使用命令下载）
sudo docker pull gitlab/gitlab-ce

#Ubuntu中建目录（可自定）
sudo mkdir -p /srv/gitlab/data
sudo mkdir -p /srv/gitlab/logs
sudo mkdir -p /srv/gitlab/config

#使用docker-compose安装
cd && mkdir gitlab && cd gitlab

vim gitlab.rb
#写入如下
external_url 'https://my.domain.com/'
gitlab_rails['initial_root_password'] = File.read('/run/secrets/gitlab_root_password')

vim root_password.txt
#写入如下
MySuperSecretAndSecurePass0rd!

vim docker-compose.yml
#写入如下
version: "3.6"
services:
  gitlab:
    image: gitlab/gitlab-ce:latest
    ports:
      - "31000:80"
      - "31001:22"
      - "31002:443"
    volumes:
      - /srv/gitlab/data:/var/opt/gitlab
      - /srv/gitlab/logs:/var/log/gitlab
      - /srv/gitlab/config:/etc/gitlab
    environment:
      GITLAB_OMNIBUS_CONFIG: "from_file('/omnibus_config.rb')"
    configs:
      - source: gitlab
        target: /omnibus_config.rb
    secrets:
      - gitlab_root_password
  gitlab-runner:
    image: gitlab/gitlab-runner:alpine
    deploy:
      mode: replicated
      replicas: 4
configs:
  gitlab:
    file: ./gitlab.rb
secrets:
  gitlab_root_password:
    file: ./root_password.txt

#新建stack
sudo docker stack deploy --compose-file docker-compose.yml gitlab
#等待5分钟左右容器启动完成
````

修改GitLab root密码
> [参考链接](https://www.cnblogs.com/lvchaoshun/p/13285033.html "点击打开")

````
#Portainer进入gitlab容器控制台
#1.使用以下命令启动Ruby on Rails控制台
gitlab-rails console -e production
#2.选择root
user = User.where(id: 1).first
#3.修改密码
user.password = 'secret_pass'
user.password_confirmation = 'secret_pass'
#4.保存
user.save!

````

[返回目录](#home)

<h2 id="3-2">3.2 配置GitLab</h2>

> [参考链接](https://docs.gitlab.com/ "点击打开")

### 3.2.1 修改语言

1.登录进GitLab

2.点击右上角头像

3.点击Preferences

4.拉到最底下选择语言

5.刷新页面

[返回目录](#home)

<h1 id="4">4. 安装配置*Registry*</h1>

<h2 id="4-1">4.1 安装Registry</h2>

> [参考链接](https://docs.docker.com/registry/deploying/ "点击打开")
> 
> [参考链接](https://docs.docker.com/registry/deploying/#deploy-your-registry-using-a-compose-file "点击打开")
> 
> [参考链接](https://www.cnblogs.com/edisonchou/p/docker_registry_repository_setup_introduction.html "点击打开")
> 
> [参考链接](https://www.cnblogs.com/zhaojiankai/p/7813969.html "点击打开")

````
#下载镜像（当前版本26.2M，无须提前下载）
sudo docker pull registry:latest

#新建相应目录
sudo mkdir -p /dockeruse/registry/data
sudo mkdir -p /dockeruse/registry/certs
sudo mkdir -p /dockeruse/registry/auth

#生成证书
sudo openssl req -newkey rsa:4096 -nodes -sha256 -keyout /dockeruse/registry/certs/domain.key -x509 -days 365 -out /dockeruse/registry/certs/domain.crt

#在docker中创建
sudo docker secret create registry_domain.crt certs/domain.crt
sudo docker secret create registry_domain.key certs/domain.key

#新建stack，粘贴以下
version: "3"
services:
  registry:
      restart: always
      image: registry:latest
      ports:
        - 32000:5000
      environment:
        REGISTRY_HTTP_TLS_CERTIFICATE: /certs/domain.crt
        REGISTRY_HTTP_TLS_KEY: /certs/domain.key
        #REGISTRY_AUTH: htpasswd
        #REGISTRY_AUTH_HTPASSWD_PATH: /auth/htpasswd
        #REGISTRY_AUTH_HTPASSWD_REALM: Registry Realm
      volumes:
        - /dockeruse/registry/data:/var/lib/registry
        - /dockeruse/registry/certs:/certs
        - /dockeruse/registry/auth:/auth

#启动，通过https://ip:32000/v2/_catalog访问
#响应{"repositories":[]}
````

[返回目录](#home)

<h2 id="4-2">4.2 配置Registry</h2>

> [参考链接](https:// "点击打开")

[返回目录](#home)

<h1 id="5">5. 安装配置*IDEA*</h1>

<h2 id="5-1">5.1 安装IDEA</h2>

> [参考链接](https:// "点击打开")

[返回目录](#home)

<h2 id="5-2">5.2 配置IDEA编译java</h2>

> [参考链接](https:// "点击打开")

[返回目录](#home)

<h1 id="6">6. 配置jar通过GitLab CI生成镜像</h1>

<h2 id="6-1">6.1 安装CI</h2>

> [参考链接](https:// "点击打开")

[返回目录](#home)