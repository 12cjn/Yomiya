# Gitalk

Gitalk，一个现代化的，基于Preact和Github Issue的评论系统。

#### 在 `index.html`里配置Gitalk插件

```html
# 添加在head中
<!-- Gitalk css -->
<link rel="stylesheet" href="https://wugenqiang.gitee.io/notebook/plugin/gitalk.css">
# 配置script
<!-- plugin：gitalk(用户评论) -->
<script src="https://wugenqiang.gitee.io/notebook/plugin/gitalk.js"></script>
<script src="https://wugenqiang.gitee.io/notebook/plugin/gitalk.min.js"></script>
<script src="https://wugenqiang.gitee.io/notebook/plugin/md5.min.js"></script>
<script>
    // title_id需要初始化
    window.title_id = window.location.hash.match(/#(.*?)([?]|$)/) ? window.location.hash.match(/#(.*?)([?]|$)/)[1] : '/';
    const gitalk = new Gitalk({
        clientID: 'd028a1a2d9393917b6ec', // Github 应用程序客户端 ID
        clientSecret: '7e90fd31c29b8c931c24d1b4718409850b4414b6', // Github 应用程序客户端密码
        repo: 'mydocsifytalk', // Github 仓库
        owner: '12cjn', // Github 仓库所有者
        admin: ['12cjn'], // Github repo 合作者，只有这些人才能初始化 github issues
        title: decodeURI(window.title_id),
        // facebook-like distraction free mode
        distractionFreeMode: false,
        id: md5(window.location.hash),  // 页面的唯一标识，gitalk 会根据这个标识自动创建的issue的标签,我们使用页面的相对路径作为标识
        enableHotKey: true, // 提交评论快捷键(cmd/ctrl + enter)
        proxy: 'https://12cjn.github.io/Yomiya/login/oauth/access_token', // 代理配置
    })
    // 监听URL中hash的变化，如果发现换了一个MD文件，那么刷新页面，解决整个网站使用一个gitalk评论issues的问题。
    window.onhashchange = function (event) {
        if (event.newURL.split('?')[0] !== event.oldURL.split('?')[0]) {
            location.reload()
        }
    }
</script>
```

> 其中的`proxy: 'https://12cjn.github.io/Yomiya/login/oauth/access_token'`是一个大坑，最好需要自己配置nginx反向代理，下面演示如何操作

在反向代理的网站服务器上进行nginx设置

```bash
vim /etc/nginx/sites-enabled/default
# 最好备份一下default
```

```bash
location /login/oauth/access_token {
	# First attempt to serve request as file, then
	# as directory, then fall back to displaying a 404.
    #try_files $uri $uri/ =404;
    add_header Access-Control-Allow-Origin 'https://12cjn.github.io/Yomiya';
    add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';
    add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization';
    if ($request_method = 'OPTIONS') {
    return 204;
    }
    proxy_pass https://github.com;
}
```

`add_header Access-Control-Allow-Origin 'https://12cjn.github.io/Yomiya'`这里的域名设置成自己docsify的

在gitalk配置的proxy中替换 proxy: '**https://12cjn.github.io/Yomiya**/login/oauth/access_token' 里的域名为上面的即可

![image-20221019115036845](https://raw.githubusercontent.com/12cjn/lizituchuang/main/img/202210191150047.png)

可以尝试了！！
