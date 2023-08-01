(1)简介
axios时目前最流行的ajax封装库之一，用于很方便地实现ajax请求的发送。

支持的功能：
从浏览器发出 XMLHttpRequests请求。
从 node.js 发出 http 请求。
支持 Promise API。
能拦截请求和响应。
能转换请求和响应数据。
取消请求。
实现JSON数据的自动转换。
客户端支持防止 XSRF攻击。

(2)配置对象常用的配置项

```js
{

  // 路径url
  url: '/user', // 请求方法，默认get
  method: 'get',
  //基础url，最终请求的url是 baseURL+url拼接，所以再全局设置默认，可以使得发送请求时的url变得简洁
  baseURL: 'https://some-domain.com/api/',
  //设置请求头
  headers: {'X-Requested-With': 'XMLHttpRequest'},
  //设置请求url的query参数，可以使得url简洁。
  //比如url是https://some-domain.com/api/user  然后params如下设置，那么最终的url是：
  //https://some-domain.com/api/user?ID=12345&name=Jack
  params: {
    ID: 12345,
    name:"Jack"
  }, 
  //设置请求体
  data: {
    firstName: 'Fred'
  },
  //设置请求的另外一种格式，不过这个是直接设置字符串的
  data: 'Country=Brasil&City=Belo Horizonte',
  //请求超时，单位毫秒，默认0，不超时。
  timeout: 1000,//响应数据类型，默认json
  responseType: 'json', //响应数据的编码规则，默认utf-8
  responseEncoding: 'utf8',//响应体的最大长度 
  maxContentLength: 2000, // 请求体的最大长度
  maxBodyLength: 2000,//设置响应状态码为多少时是成功，调用resolve，否则调用reject失败
  //默认是大于等于200，小于300
  validateStatus: function (status) {
    return status >= 200 && status < 300; 
  },
}
```





(3)interceptors 拦截器
```js
<!-- 请求拦截器 -->
req.interceptors..request.use((config)=>{
    <!-- 成功的回调 -->
    <!-- config：配置对象，对象里面有一个重要属性，请求头headers -->
    return config
},(error)=>{
    return Promise.reject()
})

<!-- 响应拦截器 -->
requests.interceptors.response.use((res)=>{
    nProgress.done()
    <!-- 成功的回调函数 -->
    return res.data
},(error)=>{
    <!-- 响应失败的回调函数 -->
    return Promise.reject(new Error('faile'))
})
```



(4)axios.create
axios.create(config) 对axios请求进行二次封装
根据指定配置创建一个新的 axios ,也就是每个axios 都有自己的配置
新的 axios 只是没有 取消请求 和 批量请求 的方法，其它所有语法都是一致的

```js
const requests = axios.create({
    //配置对象
    baseURL:'http://localhost:3000/api',
    timeout:5000
})
```



(5)取消请求

```js
	//第一步：定义一个全局的cancel变量，初始值是null
    let cancel = null;
    document.getElementById("btn1").onclick = function(){
        axios.get("http://localhost:3000/posts/1",
        {
            //第二步：在请求的配置对象中，配置cancelToken属性值，并把函数的c参数赋值给全局变量cancel
            cancelToken:new axios.CancelToken(function(c){
                cancel = c;
            })
        })
    .then(function(response){
        //
        console.log(response);
    }).catch(function(error){
        console.log("请求回调失败");
    })
    }
    document.getElementById("btn2").onclick = function(){
        //第三步：调用cancel函数就是取消请求接收
        cancel();
    }
```



<script>
    //第一步：定义一个全局的cancel变量，初始值是null
    let cancel = null;
    document.getElementById("btn1").onclick = function(){
        axios.get("http://localhost:3000/posts/1",
        {
            //第二步：在请求的配置对象中，配置cancelToken属性值，并把函数的c参数赋值给全局变量cancel
            cancelToken:new axios.CancelToken(function(c){
                cancel = c;
            })
        })
    .then(function(response){
        //
        console.log(response);
    }).catch(function(error){
        console.log("请求回调失败");
    })
    }
    document.getElementById("btn2").onclick = function(){
        //第三步：调用cancel函数就是取消请求接收
        cancel();
    }
</script>