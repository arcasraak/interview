# HTML5 - 高级篇

## 标签语义化的理解？

- 去掉或者丢失样式的时候能够让页面呈现出清晰的结构

- 有利于 SEO：和搜索引擎建立良好沟通，有助于爬虫抓取更多的有效信息：爬虫依赖于标签来确定上下文和各个关键字的权重；

- 方便其他设备解析（如屏幕阅读器、盲人阅读器、移动设备）以意义的方式来渲染网页；

- 便于团队开发和维护，语义化更具可读性，遵循 W3C 标准的团队都遵循这个标准，可以减少差异化。

|    标签 | 介绍                                                                                                                                                                                                              |
| ------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
|  header | 代表“网页”或“section”的页眉,没有个数限制。                                                                                                                                                                        |
|  footer | 代表“网页”或“section”的页脚,没有个数限制。通常含有该节的一些基本信息，譬如：作者，相关文档链接，版权资料。                                                                                                        |
|  hgroup | 代表“网页”或“section”的标题和元数据组，该元素可以将 h1 到 h6 元素放在其内，譬如文章的主标题和副标题的组合                                                                                                         |
|     nav | 页面的导航链接区域。用于定义页面的主要导航部分。推荐单个使用。                                                                                                                                                    |
|   aside | 包含在 article 元素中作为主要内容的附属信息部分，其中的内容可以是与当前文章有关的相关资料、标签、名次解释等。（特殊的 section），或者作为侧边栏广告，其他日志链接或者其他分类导航                                 |
| section | 文档中的“节”或“段”，“段”可以是指一篇文章里按照主题的分段；“节”可以是指一个页面里的分组。article、nav、aside 可以理解为特殊的 section，所以如果可以用 article、nav、aside 就不要用 section，没实际意义的就用 div。 |
| article | 代表一个在文档，页面或者网站中自成一体的内容，其目的是为了让开发者独立开发或重用。                                                                                                                                |
| address | 作为联系信息出现，邮编地址、邮件地址等等,一般出现在 footer。                                                                                                                                                      |
|    time | 标记一篇                                                                                                                                                                                                          |

## html5 有哪些新特性、移除了那些元素？

**新特性：**

- HTML5 现在已经不是 SGML 的子集，主要是关于图像，位置，存储，多任务等功能的增加。

- 拖拽释放(Drag and drop) API

- 语义化标签（header,nav,footer,aside,article,section）

- 音频、视频 API(audio,video)

- 画布(Canvas) API

- 地理(Geolocation) API

- 本地离线存储 localStorage 长期存储数据，浏览器关闭后数据不丢失

- sessionStorage 的数据在页面会话结束时会被清除

- 表单控件，calendar、date、time、email、url、search

- 新的技术 webworker, websocket 等

**移除的元素：**

- 纯表现的元素：basefont，big，center, s，strike，tt，u；

- 对可用性产生负面影响的元素：frame，frameset，noframes；

## HTML5 的文件离线储存怎么使用，工作原理是什么？

在线情况下，浏览器发现 HTML 头部有 manifest 属性，它会请求 manifest 文件，如果是第一次访问，那么浏览器就会根据 manifest 文件的内容下载相应的资源，并进行离线存储。如果已经访问过并且资源已经离线存储了，那么浏览器就会使用离线的资源加载页面。然后浏览器会对比新的 manifest 文件与旧的 manifest 文件，如果文件没有发生改变，就不会做任何操作，如果文件改变了，那么就会重新下载文件中的资源，并且进行离线存储。例如，

在页面头部加入 manifest 属性

```html
<html manifest="cache.manifest"></html>
```

在 cache.manifest 文件中编写离线存储的资源

```bash
CACHE MANIFEST
#v0.11
CACHE:
js/app.js
css/style.css

NETWORK:
Resourse/logo.png

FALLBACK:
 //offline.html
```

1. CACHE：必选，表示需要离线存储的资源列表，由于包含 manifest 文件的页面将被自动离线存储，所以不需要把页面自身也列出来。
2. NETWORK：可选，可以使用通配符，表示在它下面列出来的资源只有在在线的情况下才能访问，他们不会被离线存储，所以在离线情况下无法使用这些资源。不过，如果在 CACHE 和 NETWORK 中有一个相同的资源，那么这个资源还是会被离线存储，也就是说 CACHE 的优先级更高。
3. FALLBACK：可选，表示如果访问第一个资源失败，那么就使用第二个资源来替换他，如/html/ /404.html 表示用 “404.html” 替代 /html/ 目录中的所有文件，/ /404.html 表示用 “404.html” 替代当前目录中的所有文件，\*.html /404.html 表示用 “404.html” 替代 所有 html 文件。

