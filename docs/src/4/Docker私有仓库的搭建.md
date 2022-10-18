> 注意：
> 本文档部署环境以Vmware16里的Ubuntu18.04系统为例
> 解释器为 /usr/bin/zsh

# 一、安装Docker

- 部署Docker
[Ubuntu18.04部署Docker](https://www.yuque.com/penghan-jnwgj/hscmnp/dca01o?view=doc_embed)

- 运行Docker下面是常用命令：
[Docker常用命令](https://www.yuque.com/penghan-jnwgj/hscmnp/gexn4r?view=doc_embed)

# 二、搭建仓库

- harbor完整的搭建参考下面的文章
[Docker搭建本地仓库Harbor](https://www.yuque.com/penghan-jnwgj/hscmnp/05bf8772-7453-42c6-9413-f5cdd7cf2124?view=doc_embed)

> 这里采用Registry私有镜像仓库 虽然功能简单 但部署快速且容易

```bash
# 1.下载Registry镜像
docker pull registry

# 2.运行Registry镜像
docker run -d --name registry -p 5000:5000 -v /storage/registry:/tmp/registry registry

# 3.查看仓库中的所有镜像
curl http://127.0.0.1:5000/v2/_catalog
```
# 三、配置仓库可以直接通过http方式访问
docker默认是传输方式使用https协议，我们手头上没有sttps证书，所以此处不配置https证书，直接设置可信源，使我们内网可以通过http方式访问
```bash
# 1.修改daemon.json，没有该文件则需新建
vim /etc/docker/daemon.json
#在文件中添加以下内容
#{ 
#    "insecure-registries" : [ "your-server-ip:5000" ] 
#}
{ 
    "insecure-registries" : [ "192.168.138.135:5000" ] 
}
```

```bash
# 2.重新加载、重启docker、启动镜像
systemctl daemon-reload
systemctl restart docker
docker start registry
```
# 四、上传镜像至仓库
```bash
# 1.查看镜像
docker images

# 2.将要上传的镜像打Tag
#docker tag your-image-name:tagname your-server-ip:5000/your-image-name:tagname
docker tag mysql:latest 192.168.138.135:5000/mysql:latest

# 3.把镜像推送到仓库
#docker push your-registry-server-ip:5000/your-image-name:tagname
docker push 192.168.138.135:5000/mysql:latest

# 4.验证是否推送成功
#curl http://your-server-ip:5000/v2/_catalog
curl http://192.168.138.135:5000/v2/_catalog
```
可以看见{"repositories":["mysql"]},说明已经推送成功,有一个registry镜像了
# 五、客户端拉取仓库镜像
现在在本机测试能否下载刚刚上次的镜像，如果此处是在另一台客户机下载，注意要配置http方式访问
```bash
# 1. 修改vim /etc/docker/daemon.json添加以下内容:
{ 
    "insecure-registries" : [ "192.168.138.135:5000" ] 
}

# 2. 重新加载docker
systemctl daemon-reload
systemctl restart docker

# 3.下载镜像
docker pull 192.168.138.135:5000/mysql:latest
```

