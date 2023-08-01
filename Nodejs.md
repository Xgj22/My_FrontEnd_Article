(1)回调函数
<script>
    var fs = require("fs");

    fs.readFile('input.txt', function (err, data) {
        if (err) return console.error(err);
        console.log(data.toString());
    });
</script>

console.log("程序执行结束!");
阻塞与非阻塞调用的不同。第一个实例在文件读取完后才执行程序。 第二个实例我们不需要等待文件读取完，这样就可以在读取文件时同时执行接下来的代码，大大提高了程序的性能。
因此，阻塞是按顺序执行的，而非阻塞是不需要按顺序的，所以如果需要处理回调函数的参数，我们就需要写在回调函数内。
阻塞就像单线程，一个任务一个任务按顺序执行，一旦出现错误就阻塞在那里
非阻塞调用就像多线程，各个任务单独执行，哪个线程做分配到的任务，完成了对应的任务就行，某个线程的任务没做完那就做报对应的错，其他的不受影响。

(2)事件循环
Nodejs使用事件驱动模型，Node.js 使用事件驱动模型，当web server接收到请求，就把它关闭然后进行处理，然后去服务下一个web请求。
当这个请求完成，它被放回处理队列，当到达队列开头，这个结果被返回给用户。
<script>
    // 引入 events 模块
    var events = require('events');
    // 创建 eventEmitter 对象
    var eventEmitter = new events.EventEmitter();

    // 创建事件处理程序
    var connectHandler = function connected() {
    console.log('连接成功。');
    
    // 触发 data_received 事件 
    eventEmitter.emit('data_received');
    }
    
    // 绑定 connection 事件处理程序
    eventEmitter.on('connection', connectHandler);
    
    // 使用匿名函数绑定 data_received 事件
    eventEmitter.on('data_received', function(){
    console.log('数据接收成功。');
    });
    
    // 触发 connection 事件 
    eventEmitter.emit('connection');
    
    console.log("程序执行完毕。");
</script>

eventEmitter.on是绑定处理事件的函数（处理器），eventEmitter.emit是触发事件，事件的处理和请求是分开的，所以是异步。Vue中全局事件总线（vue$bus）（可子传父的通信方式）底层原理就是根据这个而来。
<script>
    var fs = require("fs");

    fs.readFile('input.txt', function (err, data) {
    if (err){
        console.log(err.stack);
        return;
    }
    console.log(data.toString());
    });
    console.log("程序执行完毕");
</script>
文件读取模块，fs.readFile() 是异步函数用于读取文件。 如果在读取文件过程中发生错误，错误 err 对象就会输出错误信息。
如果没发生错误，readFile 跳过 err 对象的输出，文件内容就通过回调函数输出

(3)eventEmitter
EventEmitter 类
events 模块只提供了一个对象： events.EventEmitter。EventEmitter 的核心就是事件触发与事件监听器功能的封装。

(4)Stream(流)
Node.js，Stream 有四种流类型：

Readable - 可读操作。Writable - 可写操作。Duplex - 可读可写操作.Transform - 操作被写入数据，然后读出结果。

Stream 对象都是 EventEmitter 的实例。常用的事件有：

data - 当有数据可读时触发。

end - 没有更多的数据可读时触发。

error - 在接收和写入过程中发生错误时触发。

finish - 所有数据已被写入到底层系统时触发。

<script>
    // 创建可读流
    var readerStream = fs.createReadStream('input.txt');

    // 设置编码为 utf8。
    readerStream.setEncoding('UTF8');
    
    // 处理流事件 --> data, end, and error
    readerStream.on('data', function(chunk) {
    data += chunk;
    });
    
    readerStream.on('end',function(){
    console.log(data);
    });
    
    readerStream.on('error', function(err){
    console.log(err.stack);
    });
</script>

写入流，下面该写法是覆盖写法，如果想要追加内容到out.txt中
<script>
    // 创建一个可以写入的流，写入到文件 output.txt 中
    var writerStream = fs.createWriteStream('output.txt');

    // 使用 utf8 编码写入数据
    writerStream.write(data,'UTF8');
    
    // 标记文件末尾
    writerStream.end();
    
    // 处理流事件 --> finish、error
    writerStream.on('finish', function() {
        console.log("写入完成。");
    });
    
    writerStream.on('error', function(err){
    console.log(err.stack);
    });
</script>

(5) 模块系统
模块是Node.js 应用程序的基本组成部分，文件和模块是一一对应的。
exports 和 module.exports 的使用

