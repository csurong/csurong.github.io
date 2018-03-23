---
layout: post
title:  "urllib(urllib2)包"
date: 2017-09-13 22:47:01 +0800
categories: python
tags:  python 爬虫 urllib
author: csurong
---

* content
{:toc}

urllib相比requests要复杂不少，但对理解爬虫的爬取过程还是很有帮助的，还是有必要学习一下




## 结构

python3已经将urllib1和urllib2模块合并成一个包了。urllib现在主要可以分为四个模块，文档里有很详尽的介绍

> `urllib.request` for opening and reading URLs
> `urllib.error` containing the exceptions raised by urllib.request
> `urllib.parse` for parsing URLs
> `urllib.robotparser` for parsing robots.txt files

reqeust是请求用的，爬虫中常用的 get、post都是这个模块的方法。parse的中文翻译过来是语法描述/分析的意思，这个方法还是挺重要的，下面用一个例子来说明

我在百度中搜索关键字“天气”，浏览器中看见的请求页面的地址是`https://www.baidu.com/s?wd=天气`，然而用 fiddler 抓取请求地址却发现，真正请求地址是:
![UrlEncode编码.png](https://upload-images.jianshu.io/upload_images/6434703-56ee5da203cb2237.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这个地址才是浏览器真正发出去的请求的地址，一般HTTP请求提交数据，需要编码成 URL编码格式，然后做为url的一部分，或者作为参数传到Request对象中。这便是parse的作用。errors是发生错误或异常时用的，这个没什么好说的，使用的时候注意错误类型对象的继承关系就行了，文档中粗略给了两个错误类型方法 `URLError`和`HTTPError`，不过还可以细分，这个自己去看文档就好了。robotparser基本用不到的，这种东西对类似baidu、Google这种通用爬虫有意义，我们自己写的爬虫不用管这个，感兴趣的去[文档](https://docs.python.org/3/library/urllib.robotparser.html#module-urllib.robotparser)看吧

## 语法

get请求的基本使用，还是以上文的百度搜索为例：
```python
url = 'http://www.baidu.com/s'
headers={ "User-Agent": "Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/51.0.2704.103 Safari/537.36"}
request_keyword = {
	'wd': '天气'
}
request_keyword = urllib.parse.urlencode(reqeust_keyword) # 汉字编码
full_url = url + '?' + request # 完整的请求地址
request = urllib.request.Request(url, headers) 
response = urllib.request.urlopen(request)
print(response.read())
```

`urllib.request.Request`构造请求对象，get方式的请求必须有两个参数，url（请求的地址）、headers（模拟浏览器的请求头），post方法还多了一个data参数，data参数指的是要 post 过去的数据。get请求的url会附带参数，因此编码这一步不可或缺。post过去的数据，得看具体情况，我之前做有道词典爬虫的时候，就遇到过一次。百度翻译的请求方式是 get，比较好弄，有道的是post，而且post过去的数据需要编码，我也是检查了半天，最后用抓包才看出来的，不得不说，有道的反爬做的还是挺严格的，所以基本用法不难，难的是分析的过程和查找问题的思路。

说到抓包，这里再闲扯一句，网页中ajax数据的抓取一定要抓包之后再搞，找出它的请求地址和数据格式

## SSL证书验证错误

```python
# 1. 导入Python SSL处理模块
import ssl

# 2. 表示忽略未经核实的SSL证书认证
context = ssl._create_unverified_context()

# 3. 在urlopen()方法里 指明添加 context 参数
response = urllib2.urlopen(request, context = context)
```

## 代理访问

urllib中通过ProxyHandler来设置使用代理服务器
```
import urllib.request

# 构建了两个代理Handler，一个有代理IP，一个没有代理IP
httpproxy_handler = urllib.request.ProxyHandler({"http" : "124.xx.xx.xx:80"})
nullproxy_handler = urllib.request.ProxyHandler({})

proxySwitch = True #定义一个代理开关,没有当然也没关系

# 通过 urllib.request.build_opener()方法使用这些代理Handler对象，创建自定义opener对象
# 根据代理开关是否打开，使用不同的代理模式
if proxySwitch:  
    opener = urllib.request.build_opener(httpproxy_handler)
else:
    opener = urllib.request.build_opener(nullproxy_handler)

request = urllib.request.Request("http://www.baidu.com/")

# 1. 如果这么写，只有使用opener.open()方法发送请求才使用自定义的代理，而urlopen()则不使用自定义代理。
response = opener.open(request)

# 2. 如果这么写，就是将opener应用到全局，之后所有的，不管是opener.open()还是urlopen() 发送请求，都将使用自定义代理。
# urllib.request.install_opener(opener)
# response = urlopen(request)

print(response.read().decode('utf-8'))