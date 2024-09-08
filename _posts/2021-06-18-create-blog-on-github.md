---
layout: post
title:  "create blog on github"
date:   2021-06-18 18:41:47 +0800
categories: [github blog]
tags: [github, blog]
---
# 在 Github 上建立博客

1. 在 Github 任何页面下，使用右上角  ‘+’  下拉菜单选择 **New repository（新建仓库）**
2. 选择仓库的名称**（注意一定要以 .github.io 结尾）**
3. 建立完成后，使用 Git Bash 克隆到本地
4. 在 win10 WSL 中安装 Ruby 环境

	```shell
	$ sudo apt-get install ruby-full build-essential zlib1g-dev
	# 安装 Ruby 以及配套软件
	$ echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
	$ echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
	$ echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
	$ source ~/.bashrc
	# 配置环境变量以及 Gem 安装目录至 .bashrc
	$ gem install jekyll -v ‘3.9.0’
	$ gem install bundler -v ‘2.1.2’
	# 使用 Ruby 包管理器 Gem 安装 jekyll 和 bundler （版本太高 GitHub 不支持）
	```

5. 将网上下载好的 jekyll theme 拷贝到 NAME.github.io 目录中（步骤 3克隆到本地的目录）

6. 使用 jekyll 命令构建静态网站（将来可以本地调试）

	```shell
	$ jekyll new . --force
	# --force 选项是当目录非空时强制构建的命令选项
	```
   如果有报错，则需要手动安装依赖的包

7. 打开 Jekyll 创建的 Gemfile 文件（就在当前目录下）
8. 将 "#" 添加到以 `gem "jekyll "` 开头的行首，以注释此行
9. 编辑以 `# gem "github-pages"` 开头的行来添加 `github-pages` gem。 将此行更改为：

	```ruby
	gem "github-pages", "~> GITHUB-PAGES-VERSION", group: :jekyll_plugins
	```

	将 *GITHUB-PAGES-VERSION* 替换为 `github-pages` gem 的最新支持版本

	您可以在这里找到这个版本：“[依赖项版本](https://pages.github.com/versions/)”

	正确版本 Jekyll 将安装为 `github-pages` gem 的依赖项。

10. 保存并关闭 Gemfile

11. 从命令行运行 `bundle install` 或者 `bundle update` 

    开发过程中如需安装某个gem最好指定相应的版本号，然后bundle install，如需更新某个gem，可以在Gemfile里修改其版本号然后bundle install或者单独更新(bundle update[gem name])，然后测试一下，不要直接bundle update，这样会更新所有gem然后更新Gemfile.lock文件，在程序运行时可能会引发其它问题或引入新的bug

12. 在本地运行您的 Jekyll 站点。

	```shell
	$ bundle exec jekyll serve
	```

13. 要预览站点，请在 web 浏览器中导航到 `http://localhost:4000`

14. 调试完成后，提交修改至 Github

	```shell
	$ git add .
	$ git commit -m "YOUR COMMIT"
	$ git push -u origin main	#github pages 默认分支是 ‘main’
	```

15. 由于 jekyll 安装后 会在 .gitignore 文件中添加 ignore 选项，所以每次重新 git clone 下来博客源代码后，要按照之前的步骤重新在本地安装 jekyll 和 bundle ，然后才能推送文章到 github 博客上面。



完成

参考网址：

[GitHub Pages 文档 - GitHub Docs](https://docs.github.com/cn/pages)

[Jekyll on Ubuntu  Jekyll  Simple, blog-aware, static sites (jekyllrb.com)](https://jekyllrb.com/docs/installation/ubuntu/)

[bundle update和bundle install的区别_暗夜之虎的专栏-CSDN博客_bundle update](https://blog.csdn.net/thinkhb/article/details/50190877)

