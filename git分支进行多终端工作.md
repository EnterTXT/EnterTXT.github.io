作者：直上云霄  
链接：https://www.zhihu.com/question/21193762/answer/489124966  
来源：知乎  
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。  

## **git分支进行多终端工作**

问题来了，如果你现在在自己的笔记本上写的博客，部署在了网站上，那么你在家里用台式机，或者实验室的台式机，发现你电脑里面没有博客的文件，或者要换电脑了，最后不知道怎么移动文件，怎么办？

在这里我们就可以利用git的分支系统进行多终端工作了，这样每次打开不一样的电脑，只需要进行简单的配置和在github上把文件同步下来，就可以无缝操作了。

## **机制**

机制是这样的，由于`hexo d`上传部署到github的其实是hexo编译后的文件，是用来生成网页的，不包含源文件。

![](https://pic4.zhimg.com/80/v2-32ab30d0d28b916a204c03de4ed0fc4f_720w.jpg?source=1940ef5c)

也就是上传的是在本地目录里自动生成的`.deploy_git`里面。

其他文件 ，包括我们写在source 里面的，和配置文件，主题文件，都没有上传到github

![](https://pica.zhimg.com/80/v2-59bb330178a4e010d9818911f789082b_720w.jpg?source=1940ef5c)

所以可以利用git的分支管理，将源文件上传到github的另一个分支即可。

## **上传分支**

首先，先在github上新建一个[hexo](https://www.zhihu.com/search?q=hexo&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A489124966%7D)分支，如图：

![](https://picx.zhimg.com/80/v2-ebb3e05632e85ab036663390305caa1c_720w.jpg?source=1940ef5c)

然后在这个仓库的settings中，选择默认分支为hexo分支（这样每次同步的时候就不用指定分支，比较方便）。

![](https://picx.zhimg.com/80/v2-1899b6219f3787832652813b958b9b3d_720w.jpg?source=1940ef5c)

然后在本地的任意目录下，打开git bash，

```text
git clone git@github.com:ZJUFangzh/ZJUFangzh.github.io.git
```

将其克隆到本地，因为默认分支已经设成了hexo，所以clone时只clone了hexo。

接下来在克隆到本地的`ZJUFangzh.github.io`中，把除了.git 文件夹外的所有文件都删掉

把之前我们写的博客源文件全部复制过来，除了`.deploy_git`。这里应该说一句，复制过来的源文件应该有一个`.[gitignore](https://www.zhihu.com/search?q=gitignore&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A489124966%7D)`，用来忽略一些不需要的文件，如果没有的话，自己新建一个，在里面写上如下，表示这些类型文件不需要git：

```text
.DS_Store
Thumbs.db
db.json
*.log
node_modules/
public/
.deploy*/
```

注意，如果你之前克隆过[theme](https://www.zhihu.com/search?q=theme&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A489124966%7D)中的主题文件，那么应该把主题文件中的`.git`文件夹删掉，因为git不能嵌套上传，最好是显示隐藏文件，检查一下有没有，否则上传的时候会出错，导致你的主题文件无法上传，这样你的配置在别的电脑上就用不了了。

而后

```text
git add .
git commit –m "add branch"
git push 
```

这样就上传完了，可以去你的github上看一看hexo分支有没有上传上去，其中`node_modules`、`public`、`db.json`已经被忽略掉了，没有关系，不需要上传的，因为在别的电脑上需要重新输入命令安装 。

![](https://picx.zhimg.com/80/v2-a94330ca825f4debde8ce7ceeb8f8394_720w.jpg?source=1940ef5c)

这样就上传完了。

## **更换电脑操作**

一样的，跟之前的环境搭建一样，

- 安装git

```text
sudo apt-get install git
```

- 设置git全局邮箱和用户名

```text
git config --global user.name "yourgithubname"
git config --global user.email "yourgithubemail"
```

- 设置ssh key

```text
ssh-keygen -t rsa -C "youremail"
#生成后填到github和coding上（有coding平台的话）
#验证是否成功
ssh -T git@github.com
ssh -T git@git.coding.net #(有coding平台的话)
```

- 安装nodejs

```text
sudo apt-get install nodejs
sudo apt-get install npm
```

- 安装hexo

```text
sudo npm install hexo-cli -g
```

但是已经不需要初始化了，

直接在任意文件夹下，

```text
git clone git@………………
```

然后进入克隆到的文件夹：

```text
cd xxx.github.io
npm install
npm install hexo-deployer-git --save
npm install --save hexo-renderer-jade hexo-generator-feed hexo-generator-sitemap hexo-browsersync hexo-generator-archive
```

生成，部署：

```text
hexo g
hexo d
```

然后就可以开始写你的新博客了

```text
hexo new newpage
```

**Tips:**

1. 不要忘了，每次写完最好都把[源文件](https://www.zhihu.com/search?q=%E6%BA%90%E6%96%87%E4%BB%B6&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A489124966%7D)上传一下

```text
git add .
git commit –m "xxxx"
git push 
```

1. 如果是在已经编辑过的电脑上，已经有clone文件夹了，那么，每次只要和远端同步一下就行了

```text
git pull
```
