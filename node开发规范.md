# node开发规范

## 命名方式

### 接口命名

* 路由配置 - /[项目]/[模块]/[功能]

```
/app/account/forget.do
/admin/account/forget.do
```

* 命名原则 - 尽可能简洁, 如果一个单词能够表达意义的, 不用再加多一个单词

```
/app/account/forgetPwd.do  这里其实可以用 forget 来代替, 因为 forget 已经能表达出意义, 除非出现了歧义, 如:
/app/account/userList.do 与 /app/account/vipUserList.do 这种, 如果整个模块下只有一个列表, 那么应该用:
/app/account/list.do
```

### 变量命名

* 驼峰命名 - 多个单词链接是采用 `小驼峰` , 以动词加名词组合使用, 动词在前

* 关于像 `match` `update` `option` 等数据库操作相关的变量, 应该采用 在其后加上 `表名`

```
var matchAccount = {};
var matchUser = {};
var updateAccount = {};
var updateUser = {};
var optionAccount = {};
var optionUser = {};
```

* 在 `Promise` 回调中关于变量的命名

```
find 和 findOne 类型的 , 在回调函数中 用 `_` + `表名` 来命名返回值变量, 如:
User.findOneAsync(matchAccount).then(function(_User) {})
User.findAsync(matchAccount).then(function(_User) {})
update 和 Promise.all 类型的, 在回调函数中 用 `result` 来命名返回值变量, 如:
User.updateAsync({}).then(function(result) {})
Promise.all(task).then(function(result) {}) 这里result是一个数组, 就算里面放的是findOne 也是返回一个数组
count 类型的, 在回调函数中 用 `count` 来命名返回值变量, 如:
Article.countAsync(matchArticle).then(function(count) {})
```

## Promise.all 用法

```
var task = [];
task.push(Article.countAsync(matchArticle));
task.push(Article.findAsync(matchArticle, '', optionArticle));
Promise.all(task).then(function(result) {})
注意: count操作放在数组的第一条, 列表操作放在第二条
```

## req.log 用法

* req.log(desc, data) 
* desc - 文字描述
* data - 打印数据


```
req.log('banner.task err', err);
$:> 1501231234 cost(ms):123 post /admin/user/edit banner.task err:----> null
```