---
layout: post
title:  "基于jekyll的Github Pages博客配置"
date:   2017-10-16 12:41:00 +0800
categories: jekyll update
---


# 配置
## 依赖环境
- 1.需要判断环境中是否包含Ruby环境，在命令行中：
    $ ruby --version

- 2.若未安装，windows用户前往[RubyInstallers](https://rubyinstaller.org/downloads/)，下载安装Ruby。
- 3.Install Bundler:
    $ gem install bundler
    # Installs the Bundler gem

## 步骤一：创建本地博客网站仓库
- 1.为了预览博客效果，必须在本地实现预览功能，需要创建jekyll site文件于本地:
    $ bundle exec jekyll _3.3.0_ new NEW-JEKYLL-SITE-REPOSITORY-NAME
- 2.首先配置好git环境，初始化仓库
    $ git init your-site-repository-name
- 3.切换至仓库内部

## 步骤二：
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

## 步骤三：构建本地jekyll网站
    $ bundle exec jekyll serve

## 步骤四：保持GitHub  Pages gem最新
    $ bundle update

## 下一步：配置jekyll

# 参考文献
[Customizing GitHub Pages](https://help.github.com/articles/setting-up-your-github-pages-site-locally-with-jekyll)
