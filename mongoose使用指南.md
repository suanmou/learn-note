# Mongoose 使用指南

## 查找操作中的字段

### 比较类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$eq | = | $ne | !=
$gt | > | $gte | >=
$lt | < | $lte | <=
$in | 匹配指定数组内 | $nin | 匹配指定数组外

### 逻辑类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$or | 或者 | $and | 并且
$not | 返回不匹配 | $nor | 返回多个不匹配

### 元素类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$exists | 字段存在 | $type | 根据数据类型

### 评估类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$mod | 求余数 [a, b] | $regex | 正则匹配
$text | 语言语法匹配 | $where | js字符串或者函数

### 数组类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$all | 满足所有 | $elemMatch | 在数组中存在
$size | 数组长度 | - | -

### 地理类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$geoWithin | - | $geoIntersects | -
$near | - | $nearSphere | -

内部字段

* $geometry - Specifies a geometry in GeoJSON format to geospatial query operators.
* $minDistance - Specifies a minimum distance to limit the results of $near and $nearSphere queries. For use with 2dsphere index only.
* $maxDistance - Specifies a maximum distance to limit the results of $near and $nearSphere queries. The 2dsphere and 2d indexes support $centerSphere.
* $center - Specifies a circle using legacy coordinate pairs to $geoWithin queries when using planar geometry. The 2d index supports $center.
* $centerSphere - Specifies a circle using either legacy coordinate pairs or GeoJSON format for $geoWithin queries when using spherical geometry. The 2dsphere and 2d indexes support $centerSphere.
* $box - Specifies a rectangular box using legacy coordinate pairs for $geoWithin queries. The 2d index supports $box.
* $polygon - Specifies a polygon to using legacy coordinate pairs for $geoWithin queries. The 2d index supports $center.
* $uniqueDocs - Deprecated. Modifies a $geoWithin and $near queries to ensure that even if a document matches the query multiple times, the query returns the document once.

## 更新操作中的字段

### 属性类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$inc | 加等于 += | $mul | 乘等于 *=
$rename | 重命名字段 | $setOnInsert | 插入时设置
$set | 设置字段,可用于对象的更新 | $unset | 删除字段
$min | 设置比较后小值 | $max | 设置比较后大值
$currentDate | 设置当前时间,可以选择格式类型 | - | -

### 数组类型字段

字段 | 描述 | 字段 | 描述
- | - | - | -
$ | 用于更新数组中的某个item,用作占位符,代表该item的下标 | - | -
$pop | 移除数组开头或者结尾 | $addToSet | 添加到集合,重复的忽略
$pullAll | 移除所有给出值 | $pull | 根据查找结果来移除
$pushAll | 添加所有给出值(废弃) | $push | 添加所有给出值,四种用法
$each | 每个添加 | $slice | 截取
$sort | 排序 | $position | 按下标位置添加

```
Friend.updateAsync({name: name},{$push: {group：{groupName:"friends"}}})
Friend.updateAsync({name: name},{$set: {'user.$.username':'一回'}}) -- 其中$用具体的下表来表示，例如
Friend.updateAsync({name: name},{$set: {'user.1.username': '一回'}}) --代表数组第2项

```
## 查找时使用populate查找外链静态表

```
var optionXXX = {
    select: 'type data',
    sort: '_id',
    populate: {
        path: 'data.user data.group',
        model: 'User Group',
        select: 'loginName name type'
    }
};
```

## 查找多个结果集合

```
var task = [];
task.push(Option.findAsync(matchOption, '', optionOption));
task.push(Option.countAsync(matchOption));
Promise.all(task).then(function (result) {
    var totalCount = result[1];
    Vue.render({
        template: '/admin/option/list-V.html',
        data: {list: result[0]}
    }, function (table) {
        res.render('admin/option/list', {
            table: table,
            totalCount: totalCount,
            currentPage: searchPage,
            totalPages: Math.ceil(totalCount / pageSize)
        });
    });
}).catch(function (err) {
    console.log('/admin/option/list find err:---->', err);
});
```


## 列表分页以及条件查找

