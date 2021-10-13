---
title:  Build a blog with jekyll
tags:   jekyll 
---

## Github Pages with Jekyll

为了搭建自己的博客，我们需要借助 [jekyll](https://jekyllrb.com/) 这个工具，以及 `github` 提供的静态站点托管服务 [Github Pages](https://docs.github.com/cn/pages/getting-started-with-github-pages/about-github-pages)。

### 第一步：创建 github 账号

此处不做介绍，提供传送门：[github](https://github.com/)。

### 第二步：创建 github pages 的 repositorie

此处可参考 [github 官方](https://docs.github.com/cn/pages/getting-started-with-github-pages/about-github-pages)。

登入 github 后，在网页的左上角点击 `New`

![New-repo](/assets/images/blog/github-new-repo.png))

将项目名字设为：`username.github.io`

![Repo-name](/assets/images/blog/github-new-repo1.png)

注意这里的 `username` 指的是你 github 的名字，不清楚的可以点击 github 主页右上角的头像查看。

项目新建完之后，进入 `Settins` ，在侧边栏中找到 `Pages` ，点击 `Choose a theme` 选择一个喜欢的主题。
我们也可以先随意选取一个主题，之后我们仍然能够进行更换。

![Settings](/assets/images/blog/github-new-repo2.png)

随后我们可以在项目的 `Code` 下面可以找到 `_config.yml` 和 `index.md` 文件。
其中 `_config.yml` 是我们主要进行配置的地方，`index.md` 相当于是我们博客主页。
更多的内容请参考 [github 官方](https://docs.github.com/cn/pages/getting-started-with-github-pages/about-github-pages)。

### 第三步：安装 jekyll

`Jekyll` 是一个静态站点生成器，内置 `Github Pages` 支持和简化的构建过程。`Jekyll` 使用 Markdown 和 HTML 文件，并根据您选择的布局创建完整静态网站。参考[这里](https://docs.github.com/cn/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)。

此处使用 `ubuntu` 安装 `jekyll`

打开终端，配置环境：

安装 ruby 和其他依赖包。

```bash
sudo apt-get install ruby-full build-essential zlib1g-dev
```

避免使用 `root` 安装 `RubyGems(gems)`，我们需要安装 `gems` 到本地用户的文件夹下。
以下我们将环境变量写入到 `~/.bashrc` 或者是 `~/.zshrc` 中去，至于写入哪个文件，需要看你使用的 `shell` 是 `bash` 还是 `zsh`。当然你也可以直接修改 `~/.bashrc` 或者 `~/.zshrc` 文件，需要注意的是传入的是单引号内的内容。

```bash
echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
source ~/.bashrc
```

完成以上步骤之后，安装 `jekyll` 和 `bundler`

```bash
gem install jekyll bundler
```

安装步骤参考[这里](https://jekyllrb.com/docs/installation/ubuntu/)。

### 第四步：使用 jekyll && 更换主题

个人认为使用 `jekyll` 只是为了新增网页内容，美化主题。没必要花太多的时间去美化网页，相比之下应该花更多的时间去撰写博客。所以在这里推荐直接使用别人写好的主题，这里提供 `jekyll` 的[主题链接](http://jekyllthemes.org/)。

本人使用的主题是[这个](https://tianqi.name/jekyll-TeXt-theme/test/)，下面以这个为例简单介绍一下。

我们打开终端，将喜欢的主题克隆至本地

```bash
git clone https://github.com/kitian616/jekyll-TeXt-theme.git
```

克隆完成之后进入文件夹中，我们可以大致看到以下的文件结构

```
    - _data         
    - _includes     
    - _layouts
    - _posts        存放博客的文件夹
    - _sass         网站的主题样式
    - assets        存放资源的文件夹，比如博客使用的图片等
    - _config.yml   总的配置文件
    - 404.html      404 页面
    - about.md      网站中关于的界面
    - Gemfile       
    - index.html    网站主页
```

由于未理解透彻，所以有一些文件夹无法解释清楚，还请查看[官方文档](https://jekyllrb.com/docs/)

不喜欢折腾的只需要关注 `_posts` 这个文件夹即可，这是我们存放博客的文件夹。在这里，博客的命名有自己特定的规则，比如说博客文件的后缀必须为 `.md`, `.markdown`, `.html`，前边命名为 `年-月-日-名字`，比如说 `2021-10-13-welcome.md`。

一般来说，下好喜欢的主题之后修改 `_config.yml` 中的作者信息就基本大功告成了，当然你也可以修改 404 的界面，或者网站的主页等等。

更多的介绍请参考[官方文档](https://jekyllrb.com/docs/)。

更多的资源请查看[资源](https://jekyllrb.com/resources/)。

`jekyll` 还有许多的功能，这里就不进行介绍了（其实只是我不会罢了），如果需要请查看上方的官方文档。
