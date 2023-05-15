# Ajax

>前言
学完SpringBoot,仔细了解了一下学习路线,还是要学vue啊,但是vue又用到了axios,而axios又用到了ajax
所以我来了

同样还是尚硅谷的视频,[视频地址](https://www.bilibili.com/video/BV1WC4y1b78y?p=1&vd_source=5815ab19beeaab6a8ea7a561cba20233)

## Ajax基础

### 原生Ajax

#### Ajax简介

>Ajax全程为Asynchronous JavaScript And XML 就是异步的JS和XML,通过Ajax可以在浏览器中向服务器发送异步请求,最大的优势,无刷新获取数据Ajax不是新的编程语言,而是一种将现有的标准组合在一起使用的新方式



前面这两节课都是介绍场景,然后还有xml简介的,这个再熟悉不过了


#### Ajax的优缺点

**优点**
- 可以无需刷新页面与服务器端进行通信
- 允许你根据用户事件来更新部分页面内容

**缺点**
- 没有浏览历史,不能回退
- 存在跨域问题(同源)
- SEO不友好

### HTTP协议

这老师怎么突然从word跑到了vscode的markdown了哈哈哈哈艹

**请求报文**
>*重点是格式与参数*

- 请求行
- 请求头
- 空行
- 请求体


**响应报文**

- 响应行
- 响应头
- 空行
- 响应体


### Node.js
竟然用到这个东西了,之前装这个东西还是在写博客的时候
没想到这么巧
就直接拿来用了

### Express
这个是没听过

就是用于代替服务端的,毕竟学前端的人不会去学后端,我感觉我可以自己来写一下后端哎,反正是接收请求啥的

先跟着老师写,然后看看能不能自己改成controller啥的

安装也很简单
管理员模式:
- npm init --yes
- npm i express

就安装好了

express的使用
```js
// 1. 引入express
const { request, response } = require('express');
const express = require('express');

// 2. 创建应用对象
const app = express();

// 3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
app.get('/',(request,response)=>{
    // 设置响应
    response.send("Hello Express");
});

// 4. 监听端口启动服务
app.listen(8000, ()=>{
    console.log("服务已经启动,8000 端口监听中");
});
```

## 简单案例

### GET

**先是服务端**

跟上面的代码区别不大,主要区别就在于设置了一个响应头,用于允许跨域,以及设置get请求的请求路径为/server

```js
// 1. 引入express
const { request, response } = require('express');
const express = require('express');

// 2. 床架应用对象
const app = express();

// 3. 创建路由规则
// request 是对请求报文的封装
// response 是对响应报文的封装
app.get('/server',(request,response)=>{

    // 设置响应头,设置允许跨域
    response.setHeader('Access-Control-Allow-Origin','*')

    // 设置响应
    response.send('HELLO AJAX')
});

// 4. 监听端口启动服务
app.listen(8000, ()=>{
    console.log("服务已经启动,8000 端口监听中");
});
```

然后是页面实现

还是基础的js语法,大概想起了一些,什么document

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax GET 请求</title>
    <style>
        #result {
            width: 200px;
            height: 100px;
            border: solid 1px #90b
        }
    </style>
</head>

<body>
    <button>点击发送请求</button>
    <div id="result"></div>

    <script>
        // 获取button元素
        const btn = document.getElementsByTagName('button')[0];
        const result = document.getElementById('result');
        // 绑定事件
        btn.onclick = function () {
            // 1. 创建对象
            const xhr = new XMLHttpRequest();
            // 2. 初始化 设置请求方法和 url
            xhr.open('GET', 'http://localhost:8000/server');
            // 3. 发送
            xhr.send();
            // 4. 事件绑定 处理服务端返回的结果
            // on 当..
            // readystate 是xhr对象中的属性,表示状态 0,1,2,3,4
            // change 改变 
            xhr.onreadystatechange = function () {
                // 判断(服务端返回了所有的结果)
                if (xhr.readyState === 4) {
                    // 判断响应状态码 200 404 403 401 500 ...
                    if (xhr.status >= 200 && xhr.status < 300) {
                        // 处理结果 行 头 空行 体
                        // 响应 
                        // console.log(xhr.status);// 响应状态码
                        // console.log(xhr.statusText);// 状态字符串
                        // console.log(xhr.getAllResponseHeaders());//所有响应头
                        // console.log(xhr.response);//响应体
                        
                        // 设置result的文本
                        result.innerHTML = xhr.responseText;
                    }else{

                    }
                }
            };
        }
    </script>

