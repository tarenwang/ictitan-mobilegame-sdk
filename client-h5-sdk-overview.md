# H5 SDK 接入

**修改记录**

| 修订号   | 修改描述       | 修改日期       |
| ----- | -------- |  ---------- |
| 1.0.0 | 初稿完成       | 2020-06-17 |
| 1.0.1 | 事件回调中登录返回参数修改       | 2020-06-19 |

本文为H5手游接入本SDK的使用教程，只涉及SDK的使用方法，默认游戏研发商已经熟悉游戏IDE的基本使用方法，以及具有相应的Javascript编程知识基础等。

**游戏研发商只需要根据此文档完成PC下网页端的母包渠道测试就可以，具体H5游戏的生成壳包过程，由发行商来操作。**

### 准备阶段

- 需要先将游戏内的可购买商品列表发送给发行商，物品属性包含物品id、物品价格、物品在游戏中对应的点数、物品描述等
- 发行商向开发商提供`appId`、`appSecret`、`channelId`、`sdkDomain`、`assetCDN`等配置信息

#### 配置说明

|参数名|说明|
|---|---|
|appId|发行聚合SDK分配的应用唯一ID|
|appSecret|发行聚合SDK针对appId分配的唯一密钥，用于服务端登录验证和内购商品付款通知时的验签|
|channelId|渠道ID，母包测试时请使用`19001`|
|sdkDomain|聚合SDK的服务端API所部署的域名|
|assetCDN|H5 SDK相关文件存储的CDN地址，母包测试时可为空|
|js|H5 SDK加载的渠道适配js文件的文件名，母包测试时请使用`tong_h5`|

### 一、游戏配置

#### 游戏主体html的请求参数配置

在接入大力神H5手游SDK的游戏主体网页中，需要获取到网页请求URL里的GET参数，作为参数配置传递到SDK中。参数说明如下：

|参数名|是否必要|说明|
|---|---|---|
|type|是|渠道类型，母包测试时固定为``|
|app|是|等于appId|
|channel|是|等于channelId|
|cps|否|CPS渠道的ID，母包测试时可为空|
|pkg|是|包名。Android里的package、iOS里的bundle id；母包测试时可随意定义|
|device|是|设备号，母包测试时可为0|
|js|是|需要加载的渠道SDK适配文件名，母包测试时请使用`tong_h5`|

#### SDK文件的引入

在游戏主体html中引入`ictitansdk.min.js`文件，具体引入代码如下：

```html
<!-- 具体src请根据html与sdk文件的相对地址决定 -->
<script src="./sdk/ictitansdk.min.js"></script>
```

或者引入在线测试地址

```html
<script src="http://asset.tarenwang.net/ictitan/sdk/ictitansdk.min.js"></script>
```

### 二、聚合SDK接入说明

以下程序均只提供Javascript的对接方式，如果游戏主要开发语言为Typescript或者Actionscript，请自行转义。

#### 2.1 SDK事件回调(必接)

