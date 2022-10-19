# Docker搭建本地仓库

## 搭建本地私有仓库

```bash
#首先下载registry镜像
docker pull registry

#在daemon.json文件中添加私有镜像仓库地址
vim /etc/docker/daemon.json
{
"insecure-registries":["192.168.121.17:5000"],          ##添加，注意是以逗号分隔
"registry-mirrors": ["https://6ijb8ubo.mirror.aliyuncs.com"]
}

systemctl restart docker
```

![7f42a7b416474a189b52284b69772a86](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181717754.png)

```bash
#运行registry容器
docker run -itd -v /data/registry:/var/lib/ registry -p 5000:5000 --restart=always --name reqistry registry:.latest
------------------------------------------------------------------------
-itd:在容器中打开一个伪终端进行交互操作，并在后台运行
-v:把宿主机的/data/registry日录绑定到容器/var/lib/recistry日录(这个目录是registry容器中存放镜像文件的日录)，来实现数据的持久化;
-p:映射端口;访问宿主机的5000端口就访问到registry容器的服务了
--restart=always:这是重启的策略，在容器退出时总是重启容器
--name registry:创建容器命名为registry
registry:latest:这个是刚才pull下来的镜像
--------------------------------------------------------------------
Docker容器的重启策略如下:
no:默认策略,在容器退出时不重启容器
on-failure:在容器非正常退出时（退出状态非0）,才会重启容器
on-failure:3:在容器非正常退出时重启容器，最多重启3次
always:在容器退出时总是重启容器
unless-stopped:在容器退出时总是重启容器，但是不考虑在Docker守护进程启动时就已经停止了的容器 
```

![e7d2018f893942a884745a153ce420e5](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181717824.png)

```bash
#为镜像打标签
docker tag nginx:now 192.168.121.17:5000/nginx:v1

#上传到私有仓库
docker push 192.168.121.17:5000/nginx:v1

#列出私有仓库的所有镜像
curl http://192.168.121.17:5000/v2/_catalog

#列出私有仓库的contos镜像有哪些tag
curl http://192.168.121.17:5000/v2/nginx/tags/list

#先删除原有的nginx的镜像，再测试私有仓库下载
docker rmi  170c6f84dc69 -f
docker pull 192.168.121.17:5000/nginx:v1
```

![87b7753a5dc548439a7ffca51bd475c9](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181719155.png)

![399b2a79bdf44a5a9d1ee4349413a399](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181719112.png)
## Harbor
### 什么是Harbor

- Harbor 是Wware公司开源的企业级Docker Registry项目，其目标是帮助用户迅速搭建一个企业级的Docker Registry 服务。
- Harbor以 Docker 公司开源的Registry为基础，提供了图形管理ur、基于角色的访问控制(Role Based AccessControl) 、AD/LDAP集成、以及审计日志(Auditlogging)等企业用户需求的功能，同时还原生支持中文
- Harbor的每个组件都是以Docker容器的形式构建的，使用docker-compose米对它进行部署。用于部著Harbor 的 docker-compose模板位于harbor/ docker-compose. yml。

### Harbor的特性
1、基于角色控制:用户和仓库都是基于项目进行组织的，而用户在项目中可以拥有不同的权限。
2、基于镜像的复制策略:镜像可以在多个Harbor实例之间进行复制（同步）。
3、支持 LDAP/AD: Harbor可以集成企业内部已有的 AD/LDAP（类似数据库的一张表),
4、镜像删除和垃圾回收:镜像可以被删除，也可以回收镜像占用的空间。
5、图形化用户界面:用户可以通过浏览器来浏览，搜索镜像仓库以及对项目进行管理。
6、审计管理:所有针对镜像仓库的操作都可以被记录追溯，用于审计管理。
7、支持 RESTful API:RESTful API 提供给管理员对于Harbor更多的操控，使得与其它管理软件集成变得更容易。
8、Harbor和docker registry的关系:Harbor实质上是对docker registry做了封装，扩展了自己的业务模板。

### Harbor的构成

Harbor 在架构上主要有** Proxy、Registry、Core services、Database (Harbor-db） 、Log collector (Harbor-log)、Jab services**六个组件。

- **Proxy**： Harbor 的 Registry、UI、Token服务等组件，都处在nginx 反向代理后边。该代理将来自浏览器、docker clients的请|求转发到后端不同的服务上。
- **Registry**：负责储存Docker镜像，并处理Docker push/pull 命令。由于要对用户进行访问控制，即不同用户对Docker镜像有不同的读写权限，Registry 会指向一个Token服务，强制用户的每次Docker pull/push 请求都要携带一个合法的Token，Registry会通过公钥对Token进行解密验证。
- **Core services**： Harbor的核心功能，主要提供以下3个服务:

1. UI (harbor-ui)︰提供图形化界面，帮助用户管理Registry .上的镜像（image)，并对用户进行授权。
2. lWebHook:为了及时获取Registry 上image 状态变化的情况，在Registry 上配置Webhook，把状态变化传递给UI模块。
3)Token 服务:负责根据用户权限给每个 Docker push/pull命令签发Token。Docker 客户端向Registry 服务发起的请求，如果不包含Token，会被重定向到Token 服务，获得Token后再重新向 Registry进行请求。

