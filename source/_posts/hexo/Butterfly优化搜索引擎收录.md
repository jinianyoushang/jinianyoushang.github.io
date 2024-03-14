---
title: Butterfly优化搜索引擎收录
typora-root-url: Butterfly优化搜索引擎收录
date: '2024-03-08 13:24'
tags:
  - 搜索引擎收录
  - hexo
  - Butterfly
categories:
  - hexo
abbrlink: e302e79f
---

我们必须把我们的网站推送到搜索引擎那， 否则别人除了输入我们的域名或者搜索文章，是没法发现我们的博文。

## 1. 查看是否被收录

使用想要查找的搜索引擎，输入：

```
site:你的网站
比如我的：site:qmike.top
```

## 2. 永久化 URL 网址链接

> 我们可以发现 hexo 默认生成的文章地址路径是 【网站名称／年／月／日／文章名称】。
>
> 这种链接对搜索爬虫是很不友好的，第一它的 url 结构超过了三层，太深了。

安装 `abbrlink` 插件：

```BASH
npm install hexo-abbrlink --save
```

关于此插件的详细信息参见它的[官方文档](https://github.com/rozbo/hexo-abbrlink)。作用是将文章的链接转换成数字后字母，即将博客网站的网页转成`.html` 永久链接的格式，有利于搜索引擎的收录。

修改 hexo 根目录下 `config.yml` 中的 `permalink` 的值：

```YML
# URL
## Set your site url here. For example, if you use GitHub Page, set url as 'https://username.github.io/project'
url: https://qmike.top
permalink: posts/:abbrlink.html
```

在 `config.yml` 最底下添加 `abbrlink config`

```YML
# abbrlink config
abbrlink:
  alg: crc32      # support crc16(default) and crc32
  rep: hex        # support dec(default) and hex
# 不用添加其它代码
```

配置完成后，网站的链接应该类似这样：

```PLAINTEXT
https://qmike.top/posts/77940e6f.html        # 有.html后缀 
```

## 3. 站点地图

站点地图即 [sitemap](https://baike.baidu.com/item/sitemap/6241567?fr=aladdin)， 是一个页面，上面放置了网站上需要搜索引擎抓取的所有页面的链接。站点地图可以告诉搜索引擎网站上有哪些可供抓取的网页，以便搜索引擎可以更加智能地抓取网站。所以我们首先需要生成一个站点地图。

安装百度和 Google 的站点地图生成插件：

```BASH
npm install hexo-generator-baidu-sitemap --save
npm install hexo-generator-sitemap --save
```

然后来到 hexo 根目录配置文件 `config.yml`，在下面添加：

```YML
# 站点地图
sitemap:
  path: sitemap.xml
baidusitemap:
  path: baidusitemap.xml
```

然后重新推送到服务器，访问如下 URL:

```
https://你的域名/sitemap.xml
https://你的域名/baidusitemap.xml
```

看看网页中有没有出现代码。有的话就成功。

> 注意代码中的域名是否和自己网站的一样，使用 `github page` 部署的网站，可能会出现域名不同的情况，需要之前完成 token 令牌密钥的验证，[视频](https://www.bilibili.com/video/BV1W34y1o7yK/?spm_id_from=333.788&vd_source=8bba695aa4490252230ffd2e2cc0609b)中有介绍。

给你的 hexo 网站添加蜘蛛协议 robots.txt, 把 robots.txt 放在你的 hexo 站点的 source 文件下即可。

```
# hexo robots.txt
User-agent: *
Allow: /

Sitemap: https://{域名}/sitemap.xml
Sitemap: https://{域名}/baidusitemap.xml
```

## 4. 百度收录

### 提交网站

通过百度站长平台进行链接提交，增加网站的索引量。先去注册并登录：[百度站长平台](https://ziyuan.baidu.com/?castk=LTE=)

[![image-20220904180119988](image-20220904180119988-17098752495171.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220904180119988.png)

需要验证网站，我选择的是 https://，这根据你前面是否添加 SSL 证书来选择。并且我使用的是不带 www 的，看个人。然后到第三步，我使用的 HTML 标签验证。

[![img](image-20220904181206160-17098752495172.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220904181206160.png)

把 `content` 中的字符串复制到主题配置文件`_config.butterfly.yml` 中的 `baidu_site_verification` 。

```YML
# Baidu Webmaster tools verification.
# See: https://ziyuan.baidu.com/site
site_verification:
  # - name: google-site-verification
  #   content: xxxxxx
  - name: baidu-site-verification
    content:  # 在这里填上面的字符串
```

> 需要将网站部署完后，再去百度站长平台完成 HTML 标签验证

### 提交链接

百度站长平台的链接提交方式分为自动提交和手动提交两种，此处只讲自动提交，手动提交按照要求操作即可。

**主动推送**最为快速的提交方式，是被百度收录最快的推送方式。主动推送可以通过安装插件实现：

```BASH
npm install hexo-baidu-url-submit --save
```

然后在 hexo 根目录配置文件`_config.yml` 中，添加：

```YML
# 主动推送百度，被百度收录
baidu_url_submit:
  count: 10 # 提交最新的10个链接
  host: # 百度站长平台中注册的域名
  token: # 秘钥，百度站长平台 > 普通收录 > 推送接口 > 接口调用地址中token字段
  path: baidu_urls.txt # 文本文档的地址， 新链接会保存在此文本文档里，不用改
```

- host 为自己网站的域名，例如我的为 `https://qmike.top`

- token 需要打开 “普通收录–> 推送接口” 进行查看

  ![image-20220904212055771](image-20220904212055771-17098752495173.png)

其次，记得查看 hexo 根目录中`_config.yml` 文件中` url` 的值， 必须包含是百度站长平台注册的域名。

最后，在`_config.yml` 文件中的 `deploy` 加入新的 `type`:

```YML
deploy:
  - type: git
    repository: https://github.com/fenshen000/fenshen000.github.io.git
    branch: main
  - type: baidu_url_submitter
```

> 这里是新建一个 type，一定要注意这段代码里面各行的缩进值

其主动推送的实现原理如下：

- 新链接的产生， `hexo generate` 会产生一个文本文件，里面包含最新的链接

- 新链接的提交， `hexo deploy` 会从上述文件中读取链接，提交至百度搜索引擎

  > 不知道对于部署在 Netlify 上的网站有没有用，再查查资料

若要实现手动提交，则把下面的代码粘贴到百度站长平台的 “手动收录” 地址窗口即可：

```
https://你的域名/sitemap.xml
https://你的域名/baidusitemap.xml
```

[![image-20220904213924274](image-20220904213924274-17098752495174.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220904213924274.png)

后续慢慢等收录吧，百度收录比较慢。

## 5. 谷歌收录

提交谷歌搜索引擎比较简单，在提交之前，我们依然可以使用 `site:域名` 查看网站是否被收录。进入 [Google 搜索中心](https://developers.google.com/search#?modal_active=none)，登录你的谷歌账号。然后找到[注册 Search Console](https://search.google.com/search-console/welcome)(在 “使用入门–>SEO 新手指南” 中可以找到入口)，就直接输入你要收录的网站域名就行。

详细操作参考谷歌的[官方指南](https://support.google.com/webmasters/answer/9008080#html_verification&zippy=%2C域名提供商%2Chtml-标记%2Chtml-文件上传)

[![image-20220904220444787](image-20220904220444787-17098752495175.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220904220444787.png)

选择第一个或者第二个都可以的，我这里两个都选择了。

“网址前缀” 验证很简单，输入网址 `https://qmike.top` 即可直接验证。“网域” 验证较为复杂，点击 “继续” 后，操作如下：

> 可以添加所有的网址变体，包括 https，http，www 和非 www 变体

[![image-20220904221520736](image-20220904221520736-17098752495176.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/image-20220904221520736.png)

打开你的域名提供商网站，在里面添加 “解析设置”。以阿里云为例：

- 打开域名的 “解析设置”，点击 “添加记录”

  ![image-20220904224419062](image-20220904224419062-17098752495177.png)

- “记录类型” 选择 “TXT”，“主机记录” 选择 “@”，记录值写入上面复制的 TXT 记录值

  ![image-20220904225057831](image-20220904225057831-17098752495178.png)

- 重新部署后返回谷歌页面进行验证，可能需要等待一段时间。

[![20200403223509](20200403223509-17098752495179.png)](https://mikepicture.oss-cn-chengdu.aliyuncs.com/picture/20200403223509.png)

两种方式，你可以下载个 HTML 文件然后放在站点目录下的 `source` 中，然后推送到服务器。或者把 `content` 中的字符串复制到主题配置文件`_config.butterfly.yml` 对应内容中：

```YML
site_verification:
  - name: google-site-verification
    content: # 在这里填上面的字符串
  - name: baidu-site-verification
    content: XXXXX
```

> 部署网站到 Netlify，一天时间便会自动收录

## 6. 必应收录

必应收录也是很简单，点击[必应站长](https://www.bing.com/webmasters/about)。先注册登录，必应收录有两种方式，一种使用刚刚谷歌导入过去，第二种是就是自己添加 URL

## 7. 其它收录

其他搜索引擎的收录都很类似，就不一一赘述了。

## 8. 添加 nofollow 标签

给非友情链接的出站链接添加「nofollow」标签，nofollow 标签是由谷歌领头创新的一个「反垃圾链接」的标签，并被百度、yahoo 等各大搜索引擎广泛支持，引用 nofollow 标签的目的是：用于指示搜索引擎不要追踪（即抓取）网页上的带有 nofollow 属性的任何出站链接，以减少垃圾链接的分散网站权重。

```BASH
npm install hexo-filter-nofollow --save
```

再在 hexo 根目录的`_config.yml` 中添加配置，将 `nofollow` 设置为 `true`：

```YML
nofollow:
  enable: true
  field: site
  exclude: ''
```

这样，例外的链接将不会被加上 `nofollow` 属性。

## 参考

[Hexo 框架 (六)：SEO 优化及站点被搜索引擎收录设置 | 你真是一个美好的人类 (juanertu.com)](https://blog.juanertu.com/archives/9013c8d8.html)

[Butterfly 进阶篇（一） - SEO 优化搜索引擎收录 | 悠悠の哉 (qmike.top)](https://qmike.top/posts/2a1b5a62)