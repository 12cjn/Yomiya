# 手动部署
在新的主机上首次安装 [Docker](https://so.csdn.net/so/search?q=Docker&spm=1001.2101.3001.7020) 之前，需要设置 Docker 存储库。之后，可以从存储库安装和更新 Docker。

> 设置存储库

```bash
1、更新包索引并安装包，以允许通过 HTTPS 使用存储库：
sudo apt-get update
 
sudo apt-get install \
ca-certificates \
curl \
gnupg \
lsb-release
 
2、添加 Docker 的官方 GPG 密钥：
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg

3、使用以下命令设置稳定存储库。
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

> 安装Docker引擎

```bash
1、更新包索引，并安装最新版本的 Docker 引擎和容器，或转到下一步以安装特定版本。
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io

2、若要安装特定版本的Docker 引擎，请在存储库中列出可用版本，然后选择并安装：
（1)列出存储库中可用的版本。
apt-cache madison docker-ce
（2）使用版本字符串安装特定版本，例如 5:18.09.1~3-0~ubuntu-xenial。
sudo apt-get install docker-ce=<VERSION_STRING> docker-ce-cli=<VERSION_STRING> containerd.io

3、通过运行镜像验证 Docker 引擎是否已正确安装。
sudo docker run hello-world
docker version

4、配置国内加速源，并重启docker使配置生效
sudo echo \
{
"registry-mirrors": ["http://hub-mirror.c.163.com"]
} \
>> /etc/docker/daemon.json

sudo service docker restart
sudo docker version
sudo docker ps -a
```

> [Install Docker Engine on Ubuntu（英文教程）](https://docs.docker.com/engine/install/ubuntu/)
[Install Docker Engine on CentOS（英文教程）](https://docs.docker.com/engine/install/centos/)

# 脚本部署
### 准备脚本文件

- 下方链接下载脚本

[Ubuntu18.04_Docker_install.zip](https://www.yuque.com/attachments/yuque/0/2022/zip/32635561/1663553926423-ec5d7ae9-b76c-4766-ae77-883a37b2b909.zip)

- 将文件包解压缩

![image1.png](https://s2.loli.net/2022/10/18/y7h5c4pfZF3TGCN.png)

- 查看 **README **文件

![image2.png](https://s2.loli.net/2022/10/18/M4fKT7H1qtOiFSs.png)
```bash
#解释器/usr/bin/zsh
#执行授权
sudo chmod +x ./install.sh
#安装
./install.sh
```

- 根据文件内容执行即可