- **Database (harbor-db)**： 为core services提供数据库服务，负责储存用户权限、审计日志、Docker镜像分组信息等数据。
- **Job services**：主要用于镜像复制，本地镜像可以被同步到远程Harbor实例上。
- ·**Log collector (harbor-log)**：负责收集其他组件的日志到一个地方。
![fe13710497064fd684a0b2fb32855da1](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181720215.png)

Harbor的每个组件都是以Docker容器的形式构建的，因此，使用 Docker Compose来对它进行部署。总共分为7个容器运行，通过在docker-compose. yml所在目录中执行 docker-compose ps 命令来查看

名称分别为：**nginx**、**harbor-jobservice**、**harbor-ui**、**harbor-db**、**harbor-adminserver**、**registry**、**harbor-log**。

其中 harbor-adminserver 主要是作为一个后端的配置数据管理，并没有太多的其他功能。harbor-ui所要操作的所有数据都通过harbor-adminserver 这样一个数据配置管理中心来完成。
## Harbor部署
```bash
Harbor服务器  192.168.121.17  docker-ce、docker-compose、harbor-offline-v1.1.2
client服务器     192.168.121.16  docker-ce
```

### 安装 Docker-Compose 并查看版本判断安装是否成功
```bash
cp docker-compose /usr/local/bin/
chmod +x /usr/local/bin/docker-compose
docker-compose -v
```

![543e223aafe14214b35133335d8b7180](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181720569.png)

### 关闭所有节点的防火墙
```bash
systemctl stop firewalld
systemctl disable firewalld
setenforce 0
```

### 部署 Harbor 服务
Harbor 被部署为多个 Docker 容器，因此可以部署在任何支持 Docker 的 Linux 发行版 上。
服务端主机需要安装 Python、Docker 和 Docker Compose。

#### 1.下载 Harbor 安装程序
```bash
wget http:// harbor.orientsoft.cn/harbor-1.2.2/harbor-offline-installer-v1.2.2.tgz

tar zxvf harbor-offline-installer-v1.2.2.tgz -C /usr/local/
```

![3a370e9484b644fc8021e7a1e93ab664](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181721288.png)

#### 2.配置 Harbor 参数文件

```bash
vim /usr/local/harbor/harbor.cfg

hostname = 192.168.200.60     #第五行修改为主机IP
```

![225e8268bd694c73b6faeaf9a600e583](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181721127.png)

![5f981495441b479db1377e7a35f8a11c](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181722406.png)

关于 Harbor.cfg 配置文件中有两类参数：所需参数和可选参数

**（1）、所需参数：这些参数需要在配置文件 Harbor.cfg 中设置。**

如果用户更新它们并运行 install.sh脚本重新安装 Harbour，参数将生效。具体参数如下：

-  hostname：用于访问用户界面和 register 服务。它应该是目标机器的 IP 地址或完全限定的域名（FQDN）
例如 192.168.200.60 或 hub.gcc.cn。不要使用 localhost 或 127.0.0.1 为主机名。 
-  ui_url_protocol：（http 或 https，默认为 http）用于访问 UI 和令牌/通知服务的协议。如果公证处于启用状态，则此参数必须为 https。 
-  max_job_workers：镜像复制作业线程。 
-  db_password：用于db_auth 的MySQL数据库root 用户的密码。 
-  customize_crt：该属性可设置为打开或关闭，默认打开。打开此属性时，准备脚本创建私钥和根证书，用于生成/验证注册表令牌。
当由外部来源提供密钥和根证书时，将此属性设置为 off。 
-  ssl_cert：SSL 证书的路径，仅当协议设置为 https 时才应用。 
-  ssl_cert_key：SSL 密钥的路径，仅当协议设置为 https 时才应用。 
-  secretkey_path：用于在复制策略中加密或解密远程 register 密码的密钥路径。 

**（2）可选参数**

这些参数对于更新是可选的，即用户可以将其保留为默认值，并在启动 Harbor 后在 Web UI 上进行更新。
如果进入 Harbor.cfg，只会在第一次启动 Harbor 时生效，随后对这些参数的更新，Harbor.cfg 将被忽略。

