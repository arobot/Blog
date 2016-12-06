---
title: 引入Hexo，美化你的博客（二）配置Hexo
tags: [hexo,blog,博客]
categories: [Blog]
date: 2016-12-06 10:20:52
---

继上一篇文章之后，你的博客应该都已经能够正常的在本机运行了。但是这远远不能满足我们搭建博客的需求。不过老生常谈，大部分的配置都能够在官方找到，传送门[Hexo配置](https://hexo.io/zh-cn/docs/configuration.html)。不同的主题还有个性化的配置，详细的配置请参见你使用的主题的文档。

<!--more-->

Hexo的目录结构
---
使用
```vim
$ hexo init
```
命令之后，会初始化完成一个完整的博客文件夹，其中包含以下几个文件或文件夹：

>_config.yml	*Hexo的配置文件，大部分的参数在这里配置*
package.json	*应用程序的信息，小白不用动这个文件，官方提示可以自由移除*
scaffolds	*模板文件夹。新建文章时，采用的模板存放于此*
source		*用户存放资源的地方，所有用户生成文件或资源都用这里保存*
>>_drafts	*草稿文件夹，用户可以直接生成草稿到这个文件夹*
>>_posts	*可发布的文章文件夹，下面的文章会在`hexo d`时，发布出去*

>themes		*主题文件夹，Hexo会根据主题设置生成静态页面*


### _config.yml配置
>这一节的内容全部copy自[官方文档](https://hexo.io/zh-cn/docs/configuration.html)

#### 网站

|参数|描述|
|---|---|
|title|网站标题|
|subtitle|网站副标题|
|description|网站描述|
|author|您的名字|
|language|网站使用的语言|
|timezone|网站时区。Hexo默认使用您电脑的时区。[时区列表](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)。比如说：America/New_York,Japan,和UTC。|

#### 网址

|参数|描述|默认值|
|---|---|---|
|url|网址||
|root|网站根目录||
|permalink|文章的[永久链接](https://hexo.io/zh-cn/docs/permalinks.html)格式|:year/:month/:day/:title/|
|permalink_default|永久链接中各部分的默认值|　|

>### 网站存放在子目录
> 如果您的网站存放在子目录中，例如`http://yoursite.com/blog`，则请将您的`url`设为`http://yoursite.com/blog`并把`root`设为`/blog/`。

#### 目录

|参数|描述|默认值|
|---|---|---|
|source_dir|资源文件夹，这个文件夹用来存放内容。|source|
|public_dir|公共文件夹，这个文件夹用于存放生成的站点文件|public|
|tag_dir|标签文件夹|tags|
|archive_dir|归档文件夹|archives|
|category_dir|分类文件夹|categories|
|code_dir|Include code文件夹|downloads/code|
|i18n_dir|国际化(i18n)文件夹|:lang|
|skip_render|跳过指定文件的渲染，您可使用[glob 表达式](https://github.com/isaacs/node-glo://github.com/isaacs/node-glob)来匹配路径。|　|

#### 文章

|参数|描述|默认值|
|---|---|---|
|new_post_name|新文章的文件名称|:title.md|
|default|预设布局|post|
|auto_spacing|在中文和英文之间加入空格|false|
|titlecase|把标题转换为title case|false|
|external_link|在新标签中打开链接|true|
|filename_case|把新文件名称转换为（1）小写或（2）大写|0|
|render_drafts|显示草稿|false|
|post_asset_folder|启动[Asset文件夹](https://hexo.io/zh-cn/docs/asset-folders.html)|false|
|relative_link|吧链接改为与根目录的相对地址|false|
|future|显示未来的文章|true|
|highlight|代码块的设置|　|

#### 分类&标签

|参数|描述|默认值|
|---|---|---|
|default_category|默认分类|uncategorized|
|category_map|分类别名||
|tag_map|标签别名|　|

#### 日期/时间格式
Hexo使用[Moment.js](http://momentjs.com/)来解析和显示时间。

|参数|描述|默认值|
|---|---|---|
|date_format|日期格式|YYYY-MM-DD|
|time_format|时间格式|H:mm:ss|

#### 分页

|参数|描述|默认值|
|---|---|---|
|per_page|每页显示的文章量（0=关闭分页功能）|10|
|pagination_dir|分页目录|page|

#### 扩展

|参数|描述|
|---|---|
|theme|当前主题名称。值为false时禁用主题|
|deploy|部署部分的设置|

### 主题设置

我的主题使用的[@Litten](https://github.com/litten)制作的主题[Yilia](https://github.com/litten/hexo-theme-yilia)。
关于主题的设置，可以参考该主题的配置。

关于主题的设置，有些坑在这里说明一下。
#### 相对路径
Hexo对于路径的生成策略都是相对路径，也就是说，只要你不指定为绝对路径，设置的链接都将是`root`+`url`的形式。
例如：
```url
github: "github.com/arobot"
```
点击图标会链接到`http://yoursite/github.com/arobot`，显然这样是错误的。正确的设置方式是绝对路径：
```url
github: "https://github.com/arobot"
```
同理，其他项的设置也是如此，注意区分你使用的链接是否需要指定绝对路径。

### 个人域名

#### 获取ip
如果你手上恰好有一个为自己购买的域名，那正适合你刚刚搭建好的博客。
我的域名是在[aliyun.com](https://aliyun.com)上购买的，在我搭建这个博客时候就想，我应该有一个自己的域名，换了很多组合选了一个还算满意的。当你看到这里的时候，如果也有冲动，那赶紧去看看吧，说不定过两天就没有了呢

首先，需要知道你的博客所在的服务器地址
```
$ ping arobot.github.io
```
得到我的博客在`151.101.100.133`上面，记下这个ip，会在后面用到。

#### 设置域名解析
进入阿里云的云解析，可以为你购买的域名添加解析。
![域名解析](/images/hexo_2/dns_jiexi.jpg)

|选项|描述|
|---|---|
|记录类型|选择`A`|
|主机记录|配置两项。一项填写`@`;另一项填写`www`|
其他的选择默认就行，配置好的结果如下
![配置结果](/images/hexo_2/dns_result.jpg)

完成了域名的解析工作之后，在博客的`source`文件下新建文件名为`CNAME`的文件，将你的域名不加协议填写进去。例如`wluo.me`。

个性化域名的配置就完成了，将博客部署上去就能够通过自己的域名链接过来。

### 部署博客
静态网页可以部署在多种服务器上，Hexo官方提供了多种[部署](https://hexo.io/zh-cn/docs/deployment.html)方式，详细的部署方式参见官网。
我是部署在`Github pages`上。
在`_config.yml`文件中配置`deploy`
```
deploy:
   type: git
   repo: https://github.com/arobot/arobot.github.io.git
   branch: master
```