如果要对外暴露属性或方法，就用 exports 就行，要暴露对象(类似class，包含了很多属性和方法)，就用 module.exports。
<script>
    exports.world = function() {
        console.log('Hello World');
    }
</script>

<script>
    // Hello可以是对象，可以是方法
    module.exports = Hello;
</script>

(6)全局对象
js中有一个特殊的对象，被称为全局对象，在程序的任何地方都可以访问。在浏览器 JavaScript 中，通常 window 是全局对象， 而 Node.js 中的全局对象是 global。

__filename 表示当前正在执行的脚本的文件名。它将输出文件所在位置的绝对路径。
__dirname 表示当前执行脚本所在的目录。

setTimeout clearTimeout setInterval clearInterval console

process 是一个全局变量，它用于描述当前Node.js 进程状态的对象，提供了一个与操作系统的简单接口。
<script>
    process.on('exit', function(code) {
        // 以下代码永远不会执行
        setTimeout(function() {
            console.log("该代码不会执行");
        }, 0);

        console.log('退出码为:', code);
    });
    console.log("程序执行结束");
</script>
为什么该代码永远不会执行：‘exit’ 事件监听器的回调函数，只允许包含同步操作。

(7)GET/POST请求
GET
由于GET请求直接镶嵌在路径中，URL是完整的请求路径，包括？后面的部分，因此手动解析后面的内容作为GET请求的参数

<script>
    var http = require('http');
    var url = require('url');
    var util = require('util');

    http.createServer(function(req, res){
        res.writeHead(200, {'Content-Type': 'text/plain; charset=utf-8'});
        // url.parse()这个方法可以将一个url的字符串解析并返回一个url的对象
        // util.inspect()将对象转化成字符串
        res.end(util.inspect(url.parse(req.url, true)));
        // res.end(url.parse(req.url, true).toString());
    }).listen(3000);
</script>
POST
<script>
    var http = require('http');
var querystring = require('querystring');

var postHTML = 
    '<html><head><meta charset="utf-8"><title>菜鸟教程 Node.js 实例</title></head>' +
    '<body>' +
    '<form method="post">' +
    '网站名： <input name="name"><br>' +
    '网站 URL： <input name="url"><br>' +
    '<input type="submit">' +
    '</form>' +
    '</body></html>';

    http.createServer(function (req, res) {
    var body = "";
    req.on('data', function (chunk) {
        body += chunk;
    });
    req.on('end', function () {
        // 解析参数
        body = querystring.parse(body);
        // 设置响应头部信息及编码
        res.writeHead(200, {'Content-Type': 'text/html; charset=utf8'});
    
        if(body.name && body.url) { // 输出提交的数据
            res.write("网站名：" + body.name);
            res.write("<br>");
            res.write("网站 URL：" + body.url);
        } else {  // 输出表单
            res.write(postHTML);
        }
        res.end();
    });
    }).listen(3000);
</script>
返回一个字符串形式的对象
get和post的区别主要有以下几方面：
1、url可见性：
get，参数url可见；
post，url参数不可见

2、数据传输上：
get，通过拼接url进行传递参数；
post，通过body体传输参数

3、缓存性：
get请求是可以缓存的
post请求不可以缓存

4、后退页面的反应
get请求页面后退时，不产生影响
post请求页面后退时，会重新提交请求

5、传输数据的大小
get一般传输数据大小不超过2k-4k（根据浏览器不同，限制不一样，但相差不大）
post请求传输数据的大小根据php.ini 配置文件设定，也可以无限大。

6、安全性
这个也是最不好分析的，原则上post肯定要比get安全，毕竟传输参数时url不可见，但也挡不住部分人闲的没事在那抓包玩。安全性个人觉得是没多大区别的，防君子不防小人就是这个道理。对传递的参数进行加密，其实都一样。

7、数据包
GET产生一个TCP数据包；POST产生两个TCP数据包。对于GET方式的请求，浏览器会把http header和data一并发送出去，服务器响应200（返回数据）；而对于POST，浏览器先发送header，服务器响应100 continue，浏览器再发送data，服务器响应200 ok（返回数据）。在网络环境好的情况下，发一次包的时间和发两次包的时间差别基本可以无视。而在网络环境差的情况下，两次包的TCP在验证数据包完整性上，有非常大的优点。并不是所有浏览器都会在POST中发送两次包，Firefox就只发送一次

