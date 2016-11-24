Logline
=======

[![Build Status][travis-image]][travis-url]

logline是一个轻量，实用和客户端级的前端客户端日志记录工具。


为何前端定位问题很困难
-----------------
前端同学对此肯定深有体会，代码发出去之后，犹如脱缰的野马，运行在万千的客户终端上，等到产品和后台反馈问题到我们这边，很多时候定位问题只能靠猜，尤其是一些偶发诱因，因为根本不知道用户是如何操作的，真实环境遇到的问题通常是很多随机因素叠加的形成的，因此很难回放用户的操作来还原现场找到原因。这个时候，我们想，如果有一个像后台一样详实的可分类和检索的运行日志，无疑将会提供巨大的帮助。

应用场景
------

+ 核心流程监控

    在产品的一些核心流程中，我们可以在用户出错的情况下主动上传用户日志，以便我们可以快速统计和定位用户遇到的问题。

+ 主动抓取用户的日志分析用户行为

    有时候在用户不配合开发人员的时候，我们可以设计一种策略，比如我们在线上发布一个json文件，里面配置一个希望主动抓取日志的用户列表，当我们的产品在用户手机上被打开后，延时下载（避免影响主流程性能）这个json，当匹配当前用户时，直接主动上报该用户的日志。

+ 统计和辅助分析JS错误

    我们可以记录js的报错，包含调用队列一起记录，直接上传此错误日志或者在累计达到一个阈值的时候统一上传。

特性支持
------

+ 基本的日志记录功能
+ 命名空间支持多个模块同时写日志
+ 支持websql/localstorage/indexeddb三种web客户端持久化存储方案
+ 日志等级
+ 日志清理（防止日志过多，占用上传带宽和占满用户允许的内存）
+ 日志上传（需后端提供接口支持接收）
+ 无外部依赖
+ 用户级，针对单个用户的分析

快速上手
------

### 1. 引入脚本，并选择期望使用的协议

目前一共支持三个协议， 三个协议都被直接挂载在Logline对象上以便一些特殊的应用场景，也更好的符合语义化:

+ websql: Logline.PROTOCOL.WEBSQL
+ indexeddb: Logline.PROTOCOL.INDEXEDDB
+ localstorage: Logline.PROTOCOL.LOCALSTORAGE

``` javascript
// AMD/CMD
// 使用websql协议
var Logline = require('./mod/logline.min');
Logline.using(Logline.PROTOCOL.WEBSQL);

// Script Tag
<script src="./mod/logline.min.js"></script>
<script>
	// 使用indexedDB协议
	Logline.using(Logline.PROTOCOL.INDEXEDDB);
</script>
```

### 2. 清理日志

``` javascript
Logline.keep(.5); // 保留半天以内的日志，如果不传参则清空日志
Logline.clean(); // 清空日志并删除数据库
```

### 3. 记录日志

``` javascript
// 不同的模块使用不同的日志会话
var spaLog = new Logline('spa'),
	sdkLog = new Logline('sdk');

// 不包含数据的，描述为 init.succeed 的记录
spaLog.info('init.succeed');

// 包含错误描述数据，描述为 init.failed 的记录
spaLog.error('init.failed', {
	retcode: 'EINIT',
	retmsg: 'invalid signature'
});

// 不包含数据的，描述为 outdated 的记录
sdkLog.warning('outdated');

// 包含错误描述数据，描述为 system.vanish 的记录
sdkLog.critical('system.vanish', {
    // debug infos here
});
```

### 4. 读取日志

``` javascript
Logline.getAll(function(logs) {
    // process logs here
});
```


自定义构建
--------
目前Logline一共实现了`localstorage`、`websql`和`indexeddb`三个日志协议，默认是全部打包，可能你只想使用其中某个协议而已，你可以通过`npm run configure`来自定义构建你需要的版本。这样有利于减小包的大小。

``` shell
// 不跟参数默认构建所有协议
npm run configure
// 配置你需要的协议，去掉不需要的协议申明--with-xxx
npm run configure -- --with-localstorage --with-websql --with-indexeddb
// 重新打包
npm run build
// 去dist目录寻找新构建的打包文件
```


FAQ
----

### 如何上传日志？
从v1.0.1以开始，日志上传功能被移除，我们希望logline更专注于日志的记录和维护工作，你可以通过`Logline.getAll`来获取日志来自行实现上传过程。

### 如何分析日志
-------------
由于Logline上传的日志格式符合标准，具有良好的可阅读性，因此我们可以在某种程度上直接使用命令行工具或者编辑器来阅读。
但是对命令行不熟悉的用户使用可能仍然有困难，因此有必要使用Web技术栈搭建一个易于使用并且视觉良好的工具。

我们为此准备了[Logline-viewer]以供使用。


他们都在用
---------
![腾讯微证券](https://wzq.tenpay.com/weixin/v1/pic/logo/common.png)



[travis-image]: https://api.travis-ci.org/latel/logline.svg
[travis-url]: https://travis-ci.org/latel/logline
[logline-viewer]: https://github.com/latel/logline-viewer
[logline-uploader]: https://github.com/latel/logline-uploader