</body>

</html>
```

所以那个server.js,其实就是默认了一个服务器
直接用tomcat应该也一样,用SpringBoot创建一个项目,写一个controller接收server请求就行了

没问题啊没问题
看代码
```java
package com.zzmr.boot.controller;

import org.springframework.web.bind.annotation.CrossOrigin;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
@CrossOrigin
public class AjaxController {

    @GetMapping("/server")
    public String replaceServer(){
        return "Hello Ajax";
    }

}
```

**发送参数**
直接在url后面拼接就行了

在SpringBoot允许跨域,其中的一个注解就是@CrossOrigin(刚百度的)
加上这个注解,就和写的server.js效果一样了

### POST

>案例简介:当鼠标放到div上时,向服务端发送请求,将响应体在div中展示

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax POST 请求</title>
    <style>
        #result{
            width: 200px;
            height: 100px;
            border:solid 1px #904;
        }
    </style>
</head>
<body>
    <div id="result"></div>
    <script>
        // 获取元素对象
        const result = document.getElementById("result");
        // 绑定事件
        result.addEventListener("mouseover", function() {
            // 创建对象
            const xhr = new XMLHttpRequest();
            // 初始化 设置类型与url
            xhr.open("POST", 'http://localhost:8000/server')
            // 发送
            // xhr.send('a=100&b=200&c=300');
            xhr.send('a:100&b:200&c=300');
            // 事件绑定
            xhr.onreadystatechange = function() {
                // 判断
                if (xhr.readyState == 4) {
                    if(xhr.status >= 200 && xhr.status <300){
                        // 处理服务端返回的结果
                        result.innerHTML = xhr.response;
                    }
                }
            }
        });
    </script>
</body>
</html>
```

也没什么难的

### Ajax设置请求头

就是在open()后面再加一个方法
```js

        // 设置请求头 Content-Type 是用来设置请求体内容类型的
        xhr.setRequestHeader("Content-Type", "application/x-www-form-urlencoded");
        // 自定义头信息
        xhr.setRequestHeader('name','zzmr');
```

### 服务端响应Json格式数据

>案例简介:在这个窗口下,按下任何一个按键,就会像服务端发送请求,服务端返回结果,把结果呈现在div中

```html
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Ajax Json</title>
    <style>
        #result {
            width: 200px;
            height: 100px;
            border: solid 1px rgb(46, 203, 72);
        }
    </style>
</head>

<body>
    <div id="result"></div>
    <script>
        const result = document.getElementById("result");
        // 绑定键盘按下事件
        window.onkeydown = function () {
            // 发送请求
            const xhr = new XMLHttpRequest();

            // 设置响应体数据的类型-自动转换json要用的
            xhr.responseType = 'json';

            // 初始化
            xhr.open('GET', 'http://localhost:8000/json-server');
            // 发送
            xhr.send();
            // 事件绑定,处理服务端返回结果
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        // console.log(xhr.response);
                        // result.innerHTML = xhr.response;
                        // 手动对数据转化
                        // let data = JSON.parse(xhr.response);
                        // result.innerHTML = data.name;

                        // 自动转换
                        result.innerHTML = xhr.response.name;
                    }
                }
            };
        }
    </script>
</body>

</html>
```

这里也很简单,主要就是注意前端接受后端发来的json格式的数据,如何转换的问题
有两种方式:
1. 手动转换-调用JSON.parse()
```js
            // 手动对数据转化
            let data = JSON.parse(xhr.response);
            result.innerHTML = data.name;
```

