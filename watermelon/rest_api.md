接口文档
=========================

### preview:

本项目的用户登录态使用jwt(json web token)作为登录态令牌  
（可看博客简单介绍：http://www.ruanyifeng.com/blog/2018/07/json_web_token-tutorial.html）  
需要登录后才能访问的接口，要求request-header 中带上服务端下发的jwt，放在 Authorization 头部中  
如果请求没有带上jwt或者jwt已经失效，则这些接口将统一返回 http 状态码 403，在这场景下请将用户重新走登录流程  

#### 返回示例

http status code : 403  

body:

```json
{
  "code": "1=no login 2=expired",
  "message": "提示信息"
}
```

### 备注

本项目的测试服务部署在同一个域名下，接口加上 /dev 的前缀，例如接口 /api/shit , 则测试服的接口为 /dev/api/shit  

## 注意

#### 上线之前切记要把调试开关关掉！！！ 否则所有请求将打到测试服务，造成意想不到的后果！！！


### 接口列表

* [题库查询](#题库查询)
* [语音查询](#语音查询)
* [登录](#登录)
* [获取用户信息](#获取用户信息)
* [校验登录态 - 废弃](#校验登录态)
* [获取公告内容](#获取公告内容)
* [获取首页轮播图内容](#获取首页轮播图内容)
* [获取信息流tab](#获取信息流tab)
* [获取文章信息流](#获取文章信息流)
* [自增文章阅读量](#自增文章阅读量)
* [广告免除](#广告免除)
* [pdd.ddk查询接口](pdd.ddk查询接口)



题库查询
--------------------

```http
GET /api/watermelon/search
```

| 参数 | 描述 | required |
| --- | --- | --- |
| search_words | 查询关键词 - 多个关键词用空格隔开 | Y |


题库搜索，需要登录态


#### 返回

请求成功  
http status code: 200  

```json
{
  "question_list": [
    {
      "id": 2427,
      "question_text": "“故人西辞黄鹤楼，烟花三月下扬州。孤帆远影碧空尽，唯见长江天际流”，是唐代诗人【A 李白】的诗作",
      "category": 1,
      "created_at": "2019-06-30T16:37:29+08:00",
      "UpdatedAt": "2019-06-30T16:37:29+08:00"
    },
    {
    
    }
  ],
  "word_list": [
        "李白", "故人"
  ]  
}
```

请求失败  
http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```


语音查询
--------------------

```http
POST /api/watermelon/voice_search
```


| 参数 | 描述 | required |
| --- | --- | --- |
| voice_file | 音频文件，mp3格式，小程序录制 | Y |

请求content-type : multipart/form-data  

题库语音搜索，需要登录态  

本接口需要调用微信提供的免费语音识别接口，响应耗时需要2s以上，因此客户端最好提供友好的请求等待提示，比如等待响应的过程中唤起"加载图标"  
微信提供的识别接口文档地址：[地址](https://developers.weixin.qq.com/doc/offiaccount/Intelligent_Interface/AI_Open_API.html)  

#### 返回

请求成功  
http status code: 200  

```json
{
  "question_list": [
    {
      "id": 2427,
      "question_text": "“故人西辞黄鹤楼，烟花三月下扬州。孤帆远影碧空尽，唯见长江天际流”，是唐代诗人【A 李白】的诗作",
      "category": 1,
      "created_at": "2019-06-30T16:37:29+08:00",
      "UpdatedAt": "2019-06-30T16:37:29+08:00"
    },
    {
    
    }
  ],
  "word_list": [
        "李白", "故人"
  ]  
}
```

请求失败  
http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```



登录
---------------------

```http
POST /api/watermelon/login
```

| 参数 | 描述 | required |
| --- | --- | --- |
| js_code | 小程序登录所需的code，用于获取用户的openid等信息 | Y |
| nickname | 用户的昵称 | N |
| avatar_url | 用户的头像url | N |
| gender | 用户的性别 | N |
| province | 用户的省份 | N |
| city | 用户的城市 | N |
| country | 用户的国家 | N |

小程序将用户登入，提交给服务端，服务端下发jwt，小程序客户端需要将下发的jwt缓存起来，用于后续的请求

#### 返回 

成功时  
http status code : 200

```json
{
  "jwt": "jwt字符串"
}
```

请求失败  
http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```


获取用户信息
---------------------

```http
GET /api/watermelon/user_info
```

本接口需要jwt登录态

#### 返回

请求成功  
http status code: 200  

```json
{
  "user_info": {
      "id": "user id",
      "wc_union_id": "微信的用户union id",
      "wc_open_id": "微信的用户open id",
      "wc_nickname": "昵称",
      "wc_avatar_url": "头像url",
      "wc_gender": "性别",
      "wc_province": "Guangdong",
      "wc_city": "Guangzhou",
      "wc_country": "China"
    }
}
```

请求失败  
http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```



校验登录态
---------------------

```http
GET /api/watermelon/check_jwt
```

本接口主要用于调试，即将废弃

| 参数 | 描述 | required |
| --- | --- | --- |
| jwt | jwt字符串 | Y |


#### 返回 

成功时  
http status code : 200

```json
{
  "c": "场景值 1=有效 2=无效"
}
```

请求失败  
http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```


获取公告内容
---------------------

```http
GET /api/watermelon/announcement
```

成功时  
http status code : 200

```json
{
  "announcement": "这是公告概览",
  "url": "这是公告文章地址"
}
```


获取首页轮播图内容
---------------------

```http
GET /api/watermelon/slide_show
```

成功时  
http status code : 200

```json
{
  "slide_show_list": [
      {
        "id": "文章id",
        "image_url": "图片地址",
        "advertorial_url": "文章地址",
        "title": "文章标题",
        "remarks": "文章备注",
        "created_at": "2019-07-06T13:16:07+08:00"
      },
      {
        "id": 1,
        "image_url": "http://mmbiz.qpic.cn/mmbiz_jpg/DhU6iaOJgOmg7IQxcJiacib3uaVZJoCP0dicB2RQicnmW866ApIYtQQGK9ttkQcB27xuzZOqqNVuyG8lLfhcUFEic9ibQ/0?wx_fmt=jpeg",
        "advertorial_url": "https://mp.weixin.qq.com/s/OTLamS0Y7OMLd1WMjh7Exg",
        "title": "清华北大复旦招生宣传片曝光，看完哭了，孩子不想读书就给他看",
        "remarks": "测试1",
        "created_at": "2019-07-06T13:14:47+08:00"
      }
  ]
}
```

获取信息流tab
-----------------------------

```http
GET /api/watermelon/article_tabs
```

#### 成功时

http status code : 200

```json
{
  "tabs": [
    {
      "id": 1,
      "title": "新闻"
    },
    {
      "id": 2,
      "title": "时事"
    }
  ]
}
```


获取文章信息流
------------------------------

```http
GET /api/watermelon/article_flow
```

| 参数 | 描述 | required |
| --- | --- | --- |
| tab_id | tab ID | Y |
| page | 分页页码，从1开始，每页限定返回10条记录 | Y |


#### 成功时  
http status code : 200

```json
{
  "articles": [
    {
      "id": "文章id",
      "article_title": "文章标题",
      "article_url": "文章地址",
      "article_image_url": "文章图片url",
      "article_outline": "文章概要",
      "page_view_count": "文章阅读量",
      "article_tab_id": "文章所属tab的ID",
    },
    {
    
    }
  ]
}
```


自增文章阅读量
-------------------------------

```http
POST /api/watermelon/article_page_view
```

| 参数 | 描述 | required |
| --- | --- | --- |
| article_id | 文章id | Y |

#### 成功时

http status code : 200

```json
{
  "page_view_count": "自增之后的阅读量"
}
```

#### 失败时

http status code : 400  

```json
{
  "code": "失败场景值",
  "message": "失败提示信息"
}
```


广告免除
-----------------------------

此接口需要带上jwt

### 创建免除记录

```http
POST /api/watermelon/ad_exemption
```

| 参数 | 描述 | required |
| --- | --- | --- |
| exempt_until | 该用户广告免除有效的时间点，必须为整数的时间戳 | Y |


#### 成功时

http status code : 200

```json
{
  "user_id": 1,
  "exempt_until": 123456   // 时间戳
}
```

### 获取免除记录

```http
GET /api/watermelon/ad_exemption
```

#### 成功时

http status code : 200

```json
{
  "user_id": 1,
  "exempt_until": 123456   // 该用户能够免除广告的有效时间戳，当用户不存在记录时，本时间戳为0
}
```

pdd.ddk查询接口
------------------------------

此接口不需要jwt  

pdd.ddk接口文档：https://open.pinduoduo.com/#/apidocument/port?portId=pdd.ddk.cms.prom.url.generate  

* 此接口提供了ddk接口通用参数的处理，包括client_id, data_type, timestamp, sign  

```http
GET /api/watermelon/pdd/ddk/query
```

| 参数 | 描述 | required |
| --- | --- | --- |
| type | ddk的接口类型，如 pdd.ddk.top.goods.list.query | Y |
| * | 业务参数，根据type的不同需要不同的业务参数 | Y |

### 返回

详见pdd.ddk接口文档



