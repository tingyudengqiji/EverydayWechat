# EverydayWechat
1. 每天定时给多个女友发给微信暖心话 
2. 借助图灵实现自动回复. 避免长时间不回复女友,家庭暴力很危险呀

本项目非原创,只是在这位大佬https://github.com/sfyc23/EverydayWechat 的基础上补充了图灵自动回复功能,毕竟我觉得不回复女朋友的后果很严重

# 用 Python + itchat 写一个爬虫脚本自动给多人发给微信暖心话

## 项目介绍：

### 灵感来源

好多地方都看到有这个项目的推文,所以就上来找到了大佬的源码. 但大佬的源码只有定时发送功能,出于学习的目的,又在原作者的基础上新增了自动回复功能 

本来只是写单人的，不过有些优（作）秀（死）的人表示女朋友不止一个。现已支持添加多人信息。(这句话是作者说的---疯狂甩锅)

### 项目地址：
Github: [https://github.com/wujingfeng/EverydayWechat](https://github.com/wujingfeng/EverydayWechat)。

### 使用库
- [itchat][2] - 微信个人号接口
- [requests][3] - 网络请求库
- [beautifulsoup4][4] - 解析网页
- [APScheduler][5] - 定时任务

### 功能
定时给女朋友发送每日天气、提醒、每日一句。

### 数据来源
- 每日一句和上面的大佬一样也是来自 [ONE●一个][6]
- 天气信息来自 [SOJSON][7] 


### 实现效果
![命令行信息](http://vlog.sfyc23.xyz/wechat_everyday/20190312010620.png)  
![微信截图](http://vlog.sfyc23.xyz/wechat_everyday/20190312010621.png)

## 代码说明

### 目录结构

- CityDict.py ：城市对应编码字典
- _config.yaml ：设置定时时间，女友微信名称等参数
- TLWeather.py：核心代码 直接运行
- requirements.txt：需要安装的库

### 核心代码

#### 1.定时任务。(取消,改为启动便发送)
每天 9：30 给女朋友们开始给女朋友发送内容。
```
# 定时任务
scheduler = BlockingScheduler()
# 每天9：30给女朋友发送每日一句
# scheduler.add_job(start_today_info, 'cron', hour=9, minute=30)
scheduler.start()
```
*start_today_info* 是方法处理类。

#### 2.获取每日一句。
数据来源： [ONE●一个][6]
```
def get_dictum_info(self):
    '''
    获取格言信息（从『一个。one』获取信息 http://wufazhuce.com/）
    :return: str 一句格言或者短语
    '''
    print('获取格言信息..')
    user_url = 'http://wufazhuce.com/'
    resp = requests.get(user_url, headers=self.headers)
    soup_texts = BeautifulSoup(resp.text, 'lxml')
    # 『one -个』 中的每日一句
    every_msg = soup_texts.find_all('div', class_='fp-one-cita')[0].find('a').text
    return every_msg
```
#### 3. 获取今日天气 。
天气数据来源：[SOJSON][7]

```
def get_weather_info(self, city_code=''）：
    weather_url = f'http://t.weather.sojson.com/api/weather/city/{city_code}'
    resp = requests.get(url=weather_url)
    if resp.status_code == 200 and resp.json().get('status') == 200:
        weatherJson = resp.json()
        # 今日天气
        today_weather = weatherJson.get('data').get('forecast')[1]
```
city_code 城市对应 id。
[http://cdn.sojson.com/_city.json](http://cdn.sojson.com/_city.json)

#### 4. 登录微信并发送内容。
```
itchat.auto_login()
itchat.send(today_msg, toUserName=name_uuid)
```


## 项目运行

### 安装依赖

使用 pip install -r requirements.txt 安装所有依赖

### 参数配置
config.yaml
```
#每天的几点开始发送信息
alarm_timed: '9:30'
#图灵key
tuling_key: '359a013d925040a28e6191831f9f36bc'
girlfriend_infos:
  -
    #女友微信昵称
    wechat_name: '动霸tua'
    #女友所在城市
    city_name: '成都'
    # 从那天开始勾搭的（可空）
    start_date: '2012-12-9'
    # 短句的最后留言（可空）
    sweet_words: '来自最爱你的我。'

    #如果你有多个人需要发送，则参照这个样式，复制即可
    #如不需要，则删除或注解下面所有的数据
    # wechat_name: '陈老师'
    # city_name: '朝阳区'
    # start_date: '2018-11-11'
    # sweet_words: '来自你俊美的老公。'
```

### 开始运行
```
python TLwechat.py
```

## 最后
本项目非原创,只是在这位大佬https://github.com/sfyc23/EverydayWechat 的基础上补充了图灵自动回复功能


  [1]: https://juejin.im/post/5c77c6bef265da2de6611cff
  [2]: https://github.com/littlecodersh/ItChat
  [3]: http://docs.python-requests.org/en/master/
  [4]: https://www.crummy.com/software/BeautifulSoup/bs4/doc/index.zh.html#
  [5]: https://apscheduler.readthedocs.io/en/latest/
  [6]: http://wufazhuce.com/
  [7]: https://www.sojson.com/blog/305.html
