# 容器的使用
### 获取镜像
如果我们本地没有 ubuntu 镜像，我们可以使用 **docker pull** 命令来载入 ubuntu 镜像：
```bash
docker pull ubuntu
```
![docker-container-run.png](https://cdn.nlark.com/yuque/0/2022/png/32635561/1662013457698-09d7600e-579c-414d-8d35-f02a4e016865.png#clientId=uf3bda238-03d7-4&crop=0&crop=0&crop=1&crop=1&from=drop&id=u9526d36a&margin=%5Bobject%20Object%5D&name=docker-container-run.png&originHeight=56&originWidth=1000&originalType=binary&ratio=1&rotation=0&showTitle=false&size=2888&status=done&style=none&taskId=u05d60cd4-bf16-421b-9043-1f956b1b614&title= ':ignore')
### 启动容器
以下命令使用 ubuntu 镜像启动一个容器，参数为以命令行模式进入该容器：
```bash
docker run -it ubuntu /bin/bash
```
参数说明：

- -i：交互式操作
- -t：终端
- ubuntu：ubuntu镜像
- /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式Shell，因此用的是/bin/bash

要退出终端，直接输入**exit**：
```bash
root@ed09e4490c57:/# exit
```
![docker-container-exit.png](https://cdn.nlark.com/yuque/0/2022/png/32635561/1662013461825-f89cd29e-4b26-4dc3-93c6-42add337490a.png#clientId=uf3bda238-03d7-4&crop=0&crop=0&crop=1&crop=1&from=drop&id=uf38adafc&margin=%5Bobject%20Object%5D&name=docker-container-exit.png&originHeight=77&originWidth=1001&originalType=binary&ratio=1&rotation=0&showTitle=false&size=3143&status=done&style=none&taskId=u0be14ee6-2edc-41c7-8a32-dff9ab5c948&title= ':ignore')