_注意 :manifest 文件最好不要设置缓存, manifest 文件和离线资源 要一起更新，如果某个资源由于某种原因下载失败，那么这次的所有更新就算是失败的，浏览器还是会使用原来的资源；在更新了资源之后，新的资源需要到下次再打开 app 才会生效，如果需要资源马上就能生效，那么可以使用 window.applicationCache.swapCache\(\)方法来使之生效，出现这种现象的原因是浏览器会先使用离线资源加载页面，然后再去检查 manifest 是否有更新，所以需要到下次打开页面才能生效。_

参考资料：https://developer.mozilla.org/zh-CN/docs/Web/Manifest


## 页面资源预加载(Link prefetch)

> 页面资源预加载(Link prefetch)是浏览器提供的一个技巧，目的是让浏览器在空闲时间下载或预读取一些文档资源，用户在将来将会访问这些资源。一个 Web 页面可以对浏览器设置一系列的预加载指示，当浏览器加载完当前页面后，它会在后台静悄悄的加载指定的文档，并把它们存储在缓存里。当用户访问到这些预加载的文档后，浏览器能快速的从缓存里提取给用户。

- ### 为什么需要预加载？

用户可能是第一次访问网站或者清空了浏览器缓存，总之当前页面没缓存

用户可能要访问的下一个页面的资源，预先加载，秒打开体验更好

- ### DNS prefetch 预解析

我们知道，当我们访问一个网站如 www.amazon.com 时，需要将这个域名先转化为对应的 IP 地址，这是一个非常耗时的过程。

DNS prefetch 分析这个页面需要的资源所在的域名，浏览器空闲时提前将这些域名转化为 IP 地址，真正请求资源时就避免了上述这个过程的时间。

```angular2html
<meta http-equiv='x-dns-prefetch-control' content='on'>
<link rel='dns-prefetch' href='http://g-ecx.images-amazon.com'>
<link rel='dns-prefetch' href='http://z-ecx.images-amazon.com'>
```

- ### Resource prefetch 资源预加载

在 Chrome 下，我们可以用 link 标签声明特定文件的预加载：

```angular2html
<link rel='subresource' href='critical.js'>
<link rel='subresource' href='main.css'>

<link rel='prefetch' href='secondary.js'>

<!--在 Firefox 中或用 meta 标签声明：-->
<meta http-equiv="Link" content="<critical.js>; rel=prefetch">
```

rel='subresource' 表示当前页面必须加载的资源，应该放到页面最顶端先加载，有最高的优先级。

rel='prefetch' 表示当 subresource 所有资源都加载完后，开始预加载这里指定的资源，有最低的优先级。

注意：只有可缓存的资源才进行预加载，否则浪费资源！

- ### Pre render 预渲染

预渲染提前加载好用户即将访问的下一个页面，慎用！

```angular2html
<link rel='prerender' href='http://www.pagetoprerender.com'>

<!--在 Firefox 中或用 rel='next' 来声明-->
<link rel="next" href="http://www.pagetoprerender.com">
```

## cookie & localStorage & sessionStorage

- 传递性：

  - cookie 为了标示用户身份而储存在用户本地终端（Client Side）上的数据（通常经过加密）。
  - cookie 数据始终在同源的 http 请求中携带（即使不需要），记会在浏览器和服务器间来回传递。
  - sessionStorage 和 localStorage 不会自动把数据发给服务器，仅在本地保存。

- 存储大小：

  - cookie 数据大小不能超过 4k。
  - sessionStorage 和 localStorage 虽然也有存储大小的限制，但比 cookie 大得多，可以达到 5M 或更大。

- 有期时间：

  - localStorage 存储持久数据，浏览器关闭后数据不丢失除非主动删除数据；
  - sessionStorage 数据在当前浏览器窗口关闭后自动删除。
  - cookie 设置的 cookie 过期时间之前一直有效，即使窗口或浏览器关闭