ajax 全名 async javascript and XML(异步JavaScript和XML)
是前后台交互的能⼒ 也就是我们客户端给服务端发送消息的⼯具，以及接受响应的⼯具
AJAX 是与服务器交换数据并更新部分网页的艺术，在不重新加载整个页面的情况下。
是⼀个 默认异步执⾏机制的功能,AJAX分为同步（async = false）和异步（async = true）

<script>
    // server.js
    var express = require('express')
    var app = express()

    app.use('/public', express.static('public'));
    app.get('/server',function(req,res){
        // 设置响应头
        res.setHeader('Access-Control-Allow-Origin','*')
        res.send('hello world')
    })
    
    app.post('/server',function(req,res){
        // 设置响应头,解决跨域问题
        res.setHeader('Access-Control-Allow-Origin','*')
        res.send('hello world POST')
    })
    
    app.get('/delay',function(req,res){
        // 设置响应头,解决跨域问题
        res.setHeader('Access-Control-Allow-Origin','*')
        setTimeout(()=>{    
            res.send('HELLO DELAYHHHH')
        },3000)
    })
    
    var server = app.listen(5000,function(){
    
        var host = server.address().address
        var port = server.address().port
        // 默认地址为localHost
        console.log("应用实例，访问地址为 http://%s:%s", host, port)
    })
</script>
<script>
    // client.js
    function loadXMLDoc(){
        let result = document.getElementById('myDiv')
        let btn = document.getElementsByClassName('btn')
        let xhr = null
        let isSending = false
        btn[0].onclick = function(){
            if(isSending==true) xhr.abort()
            xhr = new XMLHttpRequest()
            // 第三个参数：sync：true(异步)或 false(同步)
            xhr.open("get","http://127.0.0.1:5000/delay",true);
            // xhr.send('a:200&b:100');
            xhr.send()
            xhr.timeout = 5000 
            xhr.ontimeout = function(){
                alert('请求超时')
            }
            // xhr.onerror = function(){
            //     alert('网络出现了一些问题')
            // }
            isSending = true
            xhr.onreadystatechange = function(){
                // 判断服务端返回了所有结果
                if(xhr.readyState===4){
                    if(xhr.status>=200&&xhr.status<300){
                        isSending = false
                        // console.log(xhr.response)
                        result.innerHTML = xhr.response
                    }
                }
            }
        }

        btn[1].onclick = function(){
            xhr.abort()
        }
    }
</script>
(1)XMLHttpRequest对象
XMLHttpRequest 用于在后台与服务器交换数据。这意味着可以在不重新加载整个网页的情况下，对网页的某部分进行更新。

创建 XMLHttpRequest 对象的语法：
xhr=new XMLHttpRequest();

(2)服务器解决跨域问题（AJAX发送请求会失败 Access to XMLHttpRequest at 'http://127.0.0.1:5000/server' from origin 'null' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.）
<!-- 设置响应头,解决跨域问题 -->
res.setHeader('Access-Control-Allow-Origin','*')

(3)服务端响应json数据
由于 res.send 发送对象需要对对象进行字符串转换 let str = JSON.stringify(data)
xhr.response需要对字符串进行数据转换 let data = JSON.parse(str)
或者设置响应体的数据类型 xhr.responseType = 'json'

(4)http常见的状态码有那些？分别代表是什么意思？
200 - 请求成功
301 - 资源（网页等）被永久转移到其它URL
404 - 请求的资源（网页等）不存在
500 - 内部服务器错误

(5)AJAX发送请求的六大步骤
1、创建XML HttpRequest 对象，即创建一个异步调用对象。
2、创建一个新的HTTP 请求，并指定该HTTP 请求的方法，URL及验证信息
3、设置响应HTTP请求状态变化的函数。
4、发送HTTP 请求。
5、获取异步调用返回的数据。
6、使用JavaScript和DOM 实现局部刷新