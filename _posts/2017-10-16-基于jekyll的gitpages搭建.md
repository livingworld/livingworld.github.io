---
layout: post
title:  "基于jekyll的Github Pages博客配置"
date:   2017-10-16 12:41:00 +0800
categories: github
tag: [jekyll]
---

# 配置
## 环境配置
### 依赖环境
- 1.需要判断环境中是否包含Ruby环境，在命令行中：
    $ ruby --version

- 2.若未安装，windows用户前往[RubyInstallers](https://rubyinstaller.org/downloads/)，下载安装Ruby。
- 3.Install Bundler:
    $ gem install bundler
    # Installs the Bundler gem

### 步骤一：创建本地博客网站仓库
- 1.为了预览博客效果，必须在本地实现预览功能，需要创建jekyll site文件于本地:
    $ bundle exec jekyll _3.3.0_ new NEW-JEKYLL-SITE-REPOSITORY-NAME
- 2.首先配置好git环境，初始化仓库
    $ git init your-site-repository-name
- 3.切换至仓库内部

### 步骤二：
- 1.判断是否有Gemfile位于博客仓库内。
- 2.若无，则创建相应文件名，填入如下内容：
    source 'https://rubygems.org'
    gem 'github-pages', group: :jekyll_plugins
- 3.若包含，则填入如下内容：
    source 'https://rubygems.org'
    gem 'github-pages', group: :jekyll_plugins
- 4.从Github Pages gem中安装jekyll和其他依赖库
    $ bundle install
    # 遇到问题，输入bundle update可以解决冲突。

### 步骤三：构建本地jekyll网站
    $ bundle exec jekyll serve

### 步骤四：保持GitHub  Pages gem最新
    $ bundle update

### 下一步：配置jekyll

### 参考
- [Customizing GitHub Pages](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll)


## 功能完善
### categories
- 1.分类名称设置
```
layout: post
title: My ways to live life.
categories: Personal
```
- 2.创建分类页
命名为`categories.html`,复制如下代码
```
---
layout: page
permalink: /categories/
title: Categories
---

<div id="archives">
{% for category in site.categories %}
  <div class="archive-group">
    {% capture category_name %}{{ category | first }}{% endcapture %}
    <div id="#{{ category_name | slugize }}"></div>
    <p></p>
    
    <h3 class="category-head">{{ category_name }}</h3>
    <a name="{{ category_name | slugize }}"></a>
    {% for post in site.categories[category_name] %}
    <article class="archive-item">
      <h4><a href="{{ site.baseurl }}{{ post.url }}">{{post.title}}</a></h4>
    </article>
    {% endfor %}
  </div>
{% endfor %}
</div>
```


- 参考：[3 Simple Steps To Setup Jekyll Categories And Tags](https://blog.webjeda.com/jekyll-categories/)


### tags


- 参考：[Use Tags and Categories in your Jekyll based Github Pages without plugins](https://codinfox.github.io/dev/2015/03/06/use-tags-and-categories-in-your-jekyll-based-github-pages/)


### archives
- 功能描述:存档Jekyll标签和类别的页面
- 首先，在Gemfile文件中，添加如下文件：
```
gem "jekyll-archives"
```
- 其次，运行如下命令：
```
$ bundle install #进行捆绑安装
$ bundle exec jekyll build 
$ bundle exec jekyll serve #预览
```

- 参考：[jekyll-archives](http://jekyll.github.io/plugins/plugins/jekyll-archives/)


### paginate







