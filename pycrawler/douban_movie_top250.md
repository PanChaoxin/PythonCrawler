# 请求的API URL

格式：

- 第1页: http://api.douban.com/v2/movie/top250?start=0&count=25
- 第2页: http://api.douban.com/v2/movie/top250?start=25&count=25
- 第3页: http://api.douban.com/v2/movie/top250?start=50&count=25
- 第10页（最后一页）: http://api.douban.com/v2/movie/top250?start=225&count=25



# 响应体分析

响应体是json：

```json
{
    "count": 25,
    "start": 0,
    "total": 250,
    "subjects": [
    {
        "rating":
        {
            "max": 10,
            "average": 9.6,
            "stars": "50",
            "min": 0
        },
        "genres": ["\u72af\u7f6a", "\u5267\u60c5"],
        "title": "\u8096\u7533\u514b\u7684\u6551\u8d4e",
        "casts": [
        {
            "alt": "https:\/\/movie.douban.com\/celebrity\/1054521\/",
            "avatars":
            {
                "small": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p17525.webp",
                "large": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p17525.webp",
                "medium": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p17525.webp"
            },
            "name": "\u8482\u59c6\u00b7\u7f57\u5bbe\u65af",
            "id": "1054521"
        },
        {
            "alt": "https:\/\/movie.douban.com\/celebrity\/1054534\/",
            "avatars":
            {
                "small": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p34642.webp",
                "large": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p34642.webp",
                "medium": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p34642.webp"
            },
            "name": "\u6469\u6839\u00b7\u5f17\u91cc\u66fc",
            "id": "1054534"
        },
        {
            "alt": "https:\/\/movie.douban.com\/celebrity\/1041179\/",
            "avatars":
            {
                "small": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p5837.webp",
                "large": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p5837.webp",
                "medium": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p5837.webp"
            },
            "name": "\u9c8d\u52c3\u00b7\u5188\u987f",
            "id": "1041179"
        }],
        "collect_count": 1468145,
        "original_title": "The Shawshank Redemption",
        "subtype": "movie",
        "directors": [
        {
            "alt": "https:\/\/movie.douban.com\/celebrity\/1047973\/",
            "avatars":
            {
                "small": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p230.webp",
                "large": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p230.webp",
                "medium": "http://img7.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p230.webp"
            },
            "name": "\u5f17\u5170\u514b\u00b7\u5fb7\u62c9\u90a6\u7279",
            "id": "1047973"
        }],
        "year": "1994",
        "images":
        {
            "small": "http://img7.doubanio.com\/view\/photo\/s_ratio_poster\/public\/p480747492.webp",
            "large": "http://img7.doubanio.com\/view\/photo\/s_ratio_poster\/public\/p480747492.webp",
            "medium": "http://img7.doubanio.com\/view\/photo\/s_ratio_poster\/public\/p480747492.webp"
        },
        "alt": "https:\/\/movie.douban.com\/subject\/1292052\/",
        "id": "1292052"
    },
    {
        "rating":
        {
            "max": 10,
            "average": 9.6,
            "stars": "50",
            "min": 0
        },
        "genres": ["\u5267\u60c5", "\u7231\u60c5", "\u540c\u6027"],
        "title": "\u9738\u738b\u522b\u59ec",
        "casts": [
        {
            "alt": "https:\/\/movie.douban.com\/celebrity\/1003494\/",
            "avatars":
            {
                "small": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p67.webp",
                "large": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p67.webp",
                "medium": "http://img3.doubanio.com\/view\/celebrity\/s_ratio_celebrity\/public\/p67.webp"
            },
            "name": "\u5f20\u56fd\u8363",
            "id": "1003494"
        }],
    "title": "\u8c46\u74e3\u7535\u5f71Top250"
}
```





# requests爬虫代码

程序功能：通过豆瓣API 爬取豆瓣电影TOP 250,把影片名称、导演、类型、主演、评分、链接地址爬下来，保存为CSV文件

```python
import requests

url = 'http://api.douban.com/v2/movie/top250'

with open('douban_movie_top250.csv', 'w', encoding='utf-8') as output:
    for start in range(0, 250, 25):
        # 返回requests.models.Response类型
        r = requests.get(url, params={'start': start, 'count': 25})
        print('==========================> processing: %s' % r.url)

        # r.json()以json格式解析响应体，并返回dict类型
        res = r.json()

        ''' 返回的json数据
        count: "25"        记录数
        start: "0"         起始序号
        total: "250"       总记录数
        title: "豆瓣电影Top250"
        subjects: [{}, {}] json数组，每一个json元素对应一部电影的信息
        '''

        for movie in res['subjects']:
            title = movie['title']  # 提取电影名称
            directors = ' / '.join([d['name'] for d in movie['directors']])  # 提取导演名称，多个用' / '分隔
            genres = ' / '.join(movie['genres'])  # 提取电影类型，多个用' / '分隔
            casts = ' / '.join([c['name'] for c in movie['casts']])  # 提取主演（演员阵容），多个用' / '分隔
            score = movie['rating']['average']  # 提取评分
            alt = movie['alt']  # 提取链接地址

            line = '{},{},{},{},{},{}'.format(title, directors, genres, casts, score, alt)
            output.write('{}\n'.format(line))
            # 显示写入的记录
            print('写入 ===> {}'.format(line))
```

