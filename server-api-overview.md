# 服务端API接入概述

### 阅读对象

服务端API开发文档主要面向游戏开发商的服务端开发人员，向他们提供集成指南。

### 基本介绍

提供了服务端到服务端的安全通信，基于 secure socket layer(SSL)安全协议和 Representational State Transfer (RESTful)架构，数据组织采用 JSON 协议。

### 数据安全

- Secure Sockets Layer (SSL) 数据传输

游戏商 和 发行商 之间所有网络通讯都采用 HTTPS 协议，防止通讯过程中数据泄密或者被篡改，确保数据传输的安全。[http://en.wikipedia.org/wiki/HTTP_Secure](http://en.wikipedia.org/wiki/HTTP_Secure)

- 数据签名 (Signature)

在应用层为了保证数据的完整性和安全，游戏商 和 发行商之间发送数据时需要对发送的数据进行签名，接收数据时需要对接收的数据进行签名验证。具体参见”签名规则“。(Required)

- IP 地址过滤

发行商 需要向 游戏商 提供服务器的静态 IP 地址。游戏商会校验请求的IP地址。(Optional)

### 集成准备工作

发行商将会供以下两组帐户信息供游戏商在集成时使用

|参数名|说明|
|---|---|
|appId|分配给游戏商游戏ID。等同于clientId|
|appSecret|分配给游戏商游戏的加密钥匙。等同于clientSecret|

### 返回结果(示例)

```json
// 成功示例
{
    "resultCode": 200,
    "message": "OK",
    "data": []
}
// 失败示例
{
    "resultCode": 40001,
    "message": "Invalid parameter",
    "data": []
}
```

### 错误码

|状态码|Response Body Message 说明|
|---|---|
|200|表示访问成功|
|400|表示访问失败，API 服务端拒绝了访问请求，原因可能是请求的数据格式不正确、缺少需要的字段或某些字段参数值有问题|
|401|表示没有授权，请求没有得到 API 认证机制授权，比如：发行商发起请求签名验证失败，表示访问失败|
|404|表示请求的资源并不存在|
|500|服务器内部错误|

|状态码|错误码|Response Body Message 说明|
|---|---|---|
|401|40101|无效的签名|
|400|40001|无效的参数|
|400|40002|登录已过期|
|404|40401|账户未找到|
|404|40404|应用未找到|

### 签名规则

#### 介绍

发行商与游戏商之间为了保证数据的完整性和安全需要对数据进行签名和签名验证。

- 发送数据时需要对发送的数据进行签名，生成签名字符串，签名字符串作为 signature 参数的值一并发送。
- 接收数据时需要对接收的 signature 值进行签名验证，以确保数据的完整和安全。

#### 生成

- 数据签名是一个 MD5 字符串，将发送的数据的参数名与参数值按一定顺序拼接起来生成字符串 A，字符串 A 再和 Secret 拼接得到字符串 B，对字符串 B 进行 MD5 后得到数据签名字符串。
- appSecret（等同于clientSecret） 是由发行商指定。
- 所有的参数都要参与排序。
- 对所有的参数排序后(参数名按照字母顺序进行排序)，根据顺序依次取出参数名与参数值进行拼接生成字符串 A。
- 用于生成字符串 A 的所有参数值必须是原始值。
- 用于生成字符串 A 的所有参数值区分大小写。
- 所有字符串的前后都没有空格。

#### 举例

```
appSecret: a5e283b0b4267f3dc9c36203eaf88cae
```

|参数名|参数值|
|---|---|
|account|100000|
|serverId|1|
|roleId|2|

第一步: 对所有的参数按照参数名的字母顺序进行排序。

```
{ account } & { roleId } & { serverId }
```

第二步: 对排序好的参数,依次取出参数名和参数值拼接成字符串 A。

```
account=100000&roleId=2&serverId=1
```

第三步: 字符串 A 再拼接 appSecret 生成字符串 B。

```
account=100000&roleId=2&serverId=1a5e283b0b4267f3dc9c36203eaf88cae
```

第四步: 对字符串 B 进行 MD5 得到数据签名字符串。

```
md5('account=100000&roleId=2&serverId=1a5e283b0b4267f3dc9c36203eaf88cae')
```

第五步: 在发送的数据中加上生成的数据签名参数 signature。

```
account=100000&roleId=2&serverId=1&signature=e1c57831ca7bc17fda7814195f36e548
```