注意：如果选择通过UI设置这些参数，请确保在启动Harbour后立即执行此操作。具体来说，必须在注册或在 Harbor 中创建任何新用户之前设置所需的 auth_mode。当系统中有用户时（除了默认的 admin 用户），auth_mode 不能被修改。具体参数如下：

-  Email：Harbor需要该参数才能向用户发送“密码重置”电子邮件，并且只有在需要该功能时才需要。
请注意，在默认情况下SSL连接时没有启用。如果SMTP服务器需要SSL，但不支持STARTTLS，那么应该通过设置启用SSL email_ssl = TRUE。 
-  harbour_admin_password：管理员的初始密码，只在Harbour第一次启动时生效。之后，此设置将被忽略，并且应 UI中设置管理员的密码。
请注意，默认的用户名/密码是 admin/Harbor12345。 
-  auth_mode：使用的认证类型，默认情况下，它是 db_auth，即凭据存储在数据库中。对于LDAP身份验证，请将其设置为 ldap_auth。 
-  self_registration：启用/禁用用户注册功能。禁用时，新用户只能由 Admin 用户创建，只有管理员用户可以在 Harbour中创建新用户。
注意：当 auth_mode 设置为 ldap_auth 时，自注册功能将始终处于禁用状态，并且该标志被忽略。 
-  Token_expiration：由令牌服务创建的令牌的到期时间（分钟），默认为 30 分钟。 
-  project_creation_restriction：用于控制哪些用户有权创建项目的标志。默认情况下， 每个人都可以创建一个项目。
如果将其值设置为“adminonly”，那么只有 admin 可以创建项目。 
-  verify_remote_cert：打开或关闭，默认打开。此标志决定了当Harbor与远程 register 实例通信时是否验证 SSL/TLS 证书。
将此属性设置为 off 将绕过 SSL/TLS 验证，这在远程实例具有自签名或不可信证书时经常使用。 

另外，默认情况下，Harbour 将镜像存储在本地文件系统上。在生产环境中，可以考虑使用其他存储后端而不是本地文件系统，
如 S3、Openstack Swif、Ceph 等。但需要更新 common/templates/registry/config.yml 文件。

### 3.启动 Harbor

```bash
sh /usr/local/harbor/install.sh
```

![4ba865cd149e40818f3e58d5a6bd5f76](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181722320.png)

### 4.查看 Harbor 启动镜像

```bash
#查看镜像
[root@localhost ~]# docker images

#查看容器
[root@localhost ~]# docker ps -a

[root@localhost ~]# cd /usr/local/harbor/
[root@localhost ~]# docker-compose ps       #查看此镜像下有多少容器，注意要在配置模板文件的目录中才可以使用此命令
```

![73cf13e356b64296aef3224227492811](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181722657.png)

![ccce72cdcabc44e0981c6c7b00aaff4c](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181723483.png)

![dd3a173fee354edab6fa05a42fa2eba0](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181723059.png)

