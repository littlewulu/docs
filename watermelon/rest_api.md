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
* [登录](#登录)
* [校验登录态 - 废弃](#校验登录态)
* [获取公告内容](#获取公告内容)
* [获取首页轮播图内容](#获取首页轮播图内容)



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

