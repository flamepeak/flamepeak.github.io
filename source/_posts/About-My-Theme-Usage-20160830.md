---
title: 关于我的主题的一些设置
date: 2016-08-30 23:18:07
tags: [Theme, Hexo]
mathjax: true
categories: [Web前端]
---

## Introduce
使用ejs模板
字号单位是em，是一个相对长度单位，最初是指字母M的宽度，故名em。现指的是字符宽度的倍数，用法类似百分比，如：0.8em, 1.2em,2em等。通常1em=16px。

另外：
pt (point，磅)：是一个物理长度单位，指的是72分之一英寸。
px (pixel，像素)：是一个虚拟长度单位，是计算机系统的数字化图像长度单位，如果px要换算成物理长度，需要指定精度DPI(Dots Per Inch，每英寸像素数)，在扫描打印时一般都有DPI可选。Windows系统默认是96dpi，Apple系统默认是72dpi。


## Markdown语法
### 字体、字号与颜色
```html
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=#0099ff size=7 face="黑体">color=#0099ff size=7 face="黑体"</font>
<font color=#00ffff size=3>color=#00ffff</font>
<font color=Crimson size=1>color=Crimson</font>
```
Size：规定文本的尺寸大小。可能的值：从 1 到 7 的数字。浏览器默认值是 3。

实现效果：
<font face="黑体">我是黑体字</font>
<font face="微软雅黑">我是微软雅黑</font>
<font face="STCAIYUN">我是华文彩云</font>
<font color=#0099ff size=7 face="黑体">color=#0099ff size=7 face="黑体"</font>
<font color=#00ffff size=3>color=#00ffff size=3</font>
<font color=Crimson size=1>color=Crimson size=1</font>

备注：[颜色名列表与图释](http://blog.csdn.net/testcs_dn/article/details/45719357)


## Hexo离线安装Mathjax
这部分参考：[在Hexo中离线安装数据工具包-Mathjax](http://kubicode.me/2016/01/27/Hexo/Offline-Install-Mathjax-In-Hexo-Jacman/)

毕竟我是数学出身，Blog如果不支持公式，那岂不是笑话。

在`casper/post`目录下新建文件`mathjax.ejs`，并写入内容：
```
<!-- mathjax config similar to math.stackexchange -->
<% if (theme.mathjax || page.mathjax){ %>
<script type="text/x-mathjax-config">
  MathJax.Hub.Config({
    tex2jax: {
      inlineMath: [ ['$','$'], ["\\(","\\)"] ],
      processEscapes: true
    }
  });
</script>

<script type="text/x-mathjax-config">
    MathJax.Hub.Config({
      tex2jax: {
        skipTags: ['script', 'noscript', 'style', 'textarea', 'pre', 'code']
      }
    });
</script>

<script type="text/x-mathjax-config">
    MathJax.Hub.Queue(function() {
        var all = MathJax.Hub.getAllJax(), i;
        for(i=0; i < all.length; i += 1) {
            all[i].SourceElement().parentNode.className += ' has-jax';
        }
    });
</script>

<script type="text/javascript" src="/js/mathjax27/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>
<% } %>

```

然后在casper/post.ejs中添加：
```
<%- partial('post/mathjax') %>
```
原来引用官网的js：
`<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML">`
到官网http://docs.mathjax.org/en/latest/installation.html下载Mathjax离线包，离线包下载之后解压里面的`unpacked目录`，里面有整理好的直接引用的资源。

在`theme/ghost-casper/source/js`新建一个`mathjax27`目录，并将unpacked下的文件都复制到这里。最后修改`mathjax.ejs`中的引用：
`script type="text/javascript" src="/js/mathjax27/MathJax.js?config=TeX-AMS-MML_HTMLorMML">
</script>`

**问题**
但是，最后出现一个问题，在网页加载成功之后，公式正常显示，但是过一段时间之后显示`Math Processing Error`:
```
Error: Cannot read property 'length' of undefined
Debugging tips: use 'mathjax27/MathJax.js', inspect 'MathJax.Hub.lastError' in the browser console
```
不知道哪个地方出问题，水平菜。高手指点一下。

#### 使用Staticfile CDN加速Latex公式渲染
参考：[使用Staticfile CDN加速Latex公式渲染](https://metaquant.org/shi-yong-staticfile-cdnjia-su-latexgong-shi-xuan-ran.html)

多亏了Mathjax与Markdown,我们现在可以在markdwon文件中标记出latex代码，然后mathjax脚本可以正确的渲染这些代码，从而显示出清晰美观的公式。这比传统的插入公式图片等方式要更加便利，显示效果也更加优秀。

引入Mathjax脚本最简单的方式是直接使用Mathjax提供的CDN,可以用以下脚本实现：
`<script src='https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML'></script>`

可惜的是，由于Mathjax的CDN位于国外，其访问速度堪忧，而且由于需要下载的文件较多较大，公式的渲染过程非常缓慢，偶尔还会出现渲染失败的情况。幸运的是，由七牛云与CDN联合提供的[Staticfile CDN](https://staticfile.org/)网站提供了Mathjax脚本的国内版本，经过测试发现，其下载与渲染速度远快于Mathjax原生的CDN。

Staticfile CDN使用方式非常简单，在搜索框中输入`Mathjax`,就会出现Mathjax相应的静态文件，复制其中js文件的地址，并替换Mathjax原生CDN的地址，结果如下：
`<script src='https://cdn.staticfile.org/MathJax/MathJax-2.6-latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML'></script>`

插入公式示例：
首先，插入一个行内公式，需要使用`$...$`来实现：$x^2+y^2=z^2$
插入单独一行的公式，需要使用`$$...$$`的方式来实现，如：

$$
\theta_i = \theta_i - \alpha\frac\partial{\partial\theta_i}J(\theta)
$$


此外，也可以使用`\begin{equation}...\end{equation}`的方式插入独行公式，右边会显示公式编号，如下：
```
\begin{equation}J(u(t)) = \int_0^\infty  {{e^{ - \delta t}}} V(t)u(t)dt\end{equation}
```

**这种方法不行，暂时未找到解决办法**

另外，为了加快网页渲染的速度，在`_config.yml`中将`mathjax`设置为`false`,在含有公式的文章里开头设置`mathjax: true`。