```javascript
// 监听所有SDK回调事件
window.addEventListener(ICT_CONST.EVENT_NAME, function(e) {
    switch (e.action) {
        case ICT_EVENT_ACTION.ACTION_INIT_SUCCESS: // 初始化成功
            console.log("IctitanSDK.init初始化成功, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_INIT_FAIL: // 初始化失败
            console.log("IctitanSDK.init初始化失败, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_LOGIN_SUCCESS: // 登录成功
            var user = e.data;
            var user_info = 'accountId=' + user.accountId + ","  // 帐号ID
                + 'appId=' + user.appId + ","  // 应用ID
                + 'channelId=' + user.channelId + ","  // 渠道ID
                + 'token=' + user.token + ","  // 帐号登录token
                + 'nickname=' + user.nickname + ","  // 昵称
                + 'avatarUrl=' + user.avatarUrl + ","  // 头像地址
                + 'type=' + user.type;  // 帐号类型(facebook,google,apple,guest,amazon)
            console.log("IctitanSDK.login登录成功, code=" + e.code + ", msg=" + e.msg);
            console.log("帐号信息：" + user_info);
            break;
        case ICT_EVENT_ACTION.ACTION_LOGIN_FAIL: // 登录失败
            console.log("IctitanSDK.login登录失败, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_LOGIN_CANCEL: // 登录取消
            console.log("IctitanSDK.login登录取消, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_LOGOUT_SUCCESS: // 注销成功
            console.log("IctitanSDK.logout注销成功, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_LOGOUT_FAIL: // 注销失败
            console.log("IctitanSDK.logout注销失败, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_PAY_SUCCESS: // 支付成功
            console.log("IctitanSDK.pay支付成功, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_PAY_FAIL: // 支付失败
            console.log("IctitanSDK.pay支付失败, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_PAY_CANCEL: // 支付取消
            console.log("IctitanSDK.pay支付取消, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_SHARE_SUCCESS: // 分享成功
            console.log("IctitanSDK.share分享成功, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_SHARE_FAIL: // 分享失败
            console.log("IctitanSDK.share分享失败, code=" + e.code + ", msg=" + e.msg);
            break;
        case ICT_EVENT_ACTION.ACTION_SHARE_CANCEL: // 分享取消
            console.log("IctitanSDK.share分享取消, code=" + e.code + ", msg=" + e.msg);
            break;
        default:
            console.log("未知回调事件, action=" + e.action);
            break;
    }
});
```

#### 2.2 初始化(必接)

```javascript
var config = {
    'sdkDomain' : 'xxx.api.usdk.ictitan.com',
    'assetCDN' : '',
}

IctitanSDK.init(config);
```

#### 2.3 登录(必接)

```javascript
IctitanSDK.login();
```

#### 2.4 游戏服角色信息上报(必接)

<span style="color:red;">当游戏需要进行后续的充值操作，必须先上报角色信息</span>

```javascript
var roleInfo = {
    "serverId" : "玩家所选服务器ID",
    "serverName" : "玩家所选服务器名字",
    "roleId" : "创建的角色ID",
    "roleName" : "创建的角色名字",
    "profession" : "角色对应职业",
    "level" : "角色等级",
    "type" : ICT_ROLE_EVENT_TYPE.TYPE_CREATE,
    /*
    type的可选值如下：
    ICT_ROLE_EVENT_TYPE.TYPE_CREATE  创角
    ICT_ROLE_EVENT_TYPE.TYPE_ENTER_GAME  进入游戏
    ICT_ROLE_EVENT_TYPE.TYPE_LEVEL_UPGRADE  角色升级
    */
};

IctitanSDK.reportRoleInfo(roleInfo);
```

#### 2.5 支付(必接)

```javascript
var paymentInfo = {
    "productId" : "游戏中商品ID",
    "description" : "商品描述",
    "amount" : 35.00,  // 价格，请用2位小数的浮点数
    "currency" : "THB",  // 货币类型
    "extra" : "支付成功时原样返回至游戏服务器的额外参数",
};

IctitanSDK.pay(paymentInfo);
```

#### 2.6 注销/切换帐号(必接)

```javascript
IctitanSDK.logout();
```

#### 2.7 事件打点通用接口(必接)

```javascript
var eventParams = {
    "level" : 20,
    "utype" : "task",
}

// UpgradeLevel 为事件名称，请参考运营对接文档
IctitanSDK.trackEvent("UpgradeLevel", eventParams);
```

#### 2.8 社交分享(选接)

```javascript
var shareId = "发行商平台分配的分享id";
var shareParams = {
    "displayName" : "你好啊",
}

IctitanSDK.shareToSocialNetwork(shareId, shareParams);
```

#### 2.9 显示用户中心(选接)

```javascript
IctitanSDK.showUserCenter();
```

#### 2.10 悬浮球菜单的显示/隐藏(选接)

```javascript
IctitanSDK.showOrHiddenFloatMenu(true); // 显示
// 或者
IctitanSDK.showOrHiddenFloatMenu(false); // 隐藏
```
