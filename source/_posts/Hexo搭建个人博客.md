---
title: Hexo+Github_Pages搭建个人博客
date: 2020-12-31 15:18:06
tags: 
---

## 前言
这应该是我的第一篇个人博客，秉承着“等准备好了在做，就永远开始不了”的经验，我就算草率开工了。这篇博客的内容应该不能算得上是个教学，内容是我 *搭建写博客的环境* 的整个过程。
#### Hexo
Hexo是一个可以用 markdown 文档转换成静态 Html 页面的工具。它可以让我们执行几个命令就能做到这个转换过程，而且主题样式、导航、时间版权等信息都可以通过配置文件来直接映射到最终的Html页面上。所以当我们想以 markdown 格式书写文档，并且想以html的方式上传到网上时，Hexo是一个很好的工具。
#### GitHub Pages
GitHub如果理解成是一个仓库，而当我们需要一个类似于 首页 来向访问者介绍我们仓库的大致内容时，GitHub官网提供了 GitHub Pages 让我们实现这个愿望。我们可以将一个静态页面托管到GitHub的服务器上，并且可以配置自己的域名映射。
#### 为什么选用 Hexo + GitHub Pages
稍加学习，便可以用markdown写出格式清晰，便于维护的文档，用 Hexo 实现将.md的文件转换成.html页面的过程，再将 Html页面 以GitHub Pages的方式托管到GitHub的服务器上，免去购买服务器的费用。

*****
## 搭建过程
### 第一步：用 Hexo 生成 HTML
1.全局安装 Hexo
`npm install hexo-cli -g`

2.验证安装是否成功
`hexo -v`

3.这里可以新建一个文件夹使用 `hexo init` 的方式来初始化项目，默认使用https的方式下载脚手架代码库，而我这里配置访问github的端口是443 *（git方式，https好像是22）*，所以我到 hexo 代码库中直接通过git的方式拉取
`git clone git@github.com:hexojs/hexo-starter.git`

4.进入脚手架文件夹, `npm install` 下载工具包， `hexo g`*(hexo generate)* 生成Html文件， `hexo s`*(hexo server)* 启动本地服务，便可以通过[http://localhost:4000/](http://localhost:4000/) 来访问html文件。

*****

### 第二步：使用Github Pages启动自己的在线页面
1.新建一个代码库，名字为 `YourNickname.github.io` *(名字是一定的，名字对的情况下，UI会有弹框提示 You've discovered a secret...)*

2.设置需要添加readme.md文件 *(最开始的内容就是你的 readme 的内容，之后我们会拿自己的  html文件替换readme文件)*

3.代码库建成后，可以在该代码库的setting中查看自己介绍页的地址，*图片后续上传...*

*****

### 第三步：将本地博客文件部署到线上仓库
1.可以先添加一篇博客内容
`hexp new post "my first blog"` *双引号中填写博客内容*

2.下载工具帮助我们完成上传 *（可以通过git将html、css、js...文件上传到github上，工具可以快速帮助我们完成部署）*
`npm i hexo-deployer-git`

3.修改 `_config.yml` 配置，连接线上仓库, repository 是自己的github仓库地址，branch 是对应的分之
``````
deploy:
type: git
repository: git@github.com:Thr10/Thr10.github.io.git
branch: master
``````

4.执行 `hexo d` *(hexo deploy)* 将生成的静态页面部署到你的线上仓库

*****

### 第四步：备份
1.执行 `hexo d`，发现工具仅仅将生成的静态页面文件上传到了仓库中，如何备份整个代码库呢？

2.首先在github 的该仓库下，新建一个分支，例如 hexo 分支。

3.将该代码库拉取到本地，例如 `Thr10.github.io` ，将本地的Blog代码库中的 `_config.yml`、`themes/`、`source`、`scffolds/`、`package.json`、`gitignore`文件或者文件夹拷贝到 `Thr10.github.io` 中。*(Mac：`command` + `shift` + `.` 可以展示隐藏文件)*

4.将 themes/themeName/ 下的 `.git/` 文件夹删除，否则无法 push 主题文件夹

5.随后 `npm install`, `npm install hexo-deployer-git`, 新建分支，切换分支，关联远端hexo分支，`add .`, `commit`, `push`一系列常规git操作。

6.在新的电脑上，拉取代码库，`npm install hexo-cli -g`, `npm install`, `npm install hexo-deployer-git`，新的环境就搭建好了。

*****


## Q & A
#### Q: 如何用一台电脑配置两个gitlab的ssh钥匙？
1.生成第二个key
``````
ssh-keygen -t rsa -C "yourmail@mail.com"
``````
注意不要一直会车，否则会使用默认的路径，结果是覆盖之前生成的 key。这次要手动填写路径加以区分
``````
Generating public/private rsa key pair.
Enter file in which to save the key (/c/Users/Gary/.ssh/id_rsa): /c/Users/Gary/.ssh/id_rsa_github
``````
结果会在 `~/.ssh` 目录下形成两套 key

2.创建config文件
在 `~/.ssh` 目录下创建名为 `config` 的文件。内容入下
``````
# gitee and github
# 注意：多个Host公用一个公钥时，
# 对应的HostName和Port必须跟在Host后面
    Host gitee.com
    HostName gitee.com
    Host github.com
    HostName github.com
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa

# gerrit
    Host gerrit.awaimai.com
    HostName gerrit.awaimai.com
    Port 8000
    PreferredAuthentications publickey
    IdentityFile ~/.ssh/id_rsa_gerrit

# 其他的统一用id_rsa文件登陆，或者密码。
# 注意：这里的第二行后面有个password，就是同时允许密码登陆
# 不然第一次没加公钥，密码也登陆不了，会提示错误：Permission denied (publickey,password).
    Host *
    PreferredAuthentications publickey,password
    IdentityFile ~/.ssh/id_rsa
``````
其中，`Host`和`HostName`填写git服务器的域名。

`IdentityFile`指定私钥的路径。


#### Q: GitHub连接不上？
使用命令 `ssh -T git@github.com` 来查看连接情况，可以在后边加`-v` 查看过程中具体报错以便调试

报错`ssh: connect to host github.com port 22: Connection timed out`
修改`config`连接github的配置
``````
Host github.com
 Hostname ssh.github.com
 Port 443
``````
#### Q: GitHub仓库无能推送代码?
如果能拉取代码，不能推送代码，可以在仓库中设置ssh-key













