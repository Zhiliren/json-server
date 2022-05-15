# 一、json-server环境搭建

​		一个在前端本地运行，可以存储json数据的server。

​		通俗来说，就是模拟服务器接口数据，一般用在前后端分离后，前端人员可以不依赖API开发，而在本地搭建一个JSON服务，自己产生测试数据。

​		顾名思义，json-server就是个存储json数据的server

## 下载安装

使用npm全局安装json-server：

```shell
npm install -g json-server
```

可以通过查看版本号，来测试是否安装成功：

```shell
json-server -v
```

## 创建json数据——db.json

既然是造数据，就需要创建一个json数据。

在任意一个文件夹下（此处假设我创建一个myserver文件夹），进入到该文件夹里面，执行代码：

```shell
json-server --watch db.json
```

**db.json里自带的数据**

```json
{
    "posts": [
        { "id": 1, 
        "title": "json-server", 
        "author": "typicode" 
        }
    ],
    "comments": [
        { "id": 1, 
        "body": "some comment", 
        "postId": 1 
        }
    ],
    "profile": { 
    	"name": "typicode" 
    }
}
```

再比对myserver/db.json/文件的数据，就可以发现：`/db` 就是整个的db.json数据包，而 `/posts` `/comments` `/profile` 分别是db.json里面的子对象。

**所以说，json-server把db.json根节点的每一个key,当作了一个router。我们可以根据这个规则来编写测试数据。**

## 修改端口号

json-server 默认是 3000 端口，我们也可以自己指定端口，指令如下：

```shell
json-server --watch db.json --port 3004
```

如果你很懒，觉得启动服务器的这段代码有点长，还可以考虑db.json同级文件夹（也就是myservec文件夹）新建一个package.json，把配置信息写在里头：

```json
{
    "scripts": {
        "mock": "json-server db.json --port 3004"
    }
}
```

之后启动服务，只需要执行以下指令即可：

```shell
npm run mock
```

## json-server 的相关启动参数

- 语法：`json-server  [options] <source>`
- 选项列表：

| 参数              | 简写 | 默认值                                             | 说明                             |
| ----------------- | ---- | -------------------------------------------------- | -------------------------------- |
| --config          | -c   | 指定配置文件                                       | [默认值:“json-server。json”]     |
| --port            | -p   | 设置端口 [默认值：3000]                            | Number                           |
| --host            | -H   | 设置域 [默认值：“0.0.0.0”]                         | String                           |
| --watch           | -w   | Watch file(s)                                      | 是否监听                         |
| --routes          | -r   | 指定自定义路由                                     |                                  |
| --middlewares     | -m   | 指定中间件 files                                   | [数组]                           |
| --static          | -s   | Set static files directory                         | 静态目录,类比：express的静态目录 |
| --readonly        | --ro | Allow only GET request [布尔值]                    |                                  |
| --nocors          | --nc | Disable Cross-Origin Resource Sharing [布尔值]     |                                  |
| --no              | gzip | , --ng Disable GZIP Content-Encoding [布尔值]      |                                  |
| --snapshots       | -S   | Set snapshots directory [默认值：“.”]              |                                  |
| --delay           | -d   | Add delay to responses (ms)                        |                                  |
| --id              | -i   | Set database id property （e.g _id）[默认值：“id”] |                                  |
| --foreignKeySuffx | --   | fks Set foreign key suffx (e.g _id as in post_id)  | [默认值：“”id]                   |
| --help            | -h   | 显示帮助信息                                       | [布尔]                           |
| --version         | -v   | 显示版本号                                         | [布尔]                           |

# 二、操作数据

我们先自己倒腾下 db.json 数据，比如现在是一个水果商城，放点用户信息和水果价格信息：

```json
{
    "fruits": [{
            "id": 1,
            "name": "apple",
            "price": 33
        },
        {
            "id": 2,
            "name": "orange",
            "price": 8.88
        }
    ],
    "users": [{
        "name": {
            "useranme": "admin",
            "nickname": "dachui"
        },
        "pwd": "123456"
    }]

}
```

要注意，数据格式符合JSON格式（尤其注意最后一个键值对后面不要有逗号）。如果数据格式有误，命令窗口会报错，可以根据错误提示进行修整。

接下来我们就可以GET、POST、PUT、PATCH、or DELETE 方法来对数据进行操作。

## 数据获取

首先，我们先来看下GET操作。

浏览器地址访问就可以看做GET操作，所以不用写任何代码，我们就可以先来测试下 url-GET 操作。

### **常规获取**

```
http://localhost:3004/fruits
```

可以得到所有水果数据（对象数组）：

```json
[
    {
        "id": 1,
        "name": "apple",
        "price": 33
    },
    {
        "id": 2,
        "name": "orange",
        "price": 8.88
    }
]
```

### **过滤获取 Filter**

根据id获取数据

```
http://localhost:3004/fruits/1
```

可以得到指定id为1水果（对象）：

```json
{
    "id": 1,
    "name": "apple",
    "price": 33
}
```

当前，指定id为1的获取指令还可以用如下指令，但要注意，此时返回的数据是一个数组。

```
http://localhost:3004/fruits?id=1
```

```json
[
    {
        "id": 1,
        "name": "apple",
        "price": 33
    }
]
```

以此类推，我们也可以通过水果名称，或者是价格来获取数据：

```
http://localhost:3004/fruits?name=orange
```

