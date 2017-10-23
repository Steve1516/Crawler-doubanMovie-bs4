# Crawel-based-bs4
###### A Crawel for DouBan_Movie Based on BeautifulSoup

==========================================================

```
媳妇不让总去电影院，去一次得磨半天...
正好用爬虫练个手，找找最热门的电影，关注下近期上映情况！
```

`代码如下`

```Python

from urllib import request
movie_list_resp = request.urlopen('https://movie.douban.com/nowplaying/hangzhou/')
movie_list_html_data = movie_list_resp.read().decode('utf-8')

from bs4 import BeautifulSoup as bs
movie_list_soup = bs(movie_list_html_data, 'html.parser')

nowplaying_movie = movie_list_soup.find_all('div',id = 'nowplaying')
nowplaying_movie_list = nowplaying_movie[0].find_all('li', class_ = 'list-item')

import datetime

print('爬虫正在工作：')

nowplaying_list = []
for item in nowplaying_movie_list:
    nowplaying_dict = {}
    nowplaying_dict['id'] = item['data-subject']
    nowplaying_dict['name'] = item['data-title']
    nowplaying_dict['score'] = float(item['data-score'])

    each_movie_url = 'https://movie.douban.com/subject/' + nowplaying_dict['id'] + '/?from=playing_poster'
    each_movie_resp = request.urlopen(each_movie_url)
    each_movie_html_data = each_movie_resp.read().decode('utf-8')
    each_movie_soup = bs(each_movie_html_data, 'html.parser')

    nowplaying_dict['data'] = datetime.datetime.strptime(each_movie_soup.find_all('span', property = 'v:initialReleaseDate')[0].string[:10],'%Y-%m-%d')
    if (datetime.datetime.now() - nowplaying_dict['data']).days > 0 :
        nowplaying_dict['playing'] = '已经上映'
    else:
        nowplaying_dict['playing'] = '暂未上映'

    try:
        if nowplaying_dict['playing'] == '已经上映':
            nowplaying_dict['rating'] = int(each_movie_soup.find_all('span', property = 'v:votes')[0].string)
        else:
            nowplaying_dict['rating'] = '无评论'
    except:
        nowplaying_dict['rating'] = '无评论'

    print('Getting Date--> ' + nowplaying_dict['name'])

    nowplaying_list.append(nowplaying_dict)

playing_movie = []
pre_playing_movie = []

for element in nowplaying_list:
    if element['playing'] == '已经上映' and element['rating'] != '无评论':
        playing_movie.append(element)
    else:
        pre_playing_movie.append(element)

print('\n已上映电影（按评分和观影人数排名）：')
count = 0
playing_movie.sort(key=lambda x: (x.get('score', 0), x.get('rating', 0)), reverse=True)
for element in playing_movie:
    print('排名：%d ；电影名称：%s ；评分：%.1f ；观影人数：%d'%(count,element['name'],element['score'],element['rating']))
    count += 1

print('\n未上映电影：')
for element in pre_playing_movie:
    print('电影名称：%-s ；上映时间：%s'%(element['name'],str(element['data'])[:10]))


```


```
运行结果：（2017-10-24）
爬虫正在工作：
Getting Date--> 王牌特工2：黄金圈
Getting Date--> 情比山高
Getting Date--> 童话先生
Getting Date--> 羞羞的铁拳
Getting Date--> 全球风暴
Getting Date--> 天才枪手
Getting Date--> 银翼杀手2049
Getting Date--> 十八洞村
Getting Date--> 追龙
Getting Date--> 缝纫机乐队
Getting Date--> 常在你左右
Getting Date--> 我的爸爸是森林之王
Getting Date--> 恐怖电影院2
Getting Date--> 火力全开
Getting Date--> 英伦对决
Getting Date--> 时间去哪儿了
Getting Date--> 蝴蝶公墓
Getting Date--> 空天猎
Getting Date--> 战狼2
Getting Date--> 怨灵2
Getting Date--> 胡杨的夏天
Getting Date--> 坑爹游戏
Getting Date--> 看不见的客人
Getting Date--> 情遇曼哈顿
Getting Date--> 因为爱情
Getting Date--> 回到火星
Getting Date--> 请爱我的女朋友
Getting Date--> 极致追击
Getting Date--> 你若安好
Getting Date--> 七十七天
Getting Date--> 智取威虎山
Getting Date--> 捍卫者
Getting Date--> 二十二
Getting Date--> 海边的曼彻斯特
Getting Date--> 六年，六天
Getting Date--> 昆塔：反转星球
Getting Date--> 大爱无言

已上映电影（按评分和观影人数排名）：
排名：0 ；电影名称：二十二 ；评分：8.8 ；观影人数：81883
排名：1 ；电影名称：看不见的客人 ；评分：8.7 ；观影人数：247588
排名：2 ；电影名称：火力全开 ；评分：8.7 ；观影人数：1079
排名：3 ；电影名称：海边的曼彻斯特 ；评分：8.6 ；观影人数：160513
排名：4 ；电影名称：天才枪手 ；评分：8.3 ；观影人数：118250
排名：5 ；电影名称：捍卫者 ；评分：7.8 ；观影人数：2445
排名：6 ；电影名称：智取威虎山 ；评分：7.7 ；观影人数：208947
排名：7 ；电影名称：追龙 ；评分：7.5 ；观影人数：60100
排名：8 ；电影名称：战狼2 ；评分：7.4 ；观影人数：393789
排名：9 ；电影名称：羞羞的铁拳 ；评分：7.3 ；观影人数：190265
排名：10 ；电影名称：王牌特工2：黄金圈 ；评分：7.3 ；观影人数：67017
排名：11 ；电影名称：英伦对决 ；评分：7.3 ；观影人数：49369
排名：12 ；电影名称：缝纫机乐队 ；评分：7.0 ；观影人数：51042
排名：13 ；电影名称：十八洞村 ；评分：6.9 ；观影人数：690
排名：14 ；电影名称：我的爸爸是森林之王 ；评分：6.6 ；观影人数：440
排名：15 ；电影名称：昆塔：反转星球 ；评分：6.4 ；观影人数：812
排名：16 ；电影名称：时间去哪儿了 ；评分：6.0 ；观影人数：1825
排名：17 ；电影名称：回到火星 ；评分：5.8 ；观影人数：5161
排名：18 ；电影名称：空天猎 ；评分：4.9 ；观影人数：27332
排名：19 ；电影名称：情遇曼哈顿 ；评分：4.1 ；观影人数：1341
排名：20 ；电影名称：极致追击 ；评分：3.5 ；观影人数：2965

未上映电影：
电影名称：情比山高 ；上映时间：2017-10-24
电影名称：童话先生 ；上映时间：2017-10-24
电影名称：全球风暴 ；上映时间：2017-10-27
电影名称：银翼杀手2049 ；上映时间：2017-10-27
电影名称：常在你左右 ；上映时间：2017-10-27
电影名称：恐怖电影院2 ；上映时间：2017-10-20
电影名称：蝴蝶公墓 ；上映时间：2017-10-20
电影名称：怨灵2 ；上映时间：2017-10-12
电影名称：胡杨的夏天 ；上映时间：2017-10-20
电影名称：坑爹游戏 ；上映时间：2017-10-20
电影名称：因为爱情 ；上映时间：2017-10-27
电影名称：请爱我的女朋友 ；上映时间：2017-10-20
电影名称：你若安好 ；上映时间：2017-10-17
电影名称：七十七天 ；上映时间：2017-11-03
电影名称：六年，六天 ；上映时间：2017-10-17
电影名称：大爱无言 ；上映时间：2017-10-20
