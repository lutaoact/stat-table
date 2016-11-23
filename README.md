# stat-table
服务器端用户留存统计的简易框架

模块暂时只支持日留存统计，后续可能会加入周留存率的计算。如果用户数庞大，建议将留存数据放在单独的redis服务器上，减少生成统计数据过程中可能对业务服务器造成的冲击。

## 依赖环境
```
node   v4.x
redis  v3.2.x
```

## 示例
__使用模块__

导出对象供使用，写在一个公用文件中，比如`common/stat.js`
```js
const StatTable = require('stat-table');
const stat = new StatTable({host, port, db});//es6的写法，连接redis的配置信息
module.exports = stat;
```

__注入代码__

在注册和登录的地方分别调用相关的记录函数
```js
stat.recordRegister(userId);//注册
stat.recordLogin(userId);//登录
```

__每日统计__

设置一个定时任务，在每天23点55分左右执行以下函数，date为可选的日期字符串，格式为YYYYMMDD，不提供该参数时默认取当天
```js
stat.genMultiDayRetention(date, (err) => {});
```

__导出结果__

导出文件为csv格式，dirPath为文件路径；days是希望导出的最长多少天的留存率。例如，days为10，则会导出注册日之后10天的登录留存情况。
```js
stat.exportRetention(dirPath, days, (err) => {});
```

__结果格式__

![导出结果](http://7xsgzh.com1.z0.glb.clouddn.com/QQ20161123-1.jpg)

## 其它功能

```js
//获取在指定日期注册，并在指定天数之后的留存用户id，baseDate为指定日期，格式为YYYYMMDD，afterNum为指定的天数
stat.getRetentionUserIds(baseDate, afterNum, (err, userIds) => {});

//获取在指定日期注册的用户id，baseDate为指定日期，格式为YYYYMMDD
stat.getRegisterUserIds(baseDate, (err, userIds) => {});

//获取在指定日期登录的用户id，baseDate为指定日期，格式为YYYYMMDD
stat.getLoginUserIds(baseDate, (err, userIds) => {});
```
