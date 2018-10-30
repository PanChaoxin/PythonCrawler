# 请求的URL

格式：

- 第1页: https://book.douban.com/top250?start=0
- 第2页: https://book.douban.com/top250?start=25
- 第3页: https://book.douban.com/top250?start=50
- 第10页（最后一页）:  https://book.douban.com/top250?start=225



# HTML结构分析

每一本书对应的html结构：

```html
<tr class="item">
    <td width="100" valign="top">
        <a class="nbg" href="https://book.douban.com/subject/1770782/" onclick="moreurl(this,{i:'0'})">
            <img src="https://img3.doubanio.com/view/subject/m/public/s1727290.jpg" width="90">
        </a>
    </td>
    <td valign="top">
        <div class="pl2">
            <!-- href属性值: 本书的展示页面 -->
            <!-- title属性值: 书名 -->
            <a href="https://book.douban.com/subject/1770782/" onclick="&quot;moreurl(this,{i:'0'})&quot;" title="追风筝的人">追风筝的人</a> &nbsp; <img src="https://img3.doubanio.com/pics/read.gif" alt="可试读" title="可试读">
            <br>
            <span style="font-size:12px;">The Kite Runner</span>
        </div>
        <!-- 作者 / 译者 / 出版社 / 出版年 / 定价 -->
        <p class="pl">[美] 卡勒德·胡赛尼 / 李继宏 / 上海人民出版社 / 2006-5 / 29.00元</p>
        <div class="star clearfix">
            <span class="allstar45"></span>
            <!-- 评分 -->
            <span class="rating_nums">8.9</span>
            <!-- 评价人数 -->
            <span class="pl">(345562人评价)</span>
        </div>
        <p class="quote" style="margin: 10px 0; color: #666">
            <!-- 描述-->
            <span class="inq">为你，千千万万遍</span>
        </p>
    </td>
</tr>
```



# urllib.request爬虫代码

程序功能：爬取豆瓣图书TOP 250，把书名、作者、评分爬下来，保存为CSV文件。

```python
import urllib.request as urlrequest
from bs4 import BeautifulSoup

top250_url = "https://book.douban.com/top250?start={}"

with open('douban_book_top250.csv', 'w', encoding='utf-8') as output:
    for i in range(10):  # 遍历10页
        start = i * 25
        url_visit = top250_url.format(start)
        crawl_content = urlrequest.urlopen(url_visit).read()
        http_content = crawl_content.decode('utf8')  # 获取http
        soup = BeautifulSoup(http_content, 'html.parser')  # 解析http

        all_item_divs = soup.find_all(class_='item')  # item是整本书的html结构
        for item_div in all_item_divs:
            title_div = item_div.find(class_='pl2')
            item_name = title_div.find('a')['title']  # 获取图书标题

            '''
                pl_text例子: "[日] 黑柳彻子 著 / 赵玉皎 / 南海出版公司 / 2003-1 / 20.00元,8.7"
                    1、split('/')[0]取出第1项，是作者信息
                    2、strip(' 著') 去掉 空格" "以及"著"
            '''
            pl_text = item_div.find(class_='pl').get_text()
            item_author = pl_text.split('/')[0].strip(' 著')

            score_div = item_div.find(class_='star clearfix')
            item_rating = score_div.find(class_='rating_nums').get_text()  # 获取图书评分

            data = '{},{},{}'.format(item_name, item_author, item_rating)   # 写入 书名、作者、评分
            output.write('{}\n'.format(data))
            print('写入 ===> {}'.format(data))        # 显示写入的记录
```

