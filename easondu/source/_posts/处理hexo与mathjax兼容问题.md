---
title: 处理hexo与mathjax兼容问题
date: 2017-07-14 11:25:20
tags: [Marddown,Mathjax,Hexo]
categories: 技术
---
写了一篇文章，发现Latex公式的无法渲染，经过一番推敲和实践，最终解决了这个问题，在这篇博文中简单记录一下，方便其他遇到同样问题的朋友们也能顺利解决。不想看解决思路的朋友也可以跳到文章末尾直接看解决方案 * [Jump to the end to see sulution](#小结) 。
 <!-- more -->
# 问题重现

先重现一下这个问题。在写文章的时候，我码了一个相当简单的公式：

```
$$R_{m \times n} = U_{m \times m} S_{m \times n} V_{n \times n}'$$
```
渲染出来却变成了（红框内变为了斜体）：
![enter image description here](http://oe0e8k1nf.bkt.clouddn.com/Before_Change_Markdown_Renderer1.png)



# 问题思考

琢磨了一下，MathJax支持是开了的，这么简单的公式都渲染不出，肯定不是MathJax要背的锅。简单地测试了一下，发现：

$$R_{m \times n}$$
这个公式是能work的，那到底是什么问题呢？回到前面的公式，细心观察一下，不难发现渲染之后下划线 `_` 被吞掉了，而两个下划线 `_` 之间的文本变为了斜体，这就很有意思了。

浏览一下网页源码，可以看到对应这一条公式的代码是：

![enter image description here](http://oe0e8k1nf.bkt.clouddn.com/Before_Change_Markdown_Renderer2.png)


原来下划线被渲染成了 `<em>`，在HTML里这个tag是用于将文本准换为斜体进行强调的。为什么会产生这样的错误呢？其实，在Markdown语法中 两个下划线之间的文本会被转换为斜体，所以这个错误是由Markdown渲染器引起的。Markdown本身没有支持Latex，在渲染时正则匹配到两条下划线就会把下划线替换成了 `<em>`，于是到了MathJax渲染公式时就彻底懵了。

# 解决过程

意识到这个问题的本质后，带着疑问，我先来到Next主题的[Github主页](https://github.com/iissnan/hexo-theme-next/)，在[issue](https://github.com/iissnan/hexo-theme-next/issues?utf8=%E2%9C%93&q=mathjax)里面搜索MathJax。很可惜，没有找到直接的答案，但是思路清晰了一些。Next不背这个锅，我们要做的是更换Hexo使用的Markdown渲染器。

接下来我又到Hexo的[Github主页](https://github.com/hexojs/hexo/)搜索相关的解决方案，在[issue #524](https://github.com/hexojs/hexo/issues/524) 中，有人提到了可以使用 **rawblock** 来解决，可是每次要写公式都得在公式前后加上 rawblock 来声明实在太烦了，对于公式大户来说简直要崩溃（想想之后要写机器学习的相关文章，一大堆公式证明，已经想手动再见了）。

另一个提出的解决方案是更换**pandoc渲染器**，pandoc大法固然好，但要下载安装完pandoc然后再装hexo插件实在是太重量级了.. 我只是想轻松愉悦地写写博客呀。当然，感兴趣的朋友也不妨去[hexo-renderer-pandoc主页](https://github.com/wzpan/hexo-renderer-pandoc)看看。

有没有更好的方法呢？我在segmentfault查到另一位朋友写的解决方案，可以使用 hexo-renderer-markdown-it 进行渲染，似乎是个不错的思路，在hexo-renderer-markdown-it的文档中可以看到操作相当简单：
```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-markdown-it --save
```
两行命令即可完成，先卸载Hexo自带的Markdown解析器 hexo-renderer-marked 再安装 **hexo-renderer-markdown-it** 就可以了。安装完以后，先 hexo clean && hexo g 重新生成静态网页，然后 hexo s 查看，这回公式能正常显示了：


但是这是我又发现一个新的问题，使用 **hexo-renderer-markdown-it** 渲染之后，我原本为Markdown编写的TOC里的链接都失效了，而侧边栏的快速导航链接也都失效了。这怎么可以呢？文章写得短还好，鼠标滚轮滚一滚就好了。要是写得长那岂不就得浪费很多时间才能定位到自己想看的地方？这可不行！！！

继续查找更优的解决方案，Hexo有没有更好的Markdown渲染插件呢？有的，在**[Hexo主站的插件页](https://hexo.io/plugins/)**搜索关键字 Markdown，发现了 hexo-renderer-kramed 这个插件，打开它的[Github主页](https://github.com/sun11/hexo-renderer-kramed)，描述已经说得很清楚，作者fork了 hexo-renderer-marked 项目，并且只针对MathJax支持进行了改进，这正是我们需要的！！卸载 hexo-renderer-marked（注意，如果你使用了其他的渲染插件，请卸载对应的插件），然后安装 hexo-renderer-kramed：
```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```
这下，不仅能正常使用TOC，也能完美地支持MathJax渲染了。至此，问题得到了解决。

P.S. 其实，这个问题就是因为Markdown渲染和MathJax渲染冲突造成的，除了换别的渲染器，直接修改渲染用的正则表达式也是一种解决思路，但是这个思路有一定风险，如果引起了别的bug而没有及时发现，自己又没有做好备份和记录，就需要浪费很多额外的时间来定位问题。

# 小结

简单来说，要让你的Hexo支持MathJax渲染公式，你只需要使用两条命令：
To fully support MathJax in your Hexo blog, you can simply use the following commands:
```
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
```
第一条命令用于卸载 hexo-renderer-marked（注意，如果你使用了其他的渲染插件，请卸载对应的插件），它是hexo自带的Markdown渲染引擎。
The first command uninstall Hexo’s default Markdown renderer.

第二条命令用于安装 hexo-renderer-kramed 插件，这个渲染插件针对MathJax支持进行了改进。安装完成后，重新生成博客就会惊喜地发现你的公式已经能够正常显示了。
The second command install new Markdown renderer which can support MathJax fully. After installation, you should regenerate your blog to see the changes.

感想

作为一名编程人员，不屈服于bug，不妥协于简陋的solution是很重要的。除此之外，也要善于定位问题和解决问题，能够弄清楚出问题的地方，并且利用丰富的网络资源来解决问题。

最后，感谢开源和分享。

最后的最后，再码一条写在SVM笔记里的长公式检验一下吧：

$$minw,b12∥w∥2s.t.yi(wTϕ(x)+b)≥1,i=1,2,...,m(9)$$
$$minw,b12‖w‖2s.t.yi(wTϕ(x)+b)≥1,i=1,2,...,m(9)$$
完美~
----EOF----
