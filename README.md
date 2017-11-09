# Crawel-based-bs4
###### A Crawel for DouBan_Movie Based on BeautifulSoup

==========================================================

```
爬虫练手
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

    print('Getting Data--> ' + nowplaying_dict['name'])

    nowplaying_list.append(nowplaying_dict)

playing_movie = []
pre_playing_movie = []

for element in nowplaying_list:
    if element['playing'] == '已经上映' and element['rating'] != '无评论':
        playing_movie.append(element)
    else:
        pre_playing_movie.append(element)

print('\n已上映电影（按评分和评论人数排名）：')
count = 0
playing_movie.sort(key=lambda x: (x.get('rating', 0), x.get('score', 0)), reverse=True)
for element in playing_movie:
    print('排名：%d ；电影名称：%s ；评分：%.1f ；评论人数：%d'%(count,element['name'],element['score'],element['rating']))
    count += 1

print('\n未上映电影：')
for element in pre_playing_movie:
    print('电影名称：%-s ；上映时间：%s'%(element['name'],str(element['data'])[:10]))


```


```
运行结果：（2017-10-26）
爬虫正在工作：
Getting Data--> 全球风暴
Getting Data--> 爱情邮局
Getting Data--> 王牌特工2：黄金圈
Getting Data--> 银翼杀手2049
Getting Data--> 羞羞的铁拳
Getting Data--> 天才枪手
Getting Data--> 常在你左右
Getting Data--> 追龙
Getting Data--> 缝纫机乐队
Getting Data--> 我的爸爸是森林之王
Getting Data--> 恐怖电影院2
Getting Data--> 十八洞村
Getting Data--> 英伦对决
Getting Data--> 因为爱情
Getting Data--> 火力全开
Getting Data--> 怨灵2
Getting Data--> 蝴蝶公墓
Getting Data--> 空天猎
Getting Data--> 时间去哪儿了
Getting Data--> 胡杨的夏天
Getting Data--> 看不见的客人
Getting Data--> 坑爹游戏
Getting Data--> 情比山高
Getting Data--> 童话先生
Getting Data--> 情遇曼哈顿
Getting Data--> 极致追击
Getting Data--> 七十七天
Getting Data--> 相爱相亲
Getting Data--> 回到火星
Getting Data--> 请爱我的女朋友
Getting Data--> 智取威虎山
Getting Data--> 雷神3：诸神黄昏
Getting Data--> 海边的曼彻斯特
Getting Data--> 昆塔：反转星球
Getting Data--> 二十二

已上映电影（按评分和评论人数排名）：
排名：0 ；电影名称：看不见的客人 ；评分：8.7 ；评论人数：248675
排名：1 ；电影名称：智取威虎山 ；评分：7.7 ；评论人数：209060
排名：2 ；电影名称：羞羞的铁拳 ；评分：7.3 ；评论人数：192779
排名：3 ；电影名称：海边的曼彻斯特 ；评分：8.6 ；评论人数：160854
排名：4 ；电影名称：天才枪手 ；评分：8.3 ；评论人数：124172
排名：5 ；电影名称：二十二 ；评分：8.8 ；评论人数：81968
排名：6 ；电影名称：王牌特工2：黄金圈 ；评分：7.3 ；评论人数：75936
排名：7 ；电影名称：追龙 ；评分：7.5 ；评论人数：60833
排名：8 ；电影名称：缝纫机乐队 ；评分：7.0 ；评论人数：51847
排名：9 ；电影名称：英伦对决 ；评分：7.3 ；评论人数：49733
排名：10 ；电影名称：空天猎 ；评分：4.9 ；评论人数：27407
排名：11 ；电影名称：回到火星 ；评分：5.8 ；评论人数：5186
排名：12 ；电影名称：极致追击 ；评分：3.5 ；评论人数：2977
排名：13 ；电影名称：时间去哪儿了 ；评分：6.0 ；评论人数：1916
排名：14 ；电影名称：情遇曼哈顿 ；评分：4.3 ；评论人数：1368
排名：15 ；电影名称：火力全开 ；评分：8.5 ；评论人数：1166
排名：16 ；电影名称：昆塔：反转星球 ；评分：6.4 ；评论人数：816
排名：17 ；电影名称：十八洞村 ；评分：6.9 ；评论人数：762
排名：18 ；电影名称：我的爸爸是森林之王 ；评分：6.6 ；评论人数：470

未上映电影：
电影名称：全球风暴 ；上映时间：2017-10-27
电影名称：爱情邮局 ；上映时间：2017-10-26
电影名称：银翼杀手2049 ；上映时间：2017-10-27
电影名称：常在你左右 ；上映时间：2017-10-27
电影名称：恐怖电影院2 ；上映时间：2017-10-20
电影名称：因为爱情 ；上映时间：2017-10-27
电影名称：怨灵2 ；上映时间：2017-10-12
电影名称：蝴蝶公墓 ；上映时间：2017-10-20
电影名称：胡杨的夏天 ；上映时间：2017-10-20
电影名称：坑爹游戏 ；上映时间：2017-10-20
电影名称：情比山高 ；上映时间：2017-10-24
电影名称：童话先生 ；上映时间：2017-10-24
电影名称：七十七天 ；上映时间：2017-11-03
电影名称：相爱相亲 ；上映时间：2017-11-03
电影名称：请爱我的女朋友 ；上映时间：2017-10-20
电影名称：雷神3：诸神黄昏 ；上映时间：2017-11-03
