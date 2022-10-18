之前公司代码的管理不统一，一部分人用SVN，一部分人用Git，对于习惯了使用Linux或者Mac命令行的人来说，Git的操作更方便和快捷，和小伙伴商量了一下把整个代码管理工具切换成了Git，GitHub如果不是开源项目的话是需要付费使用，所以选择使用GitLab，由于公司没有网络安全专家，对公司的网络边界以及代码库进行扫描，如果扫描到邮箱，暴力破解后，可能就会获取代码，所以采用在自己内网搭建GitLab服务的方式，在讲正文之前，先来说说Git和SVN的区别。

### 1、GIT是分布式的，SVN不是：

这是GIT和其它非分布式的版本控制系统，例如SVN，CVS等，最核心的区别。需要做一点声明，GIT并不是目前第一个或唯一的分布式版本控制系统。还有一些系统，例如[Bitkeeper](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.bitkeeper.com%2F), [Mercurial](https://links.jianshu.com/go?to=http%3A%2F%2Fmercurial.selenic.com%2F)等，也是运行在分布式模式上的。但GIT在这方面做的更好，而且有更多强大的功能特征。

GIT跟SVN一样有自己的集中式版本库或服务器。但GIT更倾向于被使用于分布式模式，也就是每个开发人员从中心版本库/服务器上chect out代码后会在自己的机器上克隆一个自己的版本库。可以这样说，如果你被困在一个不能连接网络的地方时，就像在飞机上，地下室，电梯里等，你仍然能够提交文件，查看历史版本记录，创建项目分支等。对一些人来说，这好像没多大用处，但当你突然遇到没有网络的环境时，这个将解决你的大麻烦。

同样，这种分布式的操作模式对于开源软件社区的开发来说也是个巨大的恩赐，你不必再像以前那样做出补丁包，通过email方式发送出去，你只需要创建一个分支，向项目团队发送一个推请求。这能让你的代码保持最新，而且不会在传输过程中丢失。[github.com](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.github.com%2F)就是一个这样的优秀案例。

### 2、GIT把内容按元数据方式存储，而SVN是按文件：

所有的资源控制系统都是把文件的元信息隐藏在一个类似.svn,.cvs等的文件夹里。如果你把.git目录的体积大小跟.svn比较，你会发现它们差距很大。因为,.git目录是处于你的机器上的一个克隆版的版本库，它拥有中心版本库上所有的东西，例如标签，分支，版本记录等。

### 3、GIT分支和SVN的分支不同：

分支在SVN中一点不特别，就是版本库中的另外的一个目录。如果你想知道是否合并了一个分支，你需要手工运行像这样的命令[_svn propget svn:mergeinfo_](https://links.jianshu.com/go?to=http%3A%2F%2Fjan.baresovi.cz%2Fdr%2Fen%2Fsubversion-mergeinfo)，来确认代码是否被合并。感谢Ben同学指出这个特征。所以，经常会发生有些分支被遗漏的情况。

然而，处理GIT的分支却是相当的简单和有趣。你可以从同一个工作目录下快速的在几个分支间切换。你很容易发现未被合并的分支，你能简单而快捷的合并这些文件。

### 4、GIT没有一个全局的版本号，而SVN有

目前为止这是跟SVN相比GIT缺少的最大的一个特征，SVN的版本号实际是任何一个相应时间的源代码快照。我认为它是从CVS进化到SVN的最大的一个突破。

### 5、GIT的内容完整性要优于SVN：

GIT的内容存储使用的是[SHA-1](https://links.jianshu.com/go?to=http%3A%2F%2Fen.wikipedia.org%2Fwiki%2FSHA-1)哈希算法。这能确保代码内容的完整性，确保在遇到磁盘故障和网络问题时降低对版本库的破坏。

一个研发队伍的成员正常包括：需求分析、设计、美工、程序员、测试、实施、运维，每个成员在工作中都有产出物， 包括了文档、设计代码、程序代码，这些都需要按项目集中进行管理的。SVN能清楚的按目录进行分类管理， 使项目组的管理处于有序高效的状态，SVN更适用于项目管理， Git更适用于代码管理。

首先打开公司内网部署GitLab的服务器，由于是内部员工使用，所以注册时候Username和Full name最好用自己的名字，这样管理员给用户分配项目权限的时候能够一目了然。

![image](https://upload-images.jianshu.io/upload_images/6781582-ca30fee1101f4739.png#crop=0&crop=0&crop=1&crop=1&id=JD1oS&originHeight=610&originWidth=1021&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

以管理员的身份登入gitlab，点击Settings，然后选择Members

![image](https://upload-images.jianshu.io/upload_images/6781582-054073a3d940c331.png#crop=0&crop=0&crop=1&crop=1&id=F797x&originHeight=309&originWidth=248&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以通过输入名字选择要分配权限的小组成员，然后分配角色，选择权限有效时间，点击Add to Project就把人员拉近到项目中。GitLab的角色有以下四种：

- Guest：可以创建issue、发表评论，不能读写版本库
- Reporter：可以克隆代码，不能提交，可以赋予测试、产品经理此权限
- Developer：可以克隆代码、开发、提交、push，可以赋予开发人员此权限
- MainMaster：可以创建项目、添加tag、保护分支、添加项目成员、编辑项目，一般GitLab管理员或者CTO才有此权限

![image](https://upload-images.jianshu.io/upload_images/6781582-b9d4cbc699a0ded6.png#crop=0&crop=0&crop=1&crop=1&id=x23eK&originHeight=374&originWidth=1240&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

可以通过HTTP和SSH去做克隆和提交代码，由于HTTP需要每次提交的时候输入邮箱号和密码，所以常用电脑上配置SSH，只要配置好了以后，下次提交的时候就方便了。SSH的方式主要是通过生成一个密钥和一个公钥，这个公钥可以使用在GitHub，GItLab，内网GitLab中。
大多数 Git 服务器都会选择使用 SSH 公钥来进行授权。系统中的每个用户都必须提供一个公钥用于授权，没有的话就要生成一个。生成公钥的过程在所有操作系统上都差不多。首先你要确认一下本机是否已经有一个公钥。
SSH 公钥默认储存在账户的主目录下的 ~/.ssh 目录。进去看看：

![image](https://upload-images.jianshu.io/upload_images/6781582-7ba3cae2bd2c79ea.png#crop=0&crop=0&crop=1&crop=1&id=kYPIK&originHeight=266&originWidth=620&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

看一下有没有id_rsa和id_rsa.pub(或者是id_dsa和id_dsa.pub之类成对的文件)，有 .pub 后缀的文件就是公钥，另一个文件则是密钥。

假如没有这些文件，甚至连 .ssh 目录都没有，可以用 ssh-keygen 来创建。该程序在 Linux/Mac 系统上由 SSH 包提供，而在 Windows 上则包含在GitBash里面里：

```bash
$ ssh-keygen -t rsa -C "6789346623@qq.com"

Creates a new ssh key using the provided email # Generating public/private rsa key pair.

Enter file in which to save the key (/home/you/.ssh/id_rsa):
```

然后直接三次Enter键就可以了，完了之后大概是这样：

```bash
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
The key fingerprint is: # 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db 6789346623@qq.com
```

![image](https://upload-images.jianshu.io/upload_images/6781582-289cf6cfe483b32e.png#crop=0&crop=0&crop=1&crop=1&id=FP7oM&originHeight=288&originWidth=840&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1、查看你生成的公钥：
vim id_rsa.pub
就可以查看到你的公钥
2、登陆GitLab账号，点击用户图像，然后 Settings -> 左栏点击 SSH keys

![image](https://upload-images.jianshu.io/upload_images/6781582-7dbf927dcb6f96b0.png#crop=0&crop=0&crop=1&crop=1&id=JA0Ho&originHeight=310&originWidth=284&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

3、复制公钥内容，粘贴进“Key”文本区域内，取名字
4、点击Add Key

![image](https://upload-images.jianshu.io/upload_images/6781582-050d2c55bd5202f0.png#crop=0&crop=0&crop=1&crop=1&id=npoWk&originHeight=671&originWidth=988&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

1、首先把服务器上的代码克隆下来

```bash
git clone git@192.168.200.109:snailå/GitTest.git
```

刚克隆下来的是在master分支，可以通过命令行或者IDE工具查看当前分支
2、将所有有改动的全部添加到要提交的本地库中

也可以用git add 文件名进行单独文件的提交
3、将修改提交到本地库

```bash
git commit -a -m "提交添加的注释信息"
```

4、将本地库的commit推送到远程服务器

![image](https://upload-images.jianshu.io/upload_images/6781582-0b3c4f307e1f35bb.png#crop=0&crop=0&crop=1&crop=1&id=cNH2y&originHeight=1598&originWidth=1164&originalType=binary&ratio=1&rotation=0&showTitle=false&status=done&style=none&title=)

**拉取服务器上最新资源：**

**在不同的分支之间切换：git checkout 分支名**
注意事项：切换分支的时候，如果当前分支有改动没有提交，是不能切换分支的，需要先把改动的内容提交或者放入缓存区

```bash
git checkout release/v1.0.0
```

**合并分支：git merge 分支名**

从当前分支merge feature/login分支的内容，如果有两个人修改了同一个文件的同一行，则会有冲突，可以在IDE工具上先解决当前冲突然后再提交。

首先申请账号，然后在自己的账号里面加入ssh key，让管理员开通项目的权限，然后就可以克隆项目，然后提交了。git的分支分类型分为以下几种：

- master 主分支，有且只有一个
- release 线上分支，一般为线上版本，线上版本发布后，会讲release分支合并到master
- develop 开发分支，通产给测试部署环境或者打包的分支，每个人在自己的分支上开发完成后，向develop分支合并
- feature 通常是一个功能分支或者个人分支，每个公司的用法不一样，feature分支一般会有很多个，通常merge完成后会删除
**在使用git的过程中，出现任何问题，最直观的就是查看提示信息，git的提示信息非常强大，刚开始看的时候可能会角色英文有点难，看到了就习惯了。保持良好的习惯，每次开发之前先更新，经常提交，不要一次提交很多文件，基本上简单实用就不会出现问题。git功能很强大，每个公司的用法以及流程都不一样，有的只是简单使用，有的使用Code Review进行代码审核，此文主要针对不了解GitLab的用户，让其能够快速的上手，不喜勿喷，谢谢**
