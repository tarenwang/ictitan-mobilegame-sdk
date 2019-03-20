# 获取游戏区服列表

### 接口说明

当玩家在游戏官网登入我方系统时，可以选择在线充值(第三方支付)，因此需要开发商提供2个API。第一个是获取游戏区服列表API，第二个是获取游戏角色列表API。

有些玩家付费习惯喜欢在游戏官网充值，这个占比大概为5-10%，虽然占比不高，但是充值的额度是没有限制的。所以一些大R的玩家会习惯于使用游戏官网充值。

### 请求地址

游戏商自定并告知发行商 [http://yourdomain.com/server.php](http://yourdomain.com/server.php)

### 请求方式(发行商服务器请求游戏商服务器)

GET

### 请求参数

|字段名|是否必填|类型|说明|
|---|---|---|---|
|time|是|int|10位时间戳|
|signature|是|string|签名 [参考签名规则](server-api-overview.md#签名规则)|

### 返回结果JSON

```json
// 成功示例
{
    "resultCode": 200,
    "message": "OK",
    "data": [
            {
                "id":1,
                "name":"S1 Anime arena"
            },
            {
                "id":2,
                "name":"S2 High arena"
            }
            {
                "id":3,
                "name":"S3 Mange arena"
            }
        ]
}
// 失败示例
{
    "resultCode": 40101,
    "message": "签名错误",// 具体错误信息游戏商自定义，建议详细。
    "data": []
}
```

### 返回结果字段说明

|字段名|类型|说明|
|---|---|---|
|id|string|游戏区服ID|
|name|string|游戏区服名称|
