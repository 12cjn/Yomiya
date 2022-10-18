# 容器的使用
### 获取镜像
如果我们本地没有 ubuntu 镜像，我们可以使用 **docker pull** 命令来载入 ubuntu 镜像：
```bash {.line-numbers}
docker pull ubuntu
```
![docker-container-run.png](https://s2.loli.net/2022/10/18/J4qy6vDePL7CsmS.png)
### 启动容器
以下命令使用 ubuntu 镜像启动一个容器，参数为以命令行模式进入该容器：
```bash {.line-numbers}
docker run -it ubuntu /bin/bash
```
参数说明：

- -i：交互式操作
- -t：终端
- ubuntu：ubuntu镜像
- /bin/bash：放在镜像名后的是命令，这里我们希望有个交互式Shell，因此用的是/bin/bash

要退出终端，直接输入**exit**：
```bash {.line-numbers}
root@ed09e4490c57:/# exit
```
![docker-container-exit.png](https://s2.loli.net/2022/10/18/sQIRNE6W5hkoXYi.png)

sequenceDiagram
  View->>IService: 发送请求
  IService->>ServiceImpl: 服务层处理
  ServiceImpl->>logic: 逻辑层处理
  logic->>Dao: 持久层处理
  Dao->>Mapping.xml: 调用知道的sql语句
  note right of Mapping.xml: 数据库交互
  Mapping.xml-->>Dao: 与数据库交互完毕返回
  Dao-->>logic: 持久层交互完毕返回
  logic-->>ServiceImpl: 逻辑层交互完毕返回
  ServiceImpl-->>IService: 服务交互完毕返回
  IService-->>View: 请求处理完毕，返回

gantt
    dateFormat  MM-DD
    title XXX需求开发计划甘特图(2018年06月12日 - 2018年07月06日)
    section 现场
        接收需求 :done, desc1,06-12,2h
        上线验证 :after desc14,2d
    section 需求分析
        需求概要分析-工作量评估(分析) :done,desc2,after desc1,3h
        需求详细分析SRS :crit,done,desc6,after desc2,3d  
        需求评审 :done,descp,after desc6,3h
        输出需求评审结果 :done,desc7,after descp,10h
        设计评审 :done,active,descdesign,after desc8,3h
    section 需求设计
        工作量评估(设计开发) :done,desc3,after desc1,5h
        需求评审 :done,after desc6,3h
        输出需求设计方案 :crit,done,desc8,after desc7,4d
        设计评审 :done,descdesign,after desc8,3h
        输出设计评审结果 :done,descdp,after descdesign,12h
        关键点把控1 :active,crit,06-21,1d
        关键点把控2 :crit,crit2,06-24,5h
        关键点把控3 :crit,crit3,06-27,5h
    section 需求开发
        设计评审 :done,descdesign,after desc8,3h
        需求开发 :active,desc10,after descdp,10d
        bug解决 : after desc10,2d
    section 质量管理
        工作量评估(测试) :done,desc4,after desc1,3h 
        需求评审 :done,after desc6,3h
        设计评审 :done,descdesign,after desc8,3h
        输出测试用例 :crit,active,desc9,after descdp,2d
        第一部分测试 :after crit2,2d
        第二部分测试 :after crit3,2d
        其他部分和整体测试 :desc14,after desc10,3d