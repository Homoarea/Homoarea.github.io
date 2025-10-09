---
title: beautifulsoup爬虫入门
published: 2025-09-19
description: "本文使用bs4进行网页简单爬取"
image: ""
tags: [python, 爬虫]
category: "教程"
draft: false
lang: ""
---

## 流程

使用 python requests 发起请求,将响应内容使用 beautifulsoup 进行解析.
相当于我们请求了一个文本文件,使用 beautifulsoup 查看文本的内容.

## 安装 pypi 包

```bash
pip install pip install beautifulsoup4 lxml # 使用lxml解析
```

## beautifulsoup 快速开始

参考[beautifulsoup 中文文档](https://www.crummy.com/software/BeautifulSoup/bs4/doc.zh/)

我们将使用以下:
|方法/属性|描述|示例|
|-|-|-|
|`BeautifulSoup()`|用于解析 HTML 或 XML 文档并返回一个 BeautifulSoup 对象(默认使用 html.parser 解析)|soup = BeautifulSoup(html_doc, 'html.parser')|
|`.prettify()`|格式化文档内容,返回结构化字符串|print(soup.prettify())|
|`.find()`|查找第一个匹配的标签|tag=soup.find('a')|
|`.find_all()`|查找所有匹配的标签，返回一个列表。|tags=soup.find_all('a')|
|`.get()`/`[]`|获取指定属性的值。|href = tag.get('href')/href=tag['href']|
|`.get_text()`/`.text`|提取标签内的文本内容，忽略所有 HTML 标签。|text = tag.get_text()/text = tag.text|
|`.contents`|以列表形式返回标签的所有子元素|children = tag.contents|

## 试一试

```python
from bs4 import BeautifulSoup
import requests

# 目标网址
url = "https://baidu.com"
# 发送HTTP请求获取网页内容
res = requests.get(url)
# 设置字符编码
res.encoding = "utf-8"
# 使用BeautifulSoup解析网页内容
soup = BeautifulSoup(res.text, "lxml")
# 打印标题
print(soup.title)
```

执行结果:

```bash
<title>百度一下，你就知道</title>
```

## 爬取元素

我们查看一下解析的内容

```python
print(soup.prettify()) # 美化打印
```

可以发现输出有以下标签元素

```bash
<input autofocus="" class="bg s_btn" id="su" type="submit" value="百度一下"/>
```

接下来我们编写脚本获取其中的文本内容吧!(value 属性)

```diff lang="python"
from bs4 import BeautifulSoup
import requests
# 目标网址
url = "https://baidu.com"
# 发送HTTP请求获取网页内容
res = requests.get(url)
# 设置字符编码
res.encoding = "utf-8"
# 使用BeautifulSoup解析网页内容
soup = BeautifulSoup(res.text, "lxml")

+value=soup.find('input',id="su").get('value')
+print(value)
```

`find()`,`find_all()`方法可以传递`class_`,`id`等参数作为筛选.每个 HTML 页面中,id 和元素是一对一关系,不可重复;class 和元素是多对多,一个元素可以有多个 class ,一个 class 可以被多个元素使用.

我们使用根据 id 查到元素再调用`get()`方法获取其中 value 属性,输出如下:

```bash
百度一下
```

## 练一练

现在你已经入门了,找一个静态网页练习一下吧.(如果网站有反爬虫,还请换一个,不要打消积极性 😇)
可以在浏览器打开开发者模式(快捷键`F12`)使用查看器和查看源代码.

我喜欢看动画片,以爬取动画片时间表为例(正则表达式实际强大)

```python title="bs4_new_learn.py"
from bs4 import BeautifulSoup
import requests
import re
import os
# 美化打印
from pprint import pprint

# <br>标签直接转为纯文本时的值
HTML_TAR_BR='\n             \n             '
# 函数:获取去除首尾空格的字符串
get_text_strip = lambda x: x.text.strip()
# 函数:按<br>标签分割
# 1.标签子元素文本列表(<br/>转为空串,长度为0)
# 2.按字符串长度过滤(过滤掉<br/>)
# 3.去除首尾空格
strip_html_br = lambda x: list(
    map(lambda z: z.strip(), filter(len, map(lambda y: y.text, x.contents)))
)
# 目标地址
url = "https://yuc.wiki/202507/"
# 如果不存在本地文件,则发请求保存到本地
if not os.path.exists("bs4_new_learn.html"):
    res = requests.get(url)
    res.encoding = "utf-8"
    soup = BeautifulSoup(res.text, "lxml")
    open("bs4_new_learn.html", "w", encoding="utf-8").write(soup.prettify())

# 解析本地文件
soup = BeautifulSoup(open("bs4_new_learn.html", encoding="utf-8"), "lxml")
# 中文标题
cn_titles=list(map(get_text_strip,soup.find_all('p',class_='title_cn_r')))
# 日文标题
jp_titles=list(map(get_text_strip,soup.find_all('p',class_='title_jp_r')))
# 动画片类型,使用正则表达式匹配
type_animes=list(map(get_text_strip,soup.find_all('td',class_=re.compile('type_\w_r'))))
# 标签
type_tags=list(map(lambda x:get_text_strip(x).replace(HTML_TAR_BR,"/").split('/'),soup.find_all('td',class_=re.compile('type_tag_r*'))))
# 工作人员
staffs=list(map(strip_html_br,soup.find_all('td',class_=re.compile('staff_r*'))))
# 演员
casts=list(map(strip_html_br,soup.find_all('td',class_=re.compile('cast_r*'))))
# 相关链接
links=list(map(lambda x:list(map(lambda e:{'name':e.text.strip(),'url':e['href']},x.find_all('a'))),soup.find_all('td',class_="link_a_r")))
# 播出时间
broadcasts=list(map(lambda x:get_text_strip(x).replace(HTML_TAR_BR,'-'),soup.find_all('p',class_="broadcast_r")))
# 集数
broadcast_episodes=list(map(lambda x:x.text.replace(HTML_TAR_BR,'').strip(),soup.find_all('p',class_='broadcast_ex_r')))

animes=[]
for i in range(len(cn_titles)):
    anime={}
    anime['cn_title']=cn_titles[i]
    anime['jp_title']=jp_titles[i]
    anime['type']=type_animes[i]
    anime['tags']=type_tags[i]
    anime['staff']=staffs[i]
    anime['cast']=casts[i]
    anime['link']=links[i]
    anime['broadcast']=broadcasts[i]
    anime['episodes']=broadcast_episodes[i]
    animes.append(anime)

pprint(animes[0])
```

示例输出:

```
{'broadcast': '7/9周三深夜',
 'cast': ['小笠原亚里沙',
          '伊濑茉莉也',
          '石井康嗣',
          '吉野裕行',
          '中村贵志',
          '小松由佳',
          '渡边明乃',
          '夏吉优子',
          '榎木淳弥',
          '上村祐翔'],
 'cn_title': '新 吊带袜天使',
 'episodes': '(全13话)',
 'jp_title': 'New PANTY&STOCKING with GARTERBELT',
 'link': [{'name': '动画官网', 'url': 'https://newpsg.com/'},
          {'name': 'PV',
           'url': 'https://www.bilibili.com/video/BV1knXnYeE2e/'}],
 'staff': ['原案：GEEKFLEET',
           '导演：今石洋之',
           '副导演：若林广海',
           '古川晟',
           '编剧：今石洋之',
           '若林广海',
           '角色设计：锦织敦史',
           '动画人设：小山重人',
           '石崎寿夫',
           '坂本胜',
           '动画制作：TRIGGER'],
 'tags': ['美式喜剧', '脑洞', '无节操', '无厘头'],
 'type': '原创动画'}
```

恭喜 🥳🥳🥳