```
[
   {
        "id": 2,
        "name": "orange",
        "price": 8.88
    }
]
```

也可以指定多个条件，用&符号（逻辑运算符）链接：

```
http://localhost:3004/fruits?name=orange&price=8.88
```

```
[
   {
        "id": 2,
        "name": "orange",
        "price": 8.88
    }
]
```

你甚至还可以使用对象取属性值obj.key的方式：

```
http://localhost:3004/users?name.nickname=dachui
```

```json
[
    {
        "name": {
        "useranme": "admin",
        "nickname": "dachui"
        },
        "pwd": "123456"
    }
]
```

以上看着是不是特别眼熟，不就是HTTP中GET请求方式嘛

### **分页 Paginate**

为了能演示分页效果，我们在db.json文件里fruits里面多添加了几种水果。

```json
{
"fruits": [{
            "id": 1,
            "name": "apple",
            "price": 33
        },
        {
            "id": 2,
            "name": "orange",
            "price": 8.88
        },
        {
            "id": 3,
            "name": "Banana",
            "price": 4.5
        },
        {
            "id": 4,
            "name": "watermelon",
            "price": 12
        }
    ],
}
```

编辑db.json（db.json数据有变动），都要关闭服务重新启动。（注意：不要用 CTRL + C 来停止服务，因为这种方式会导致node.js 依旧霸占3004端口，导致下一次启动失败。简单粗暴关闭窗口即可！——个人window系统，其他系统可能没有这样的烦恼。）

分页采用  `_page` 来设置页码，`_limit` 来控制每页显示条数。如果没有指定 `_limit` ，默认每页显示10条。

```
http://localhost:3004/fruits?_page=2&_limit=2
```

```json
[
    {
    "id": 3,
    "name": "Banana",
    "price": 4.5
    },
    {
    "id": 4,
    "name": "watermelon",
    "price": 12
    }
]
```

### 排序 Sort

排序采用 `_sort` 来指定要排序的字段，`_order` 来指定排序是正排序还是逆排序（asc | desc，默认是asc）。

```html
http://localhost:3004/fruits?_sort=id&_order=desc
```

```json
[
    {
    "id": 4,
    "name": "watermelon",
    "price": 12
    },
    {
    "id": 3,
    "name": "Banana",
    "price": 4.5
    },
    {
    "id": 2,
    "name": "orange",
    "price": 8.88
    },
    {
    "id": 1,
    "name": "apple",
    "price": 33
    }
]
```

也可以指定多个字段排序，一般是按照id进行排序后，相同id的再跟进name排序：

```html
http://localhost:3004/fruits?_sort=id,name&_order=desc,asc
```

### 取局部数据 Slice

slice的方式，和Array.slice() 方法类似。采用 `_start` 来指定开始位置，`_end` 来指定结束位置、或者是用 `_limit` 来指定从开始位置起往后取几个数据。

```html
http://localhost:3004/fruits?_start=2&_end=4
```

```json
[
  {
    "id": 3,
    "name": "Banana",
    "price": 4.5
  },
  {
    "id": 4,
    "name": "watermelon",
    "price": 12
  }
]
```



### 取符合某个范围 Operator

（1）采用 `_get` `_lte` 来设置一个取值范围（range）：

```html
http://localhost:3004/fruits?id_get=2&id_let=3
```

（2）采用 `_ne` 来设置不包含某个值：

```html
http://localhost:3004/fruits?id_ne=1&id_ne=4
```

```json
[
  {
    "id": 2,
    "name": "orange",
    "price": 8.88
  },
  {
    "id": 3,
    "name": "Banana",
    "price": 4.5
  }
]
```

（3）采用 `_like` 来设置匹配某一个字符串（或正则表达式）：

```html
http://localhost:3004/fruits?name_like=apple
```

```json
[
  {
    "id": 1,
    "name": "apple",
    "price": 33
  }
]
```



### 全文搜索 Full-text search

采用 `q` 来设置搜索内容

```
http://localhost:3004/fruits?q=orange
```

```json
[
  {
    "id": 2,
    "name": "orange",
    "price": 8.88
  }
]
```

除了获取数据，我们当然还希望能向操作sql一样能更改数据、删除数据了。

所以这里，我们采用大部分人熟悉的 ajax 方法，来操作下响应的数据。

案例

## 添加数据

POST方法，常用来创建一个新资源。

## 更新数据



## 删除数据



# 三、配置静态资源服务器

主要是用来配置图片、音频、视频资源

通过命令行配置路由、数据文件、监控等会让命令变的很长，而且容易敲错；

json-server允许我们把所有的配置放到一个配置文件中，这个配置文件一般命名为`json_sever_config.json` ;

**json_sever_config.json**

```json
{
	"port":"3004",
	"watch":true,
    "static":"./public",
	"read-only":false,
	"no-cors":true,
	"no-gzip":false
}
```

**package.json**

```json
{
	"scripts":{
		"mock":"json-server --c json_sever_config.json db.json"
	}
}
```

我们可以把我们的图片资源放在public目录中，但是public目录不仅在可以放在图片，也可以音频和视频，所有大家放资源的时候，在public下面创建images用来放置图片，创建audlio/video分别放置音频和视频；

既然我们已经在json_sever_config.json里面知名了静态文件目录，那么我们访问的时候，就可以忽略public；

图片：http://localhost:3004/@2