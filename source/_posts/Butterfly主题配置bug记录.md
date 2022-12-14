---
title: Butterfly主题配置bug记录（更新中）
tags:
  - null
  - null
categories:
  - 博客
description: 总能遇到千奇百怪的bug，算是一本“怪物图鉴”了吧
keywords: 'Butterfly, bug, hexo'
highlight_shrink: false
abbrlink: 7e01226e
date: 2022-09-01 17:53:00
updated: 2022-09-02 11:02:00
top_image:
cover:
---
# 前言
世界上除了学不完的知识，还有排不完的bug。。

本篇记录自己在使用butterfly主题时遇到的各种报错，每个都是独一无二且千奇百怪的

排除bug是一个非常折腾+耗时间的过程，或许本文会一直更新下去吧~（恼）

# 1. YAMLException: bad indentation of a mapping entry 
```报错示例
# 运行hexo g后出现如下报错
YAMLException: bad indentation of a mapping entry (7:20)

 4 |
 5 | menu:
 6 |   首页:/ || fas fa-home
 7 |   菜单 || fas fa-list:
------------------------^
 8 |     时间轴: /archives/ || fas fa-archive
 9 |     标签: /tags/ || fas fa-tags
    at generateError (D:\Blog\悠悠の哉\node_modules\js-yaml\lib\loader.js:183:10)
    at throwError (D:\Blog\悠悠の哉\node_modules\js-yaml\lib\loader.js:187:9)
    ...
```

```原因&解决方法
报错原因：一般是英文冒号后缺失空格
解决方法：通过报错找到相应位置，添加空格
```
> 这或许是目前遇到的最多的报错了吧。。

# 2. warning: adding embedded git repository: XXXX

```报错示例
# 运行git add .后出现如下报错
warning: adding embedded git repository:xxxxxxxxxx
hint: You've added another git repository inside your current repository.
hint: Clones of the outer repository will not contain the contents of
hint: the embedded repository and will not know how to obtain it.
hint: If you meant to add a submodule, use:
hint:
hint: git submodule add <url>xxxxxxxxxx
hint:
hint: If you added this path by mistake, you can remove it from the
hint: index with:
hint:
hint: git rm --cached   xxxx
hint:
hint: See "git help submodule" for more information.
```

```原因&解决方法
报错原因：没有添加相应的子模块，在.gitmodules中没有这个子模块的path和url信息。
        经常出现在配置完主题，却没有添加子模块的情况。
解决方法：以主题`butterfly`为例，运行如下指令：
        # 如果是文件
        git rm --cached themes/butterfly
        # 如果执行以上命令后若提示：【 error: 如下文件其暂存的内容和工作区及 HEAD 中的都不一样：】 ，`-f`强制删除
        git rm -f --cached themes/butterfly

		#=添加仓库索引=======================================
		# 创建子模块并添加butterfly的url地址
		# 格式为git submodule add <url> <path>
		git submodule add https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
		# 或者也可以使用butterfly的ssh地址
```

# 3. github报错Jekyll::Errors::MissingDependencyException和Gem::MissingSpecError

```报错示例
#配置完butterfly主题，在将本地博客文件push到github上后，显示如下错误：
/usr/local/bundle/gems/jekyll-3.9.2/lib/jekyll/theme.rb:84:in `rescue in gemspec': The butterfly theme could not be found. (Jekyll::Errors::MissingDependencyException)
	from /usr/local/bundle/gems/jekyll-3.9.2/lib/jekyll/theme.rb:81:in `gemspec'
	from /usr/local/bundle/gems/jekyll-3.9.2/lib/jekyll/theme.rb:19:in `root'
	from /usr/local/bundle/gems/jekyll-3.9.2/lib/jekyll/theme.rb:12:in `initialize'
    ......
/usr/local/lib/ruby/2.7.0/rubygems/dependency.rb:311:in `to_specs': Could not find 'butterfly' (>= 0) among 148 total gem(s) (Gem::MissingSpecError)
Checked in 'GEM_PATH=/github/home/.gem/ruby/2.7.0:/usr/local/lib/ruby/gems/2.7.0:/usr/local/bundle', execute `gem env` for more information
	from /usr/local/lib/ruby/2.7.0/rubygems/dependency.rb:323:in `to_spec'
	from /usr/local/lib/ruby/2.7.0/rubygems/specification.rb:986:in `find_by_name'
	from /usr/local/bundle/gems/jekyll-3.9.2/lib/jekyll/theme.rb:82:in `gemspec'
    ......
  Logging at level: debug
Configuration file: /github/workspace/./_config.yml
             Theme: butterfly
github-pages 227 | Error:  The butterfly theme could not be found.
```

```原因&解决方法
原因：尚未清楚出现这个报错的原因。
     由于这一步出现在github page build and deploy，因此初步判断是，github无法根据仓库内容构建和部署github page导致的正常报错，而我没有将网站部署在github page的想法，对于后续Netlify网站的部署也没有任何影响，因此目前对这个报错可以忽略不管。
```

# 4. YAMLException: duplicated mapping key

```报错示例
# 输入hexo g后，出现如下报错：
YAMLException: duplicated mapping key (16:1)

 13 | keywords: Butterfly, hexo
 14 | top_image:
 15 | cover:
 16 | highlight_shrink: false
------^
    at generateError (D:\Blog\悠悠の哉\node_modules\js-yaml\lib\loader.js:183:10)
......
```

```原因&解决方法
Front内容出现了重复，需要将重复内容删除
```

# 5. 文章目录无法跳转

点击文章页一级目录无反应，但二级目录可以正常跳转。打开浏览器右键菜单，点击“检查-控制台”查看`html`，发现点击目录一级标题后会出现如下报错：

```
Uncaught TypeError: Cannot read properties of null (reading 'offsetTop')
    at Object.getEleTop (utils.js:208:25)
    at HTMLDivElement.<anonymous> (main.js:369:30)
```


![image-20220921014231760](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220921014231760.png)

检查“页面元素”，发现目录一级标题的`'toc-link'`后没有正常生成`href`属性，因此无法实现跳转。

![image-20220921014914595](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220921014914595.png)

出现此问题的原因是`markdown-it`插件的 bug，butterfly主题的博客是由`markdown-it`渲染的，而这个插件并未渲染 heading 的 id 所以导致锚点失效，目录无法正常跳转。

解决方法是首先安装 markdown-it-named-headings 插件：

```
npm install markdown-it-named-headings --save
```

然后进入项目的根目录，修改根目录下 `node_modules\hexo-renderer-markdown-it\lib\renderer.js` 文件，在 `renderer.js` 中添加一行以使用此插件：

```
parser.use(require('markdown-it-named-headings'))
```

> 可能需要根据该文件中具体的代码格式进行适当修改

我的修改效果如下：

![修改js文件](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/%E4%BF%AE%E6%94%B9js%E6%96%87%E4%BB%B6.png)

记得保存修改，重新运行后即可正常实现目录一级标题的跳转。

> 详细问题描述及解决方案参考[Hexo 博客踩坑 | blog (convivae.top)](https://convivae.top/posts/hexo-bo-ke-cai-keng/)