```
var reqData = req.query;
var searchPage = reqData.page || 1;
var pageSize = 20;
// 普通列表
var matchOption = {
    delFlag: 2
};
var optionOption = {
    select: '',
    sort: '-addDate',
    limit: pageSize,
    skip: pageSize * (searchPage - 1)
};
// 搜索查找
// if (reqData.company) matchOption['company'] = new RegExp(reqData.company, 'i');
// if (reqData.startTime) matchOption['addDate'] = _.extend({}, matchOption['addDate'], {"$gt": reqData.startTime});
// if (reqData.endTime) matchOption['addDate'] = _.extend({}, matchOption['addDate'], {"$lt": reqData.endTime});
console.log('/admin/option/list find match:---->', matchOption);
var task = [];
task.push(Option.findAsync(matchOption, '', optionOption));
task.push(Option.countAsync(matchOption));
Promise.all(task).then(function (result) {
    var totalCount = result[1];
    Vue.render({
        template: '/admin/option/list-V.html',
        data: {list: result[0]}
    }, function (table) {
        res.render('admin/option/list', {
            table: table,
            totalCount: totalCount,
            currentPage: searchPage,
            totalPages: Math.ceil(totalCount / pageSize)
        });
    });
}).catch(function (err) {
    console.log('/admin/option/list find err:---->', err);
});
```

## 查找数组字段中存在匹配值

data:

```
{
	size: [1,2,3,4]
}
```

code:

```
find({
    size: {
        $elemMatch: {
            $eq: 4
        }
    }
})
```

## 更新操作

### 更新数组中某个item

```
students.update(
   { _id: 1, grades: 80 },
   { $set: { "grades.$" : 82 } }
)
students.update(
   { <query selector> },
   { <update operator>: { "array.$.field" : value } }
)
```

## 批量插入

### 快速批量

```
User.insertManyAsync(array).then(function (result) {
    // TODO
}).catch(function (err) {
    console.log(err)
});
```

### 普通批量

```
var array = [{ type: 'jelly bean' }, { type: 'snickers' }];
Candy.createAsync(array).then(function (result) {
	// TODO
}).catch(function (err) {
    console.log(err)
});
```

## 聚合用法

### 管道查询, 每次结果作为下次查询的内容

```
Transaction.aggregateAsync([{
    // 匹配
    "$match": {
        "type": 1,
        "delFlag": 2
    }
}, {
    // 过滤字段
    "$project": {
        user: "$data.user",
        group: "$data.group"
    }
}, {
    // 关联查询, 类似关系数据库, from表, localField当前字段, foreignField外键, as生成字段到当前的管道
    $lookup: {
        from: "users",
        localField: "user",
        foreignField: "_id",
        as: "user"
    }
}, {
    $lookup: {
        from: "groups",
        localField: "group",
        foreignField: "_id",
        as: "group"
    }
}, {
    // $lookup找到的都是数据, 就算只有一项, 用$unwind解开数组
    $unwind: {
        path: "$user"
    }
}, {
    $unwind: {
        path: "$group"
    }
}, {
    // $lookup找到的是全数据, 需要手动过滤字段
    "$project": {
        "user": {
            _id: "$user._id",
            name: "$user.name"
        },
        "group": {
            _id: "$group._id",
            name: "$group.name"
        }
    }
}, {
    // 分组聚合
    $group: {
        _id: "$user._id",
        user: {
            $first: "$user"
        },
        group: {
            $push: "$group"
        },
        count: {
            $sum: 1
        }
    }
}, {
    // 排序
    $sort: {
        "user._id": 1
    }
}])
```

## 报错异常

### schema从未被注册

```
MissingSchemaError: Schema hasn't been registered for model "Storehouse".
```

应该在引用的时候引入模块

## 备份数据库

### 备份

mongodump -h IP --port 端口 -u 用户名 -p 密码 -d 数据库 -o 文件存在路径

--authenticationDatabase  参数制定认证数据库   否则会提示错误：
Failed: error connecting to db server: server returned error on SASL authentication step: Authentication failed.   //提示认证失败

```
mongodump -h 127.0.0.1 --port 27017 -u seastore -p seastore2016 -d seastore -o '/data/LRKJ-CMS.com/seastore_20170305152727' --authenticationDatabase seastore
```

### 导入

--drop代表如果有了temple数据库则将其中的所有集合删除，不指定就会和原来temple中的集合合并


```
mongorestore -h 127.0.0.1 --port 27017 -u seastore -p seastore2016 -d seastore /data/LRKJ-CMS.com/LRKJ-CMS/seastore_20170305152727/seastore --authenticationDatabase seastore
```