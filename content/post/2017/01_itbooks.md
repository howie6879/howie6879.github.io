---
title: ITBooks—简单的书籍下载小工具
date: 2017-01-26T23:07:42
categories:
  - Python
  - 项目
tags: [Spider,效率]
---

## 1.前言

我有个习惯就是收藏一些书籍，比如说编程类的，总是会去某些网站刷刷，若有新书籍更新恰又是自己感兴趣的，自然会立马下载下来，写程序的都知道，编程书籍更新换代太快，国内的翻译的速度很难全面地跟上，对此，阅读国外的电子书籍是个途径。

很早就想写个书籍集成的脚本，本周女朋友回学校改论文，鬼使神差地做起了这件事，白天上班，晚上撸，准备集成四个网站，目前实现了两个，可以进行搜索和下载。前后花了四个晚上，有点黑眼圈。

下面展示下`0.1`版本的成果，比如说检索`Mastering python`，空格用`+`代替：

```shell
python abook.py search Mastering+python Hattem
```

![TF6CYl](https://cdn.jsdelivr.net/gh/howie6879/oss/images/TF6CYl.jpg)

## 2.说明

项目名称叫做[ITBooks](https://github.com/howie6879/ITBooks)，当然不准备只做`IT`方面书籍，文学名著也是可以的。

![9kBp2e](https://cdn.jsdelivr.net/gh/howie6879/oss/images/9kBp2e.jpg)

> [ITBooks](https://github.com/howie6879/ITBooks)，是一个查询和下载书籍的`python`脚本。

目前只存储了`allitebooks blah`网站的书籍信息，接下来会更多的集成各个网站，因为想写成个人服务脚本，故使用sqlite3数据库，使用  `scrapy`爬取信息，具体看`ITBooks/spider/allitebooks`，爬虫部分可直接运行从而进行书籍信息更新；你可以通过变写爬虫来添加自己喜欢的网站。
添加爬虫只需要在`ITBooks/spider/allitebooks/spider`，下新建一个`scrapy`的爬虫脚本即可，对于数据的存储，具体在`ITBooks/database
`，当然，你可以直接新建一个库。
这里放个`allitebooks`的爬虫脚本，这里说明下，**这个良心网站很不错，希望各位爬的时候慢点。不然崩了大家都玩完**。

``` python
# -*- coding: utf-8 -*-
import datetime
import sys
import scrapy
from scrapy.loader import ItemLoader
from scrapy.loader.processors import MapCompose, TakeFirst, Join
from scrapy.http import Request
from allitebooks.items import AllitebooksItem
from allitebooks.pipelines import AllitebooksPipeline

if sys.version_info[0] > 2:
    from urllib.parse import urljoin
else:
    from urlparse import urljoin


class AllitebooksSpider(scrapy.Spider):
    name = "allitebooks"
    allowed_domains = ["allitebooks.com"]
    start_urls = ('http://www.allitebooks.com', )

    def parse(self, response):
        """
        Get the next pages and yield requests
        :param response:
        :return: request url
        """
        pages = response.css('.pagination>a::text').extract()[-1]
        for page in range(int(pages)):
            # Generate a list of urls
            url = urljoin(response.url, '/page/' + str(page + 1))
            yield Request(url, callback=self.parse_detail)

    def parse_detail(self, response):
        """
        Get item URLs and yield Requests
        :param response:
        :return: request url
        """
        urls = response.css(
            'article>div.entry-body>header>.entry-title>a::attr(href)'
        ).extract()
        for url in urls:
            # Remove duplicate links
            sqlDb = AllitebooksPipeline()
            isExist = sqlDb.search_url('allitebooks', url)
            if not isExist:
                yield Request(url, callback=self.parse_item)

    def parse_item(self, response):
        """
        This function parses a property page.
        :param response:
        :return: item
        """
        # Create the loader using response
        l = ItemLoader(item=AllitebooksItem(), response=response)
        # Load primary fields using css expressions
        l.add_css('title', '.single-title::text', MapCompose(str.strip))
        l.add_css('cover', '.entry-body-thumbnail>a>img::attr(src)')
        book_details = response.css('.book-detail>dl>dd::text').extract()
        author_list = response.css(
            '.book-detail>dl>dd:nth-child(2)>a::text').extract()
        category_list = response.css(
            '.book-detail>dl>dd:nth-child(16)>a::text').extract()
        author = ','.join(author_list)
        category = ','.join(category_list)
        book_details = book_details[len(author_list):(-len(category_list))]
        l.add_value('author', author, MapCompose(str.strip))
        l.add_value('category', category, MapCompose(str.strip))
        item_name = "isbn year pages language file_size file_format".split()
        for index, value in enumerate(item_name):
            l.add_value(value, book_details[index], MapCompose(str.strip))
        l.add_css('description', '.entry-content')
        l.add_css('download', 'span.download-links>a::attr(href)',
                  MapCompose(str.strip), TakeFirst())
        # Housekeeping fields
        l.add_value('url', response.url)
        l.add_value('spider', self.name)
        l.add_value('date', datetime.datetime.now())
        yield l.load_item()

```
现在就是由于想在本地运行，对于自动更新是个问题，本来想使用`celery`将`scrapy`爬虫弄成一个个任务，我是可以在机器上这样部署，可是这对于使用者不大现实，目前是手动更新（确实很挫）。

接下来说说使用方法，如果你感兴趣，可以试试^_^
脚本在`python2.7+和python3`下都可以运行，我测试过，但是爬虫却没在`python2`下测过，所以我建议还是用3吧。
运行以下命令：
``` shell
git clone https://github.com/howie6879/ITBooks
cd /ITBooks/ITBooks
python abook.py search Mastering+Python Hattem
```
具体使用方法如下：
```
  python abook.py search <title> <author>
  python abook.py title <title>
  python abook.py author <author>
```
也在根目录下写了个`bash`，其实没什么差别：
```shell
bash run.sh [search|title|author] <value>
```

![7LicCz](https://cdn.jsdelivr.net/gh/howie6879/oss/images/7LicCz.jpg)


![daudEi](https://cdn.jsdelivr.net/gh/howie6879/oss/images/daudEi.jpg)


![ry3NLb](https://cdn.jsdelivr.net/gh/howie6879/oss/images/ry3NLb.jpg)

## 总结
写在最后，本人技术微末，如果错漏，欢迎指教，项目`github`地址在这里[ITBooks](https://github.com/howie6879/ITBooks)，欢迎各位添砖加瓦。