![](https://img-blog.csdnimg.cn/a43d599969044f298e314f97aa3b211b.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzU0NTg1NzY4,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=Yf3gc&originHeight=723&originWidth=1124&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=shadow&title=)

![84876217692747a0bc4edb725109c85c](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181723901.png)

### 5、在Harbor界面myproject-song目录下可看见此镜像及相关信息

此时我们发现生成了一个镜像，我们可以通过命令行上传项目

```bash
#首先在命令行登录Harbor，记住Harbor默认本机是127.0.0.1而不是本机的IP地址或0.0.0.0（任意主机）
docker login http://127.0.0.1
```

![abae5a1623ab4d1693c84d99c4bb24b1](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181724618.png)

![](https://img-blog.csdnimg.cn/ed57230c905244659ae4a38f2c21f9c8.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L20wXzU0NTg1NzY4,size_16,color_FFFFFF,t_70#crop=0&crop=0&crop=1&crop=1&id=bCabS&originHeight=302&originWidth=1028&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=shadow&title=)

```bash
#然后为我们要上传的镜像打一个标签
docker tag nginx:latest 127.0.0.1/myproject-song/nginx:v1

#上传镜像
docker push 127.0.0.1/myproject-song/nginx:v1
```

![53cd614270e5463e8c082855a017efdc](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181724461.png)

![f4c0e62618834838acd5d3020c087705](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181724390.png)

![6ed51251591848ffab9c35ee4c68ab2e](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181725077.png)

![e445b44804104112a9459b77c94d2910](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181725955.png)

### 6、在其他客户端上传镜像

以上操作都是在Harbor服务器本地操作。如果其他客户端登录到Harbor，就会报如下错误。出现这问题的原因为Docker Registry交互默认使用的是HTTPS，但是搭建私有镜像默认使用的是HTTP服务，所以与私有镜像交互时出现一下错误：

```bash
[root@localhost ~]# docker login -u admin -p Harbor12345 http://192.168.121.17
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Error response from daemon: Get https://192.168.121.17/v2/: dial tcp 192.168.121.17:443: connect: connection refused
```

```bash
解决办法：
vim /usr/lib/systemd/system/docker.service

ExecStart=/usr/bin/dockerd -H fd:// --insecure-registry 192.168.121.17 --containerd=/run/containerd/containerd.sock
## 添加，--insecure-registry 192.168.121.17

systemctl daemon-reload
systemctl restart docker
docker login -u admin -p Harbor12345 http://192.168.121.17
```

![3d22172de6ad4153b9c58c7b3bfaada1](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181725883.png)

```bash
#登录成功之后我们可以尝试从Harbor中下载镜像
docker pull 192.168.121.17/myproject-song/nginx:v1
```

![d2fd2729f1c749f6a6045c95680232eb](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181726226.png)

```bash
#我们也可以将本地的镜像上传到Harbor中
docker tag cirros:latest 192.168.121.17/myproject-song/cirros:v2     #先给原镜像打一个标签

docker push 192.168.121.17/myproject-song/cirros:v2       #上传镜像
```

![4d0a19c2bac942e89c28323ac24e72f5](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181726833.png)

![5d5caea12d804bca983580c7752d4f33](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181726095.png)

## 维护管理Harbor

可以使用 docker-compose 来管理 Harbor。一些有用的命令如下所示，必须在与
docker-compose.yml 相同的目录中运行

### 1、修改 Harbor.cfg 配置文件

要更改 Harbour 的配置文件时，请先停止现有的 Harbour 实例并更新 Harbor.cfg；然
后运行 prepare 脚本来填充配置；最后重新创建并启动 Harbour 的实例。

```bash
cd /usr/local/harbor/
docker-compose down -v

./prepare
```

### 2、创建 Harbor 用户

![3df9fe1aca5b45e6ac76ccdc43ef46a1](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181727590.png)

![07034329da634175a96153ff82610f71](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181727015.png)

### 3、添加项目开发人员

![83aa05672a9a4fe2a8a93311a86ee93a](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181727297.png)

### 4、在客户端上使用普通账户操作镜像

```bash
//删除上述打标签的本地镜像
docker rmi 192.168.121.17/myproject-song/cirros:v2

//先退出当前用户，然后使用上述创建的用户kkk-sj登录
docker logout 192.168.121.17

docker login 192.168.121.17
或
docker login -u kkk-sj -p Abc123456 http://192.168.121.17

//下载和上传镜像进行测试
docker tag cirros:lastest 192.168.121.17/myproject-song/cirros:v3
docker push 192.168.121.17/myproject-song/cirros:v3
```

![35b8e7d52d704ac48f23e228a16cf18d](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181727044.png)

![346042c99ec34b79a613a3160e507166](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181728153.png)

### 5、查看日志

WEB界面日志，操作日志按时间顺序记录用户相关操作

### 6、修改Harbor.cfg配置文件

要更改Harbour的配置文件中的可选参数时，请先停止现有的Harbour实例并更新Harbor.cfg:然后运行 prepare脚本来填充配置;最后重新创建并启动Harbour 的实例。

```bash
使用docker-compose管理Harbor 时，必须在与 docker-compose.yml相同的目录中运行。
cd /usr/ local/harbor
docker-compose down -v

vim harbor.cfg       #只能修改可选参数

./prepare

docker-compose up -d
//如果有一下报错，需开启防火墙
```

![7d983f648cd5452da372ac0fe997105c](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181728492.png)

![fa467ebfa418479d86a574ac3ec34c28](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181728847.png)

### 7、移除Harbor服务容器同时保留镜像数据/数据库，并进行迁移

```bash
//在Harbor服务器上操作
（1）移除Harbor服务器
cd /usr/local/harbor
docker-compose down -v

(2)把项目中的镜像数据进行打包
//持久数据，如镜像，数据库等在宿主机的/data/目录下，日志在宿主机的/var/log/Harbor/目录下
ls /data/registry/docker/registry/v2/repositories/myproject-song
cd /data/registry/docker/registry/v2/repositories/myproject-song
tar zcvf myproject-song.tar.gz ./*
```

![5354bf3ca55b429c8e5a214232f7b750](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181729271.png)

![3b83f98ce7354072a5982fb1279b7588](https://raw.githubusercontent.com/12cjn/lizituchuang/main/202210181729421.png)

### 如需重新部署，需要移除 Harbor 服务容器全部数据

持久数据，如镜像，数据库等在宿主机的/data/目录下,日志在宿主机的/var/log/Harbor/目录下。

```bash
cd /usr/ local/ harbor
docker-compose down -v
rm -r /data/database
rm -r / data/registry
```