2. 自动转换
在新建xhr的时候设置
```js
            // 设置响应体数据的类型-自动转换json要用的
            xhr.responseType = 'json';
```
这样,就可以直接用了
```js
            // 自动转换
            result.innerHTML = xhr.response.name;
```

这里我还是用的SpringBoot自己实现的服务端
```java
    @GetMapping("/json-server")
    public Map replaceJsonServer() {
        //返回一个对象给浏览器 const data = { name: 'zzmr'}
        // 因为加上@RestController就是默认返回json格式的数据了，这里写一个map来表示键值对
        Map<String, String> map = new HashMap<>();
        map.put("name", "zzmr");
        return map;
    }
```


### nodemon
自动重启服务

安装命令:
npm install -g nodemon

开启服务的命令:
npx nodemon server.js
不加npx就报错..
但是我感觉我用不到啊

### 解决IE缓存问题
案例就不写了,知道是用什么手法解决的就行:
时间戳解决
请求路径:'http://localhost:8000/ie?t'+Date.now()


## 各类问题

### 超时与网路异常

```js
    <script>
        const button = document.getElementsByTagName('button')[0];
        const result = document.querySelector('#result');

        button.addEventListener('click', function () {
            const xhr = new XMLHttpRequest();

            // 超时设置  2s 没有结果,请求取消
            xhr.timeout = 2000;
            // 超时回调
            xhr.ontimeout = function(){
                alert("网络异常,请稍后重试");
            }
            // 网络异常回调
            xhr.onerror = function(){
                alert("网络似乎除了一些问题");
            }

            xhr.open('GET', "http://localhost:8000/timeout");
            xhr.send();
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4) {
                    if (xhr.status >= 200 && xhr.status < 300) {
                        result.innerHTML = xhr.response;
                    }
                }
            }
        })
    </script>
```

也没啥,就是两个方法:ontimeout和onerror

后端的话,用了sleep
```java
    //    延时响应
    @GetMapping("/timeout")
    public String replaceTimeServer() throws InterruptedException {
    //    延时3000ms再响应
        Thread.sleep(3000);
        return "延时响应";
    }
```

### 取消请求

```html
<script>
        const btn1 = document.getElementsByTagName('button')[0];
        const btn2 = document.getElementsByTagName('button')[1];
        let xhr = null;

        btn1.onclick = function() {
            xhr = new XMLHttpRequest();
            xhr.open('GET','http://localhost:8000/timeout');
            xhr.send();
        }

        // 取消方法 abort
        btn2.onclick = function() {
            xhr.abort();
        }
    </script>
```
取消就是一个abort()方法,需要注意的是要把变量xhr声明在两个onclick方法外面(*但是我实测在里面也一样能访问到*)

### 请求重复发送

同一个请求,连续发送,会造成服务器压力过大的情况

原理就是,发送一个请求,先看一下之前是否有相同请求(该暂未响应),有的话,就取消上次的请求,发送新的请求

```html
<script>
        const btn1 = document.getElementsByTagName('button')[0];
        const btn2 = document.getElementsByTagName('button')[1];
        let xhr = null;

        // 标识变量
        let isSending = false; // 是否正在发送Ajax请求,true表示正在发送,false表示没有正在发送


        btn1.onclick = function () {
            // 判断标识变量
            if (isSending) {
                xhr.abort(); // 如果正在发送,则取消该请求,创建一个新的请求
            }
            xhr = new XMLHttpRequest();
            // 修改标识变量的值
            isSending = true;
            xhr.open('GET', 'http://localhost:8000/timeout');
            xhr.send();
            
            xhr.onreadystatechange = function () {
                if (xhr.readyState === 4) {
                    // 修改标识变量
                    isSending = false;
                }
            }
        }

        // 取消方法 abort
        btn2.onclick = function () {
            xhr.abort();
        }
    </script>
```

就是使用了一个标识变量,但是说实话我这我有一点迷


## JQuery中的Ajax

### GET请求和POST请求
```javascript
    <script>
        $('button').eq(0).click(function () {
            $.get('http://localhost:8000/jquery-server', { a: 100, b: 200 }, function (data) {
                console.log(data);
            });
        })

        $('button').eq(1).click(function () {
            $.post('http://localhost:8000/jquery-server', { a: 100, b: 200 }, function (data) {
                console.log(data);
            }, 'json');
        })
    </script>
```

写了两个控制器方法
```java
    @GetMapping("/jquery-server")
    public String JQueryGETServer(){
        return "Hello JQuery Ajax -- GET";
    }

    @PostMapping("/jquery-server")
    public Map JQueryPOSTServer(){
        Map<String, String> map = new HashMap<>();
        map.put("name", "zzmr");
        return map;
    }
```

### 通用方法

```js
$('button').eq(2).click(function () {
            $.ajax({
                // url
                url: 'http://localhost:8000/jquery-server',
                // 参数
                data: { a: 100, b: 200 },
                // 请求类型
                type: 'POST',
                dataType: 'json',
                // 成功的回调
                success: function (data) {
                    console.log(data);
                },
                // 超时时间
                timeout: 2000,
                // 失败的回调
                error: function () {
                    console.log('出错了!!!!');
                },
                // 头信息
                headers: {
                  c:300,
                  d:400  
                }
            });
    });
```

## axios
最热门的ajax工具库
[项目地址](https://github.com/axios/axios)

可以在目录里安装axios,也可以在页面引用:
```html
    <script src="https://cdn.jsdelivr.net/npm/axios@1.1.2/dist/axios.min.js"></script>
```

使用cdn加速:
```html
    <script crossorigin="anonymous" src="https://cdn.bootcdn.net/ajax/libs/axios/1.2.2/axios.js"></script>
```


```js
<script>
        const btns = document.querySelectorAll('button');

        btns[0].onclick = function () {
            //GET请求
            axios.get('http://localhost:8000/axios-server', {
                // url 参数
                params: {
                    id: 100,
                    vip: 7
                },
                // 请求头信息
                headers: {
                    name: 'zzmr',
                    age: 20
                },
            }).then(value => {
                console.log(value);
            });
        }

        btns[1].onclick = function () {
            axios.post('http://localhost:8000/axios-server', {
                username: 'admin',
                password: 'admin'
            }, {
                //url 
                params: {
                    id: 200,
                    vip: 9
                },
                //请求头参数
                headers: {
                    height: 180,
                    weight: 180,
                }
            });
        }

        btns[2].onclick = function () {
            axios({
                // 请求方式
                method : 'POST',
                //url
                url: 'http://localhost:8000/axios-server',
                // 参数
                params: {
                    vip:10,
                    level:30
                },
                //头信息
                headers: {
                    a:100,
                    b:200
                },
                // 请求体参数
                data: {
                    username: 'admin',
                    package: '010203'
                }
            }).then(response=>{
                console.log(response);
                // 响应状态码
                console.log(response.status);
                // 响应状态字符串
                console.log(response.statusText);
                // 响应头信息
                console.log(response.headers);
                // 响应体
                console.log(response.data);
            })
        }

    </script>
```
最下面的这种是最常用的

## 使用fetch函数发送Ajax请求

这个放着吧,不想看了

## 同源策略
一种安全策略
同源:协议,域名,端口号必须完全相同
违背同源策略就是跨域

看看吧,剩下的都是知识点,前面的都是讲怎么用的

`<script>`标签默认可以实现跨域

jsonp
Jquery实现Jsonp
还有CORS

就仔细看看CORS吧

CORS:跨域资源共享,CORS是官方的跨域解决方案,它的特点是不需要在客户端做任何特殊的操作,完全在服务器中进行处理,支持get和post请求(还有其他的),跨域资源共享标准新增了一组HTTP首字段,允许服务器声明哪些源站通过浏览器有权限访问哪些资源


就是加上@CrossOrigin注解其实是

结束了
2022年12月31日 18点34分
