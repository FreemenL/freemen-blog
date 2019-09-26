> 进程是操作系统分配资源和调度任务的基本单位 

操作系统分配资源 -> 由进程到线程

* 进程是操作系统分配资源的最小单位

* 线程是进程中的工作单元

 nodejs 单进程 单线程  某个操作如果需要大量消耗cup的情况下 后续的操作都需要等待。
 
nodejs  : ```单线程 非阻塞I/O```

优点：

 1. 节约内存  
 2. 节约上下文切换的时间 
 3. 锁的问题 ，并发资源的处理（ java里面的概念 并发的时候对资源加锁限制其他进程对其的访问 ）
 
 缺点 ：
 一个线程崩了整个程序就挂了
 
多线程是如何实现的？
并不是真正在同一时间节点执行多个任务，而是通过非常快速的切换时间片来实现的。

浏览器里的UI线程跟js线程是公用一个线程的 


webworker 不能改变js 单线程的本质  完全受主线程的控制 不能操作dom

堆heep     栈 stack    

堆里面存 对象    栈里面存 变量

http请求是单独的线程 

异步任务是成功后才方放到任务队列的

 nodejs中的事件循环 
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190309225209214.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NjUzNDQ5,size_16,color_FFFFFF,t_70)
LIBUV是 nodejs 中的异步I/O库

线程池加同步io 模拟异步io


什么叫同步和异步  ？

同步和异步关注的是消息的通知机制 

    去餐馆点饮料有 立马给你  ->同步 
          
         点餐没有  花时间做   -> 异步

什么叫阻塞和非阻塞？

	等待取餐的时候什么都不做  阻塞   
	等待取餐时候看玩儿游戏   非阻塞
	
阻塞和非阻塞是 程序在等待结果时的状态 

同步和异步取决于被调用者  
阻塞和非阻塞取决于调用者 



```node 文件名  > a.log```正常的输出会写入a.log   错误的输出会写在控制台显示

```node 文件名>a.log   2>&1```错误的输出2重定向到1 中  

```console.assert(1==2,"error");```断言  

process.cwd()  //当前工作目录

process.memoryUsage() //内存情况 

```console.log(process.memoryUsage()); ```
```json
//v8的内存量 1.7 G
{ rss: 20340736,   // 常驻内存 
  heapTotal: 6062080,  // 堆内存的申请总量 
  heapUsed: 3696456,  // 已经使用的量 
  external: 8272      // 外部内存的使用量 
}
```

> nextTick 和 setImmediate  区别和联系 
 * nextTick 把回调函数放在```微任务队列```
 * setImmediate  把回调函数放在```事件队列的尾部 ```
```js
function read(){
    setImmediate(function(){
        console.log(1)
    })
    process.nextTick(function(){
        console.log(2);
    })
}
read();
```
## module 
node 中的模块化遵循的是commonjs 规范

具体实现是会把每个模块放在一个立即执行函数中执行 ，这个函数包含5个参数
|参数  | 详情 |
|--|--|
| ```exports```| 当前模块的导出对象 |
| ```require```| require方法 |
| ```module```| 当前模块|
|```__filename```| 当前文件的绝对路径|
|```__dirname```| 当前文件夹的绝对路径|

> require 的属性

|参数  | 详情 |
|--|--|
| ```resolve```| 只想知道模块的绝对路径,但又不想加载它  require.resolve  |
| ```extensions```| nodejs模块分三种  js json node { '.js': [Function], '.json': [Function], '.node': [Function] }, |
| ```main ```| 入口文件 |
|```cache```| 缓存对象 模块在缓存的时候key值为导入模块的绝对路径 |

## node中的进制
```js
console.log(0b10100);  // 0b 表示二进制
console.log(0o24);  // 0o 表示八进制
console.log(0x14);  // 0x 表示十六进制
// 把十进制转成任意进制 
console.log((20).toString(2));
console.log(parseInt(0x4E07.toString(2)));
```			
			
## Buffer
```js
//解决乱码  
let buffer9 = Buffer.from("刘夏江");
let buffer10 = buffer9.slice(0,5);
const { StringDecoder } = require("string_decoder");
sd = new StringDecoder; //专门用来解决乱码问题的 ；
console.log(sd.write(buffer10)); //刘
console.log(sd.write(buffer11)); //  夏江
```
	
### 合并buffer
```js 
const buffer1 = Buffer.from("夏");
const buffer2 = Buffer.from("江");
Buffer.concat1 = function(list,total = list.reduce((len,item)=>len+item.length,0)){
    if(list.length==1){
        return list[0];
    }
    let result = Buffer.alloc(total);
    let index = 0;
    for(let buf of list){
        for(let b of buf){
            if(index<=total){
                result[index++] = b;
            }else{
                return result;
            }
        }
    }
    return result;
}

const result = Buffer.concat1([buffer1,buffer2]);

console.log(result.toString());
```
## fs 
|params|detail|
|--|--|
| fs.rename()| 修改文件名|
| fs.truncate | 截断文件内容
|fs.unlink| 删除文件
|fs.rmdir| 删除非空目录
| fs.watchFile|监听文件改动
|fs.statSync| 获取文件信息
|fs.open|打开文件 
|fs.close |关闭文件
|fs.createReadStream | 创建可读流
|fs.createWriteStream | 创建可写流
```js
//异步读文件
fs.readFile('./1.txt',{ encoding:"utf8",flag:"r"},function(error,data){
    if(error){
        console.log(error);
    }else{
        console.log(data);
    }
})
//异步写入文件
/* 
   flag:a    追加写入 
   mode      linux 权限位 
*/
fs.writeFile("./2.txt",'data',{encoding:"utf8",flag:"a",mode:0o666},function(error){
    if(error){
        console.log(error)
    }
})

//追加写入
fs.appendFile("./2.txt",'data1',function(error){
    if(error){
        console.log(error)
    }
})
//精确的控制读取的字节
//fd:file descript   文件描述符 
fs.open("./1.txt",'r',0o666,function(err,fd){
    let buffer = Buffer.alloc(4);
    // 读取文件部分内容
    fs.read(fd,buffer,1,3,1,function(err,bytesRead,buffe){
        console.log(buffe.toString());
    })
})
//监听控制台输入  标准输入
process.stdin.on("data",function(data){
    console.log(data);
})
//标准输出  consoel.log 对应 process.stdout.write
console.log('hellow');
process.stdout.write("hellow");
//错误输出  consoel.error 对应 process.stderr.write
console.error('hellow');
process.stderr.write("hellow");

```

##### 边读边写 
```js
const fs = require("fs"); 
const BUFFER_SIZE = 6;
function copy(src,target) {
    const buffer = Buffer.alloc(BUFFER_SIZE)
    fs.open(src,'r',0o666,function(error,readFd){
        fs.open(target,'w',0o666,function(err,writeFd){
            !function next(){
                fs.read(readFd,buffer,0,BUFFER_SIZE,null,function(err1,bytesRead){
                    if(bytesRead>0){
                        fs.write(writeFd,buffer,0,bytesRead,null,next)
                    }
                })
            }()
        })
    })
}

copy('./2.txt','./1.txt');
```
##  创建目录 

创建目录的时候其父目录必须是存在的
```js
const fs = require("fs");
fs.mkdir('a',0o666,function(error){
    console.log(error);
})
```
## 递归删除非空目录 
```js
const fs = require("fs");
//递归删除非空目录   
function rmdir(dir){
    let files = fs.readdirSync(dir);
    files.forEach((file)=>{
        let current = dir+"/"+file;
        let child = fs.statSync(current);
        if(child.isDirectory()){
            rmdir(current);
        }else{
            fs.unlinkSync(current);
        }
    })
    fs.rmdirSync(dir)
}
rmdir('a');
```
```js
fs.watchFile('./a.txt',(newStat,preStat)=>{
    if(Date.parse(preStat.ctime)==0){
        console.log("create");
    }else if(Date.parse(preStat.ctime)!==Date.parse(newStat.ctime)){
        console.log("update")
    }else{
        console.log('delete');
    }
})
```
```js
const fs = require("fs");
//可读流
const rs = fs.createReadStream('./a.txt',{
    mode:0o666,
    encoding:"utf8",
    flags:"r",
    start:3,
    end:8,
    highWaterMark:3
});
rs.on("open",function(data){
    console.log('open');
});
rs.on("data",function(data){
    console.log(data);
    rs.pause();
    setTimeout(function(){  
        rs.resume();
    },2000)
})
rs.on("end",function(data){
    console.log('end');
})

rs.on("error",function(data){
    console.log('error');
})
rs.on("close",function(data){
    console.log('close');
})
```
```js
const fs  = require("fs");
const path = require("path");
fs.readdir('./a',function(err,files){
    files.forEach(file=>{
        let child = path.join("a",file);
        fs.stat(child,function(err,stat){
            console.log(stat);
            //stat  文件信息
            // {
            //     dev: 16777220, //设备号
            //     mode: 16877,  
            //     nlink: 2,   
            //     uid: 501,  // 用户id
            //     gid: 20,   //用户组id
            //     rdev: 0,
            //     blksize: 4194304,
            //     ino: 8880296,
            //     size: 64,
            //     blocks: 0,
            //     atimeMs: 1551535852915.9565,
            //     mtimeMs: 1551535852801.7656,
            //     ctimeMs: 1551535852801.7656,  //
            //     birthtimeMs: 1551535852801.7656,
            //     atime: 2019-03-02T14:10:52.916Z,  //文件访问事件
            //     mtime: 2019-03-02T14:10:52.802Z,  //文件更新事件
            //     ctime: 2019-03-02T14:10:52.802Z,   // 文件内容更新事件
            //     birthtime: 2019-03-02T14:10:52.802Z } //创建时间
        })
    })
})
```
```js
const fs = require("fs");
const str = "学习";

fs.open('1.txt','w',0o666,(err,fd)=>{
    let buff = Buffer.from(str);
    //当我们用write 方法写文件的时候，并不会直接写入物理文件，而是先写入缓存区，再批量写入物理文件
    fs.write(fd,buff,0,3,0,(err,bytesWritten)=>{
        fs.write(fd,buff,3,3,3,(error,bytesWritten2)=>{
            //fs.fsync  迫使操作系统立马把缓存区的内容写入物理文件 
            fs.fsync(fd,()=>{
                fs.close(fd,()=>{
                    console.log('关闭完成!');
                })
            })
        })
    })
})
```

```js
const fs = require('fs');
//可写流 
const ws = fs.createWriteStream('./2.txt',{
    flags:"w",
    start:0,
    highWaterMark:3,
    mode:0o666
});

let flag = ws.write("1");
console.log(flag)
flag = ws.write("2");
console.log(flag)
flag = ws.write("3");
console.log(flag)
flag = ws.write("4");
console.log(flag)
flag = ws.write("5");
console.log(flag)
```

```js
// 一行一行读文件内容
const fs = require("fs");
const EventEmitter = require("events");
const util = require("util");

const NEW_LINE = 0x0A; // /n换行
const RETURN = 0x0D;  // /r 回车
function lineReader(path,encoding){
    EventEmitter.call(this);
    this.encoding = encoding;
    this._reader = fs.createReadStream(path);
    this.on("newListener",(type,listener)=>{
        if(type === "newLine" ){
            let buffer = []
            this._reader.on("readable",()=>{
                let char; //Buffer 
                while(null != (char=this._reader.read(1))){
                    switch(char[0]){
                        case NEW_LINE:
                            this.emit('newLine',Buffer.from(buffer).toString(this.encoding));
                            buffer.length = 0;
                            break;
                        case RETURN:
                            this.emit('newLine',Buffer.from(buffer).toString(this.encoding));
                            buffer.length = 0;
                            let newChar = this._reader.read(1);
                            if(newChar[0]!=NEW_LINE){
                                buffer.push(newChar[0]);
                            }
                            break;
                        default:
                            buffer.push(char[0]);
                            break;
                    }
                }
            });
            this._reader.on("end",()=>{
                this.emit('newLine',Buffer.from(buffer).toString(this.encoding))
                this.emit('end');
            });
        }
    })
}

util.inherits(lineReader,EventEmitter);
module.exports = lineReader;
//=======================================================
const lineReader  = require('./lineReader');
const reader = new lineReader('./1.txt','utf8');

reader.on("newLine",(data)=>{
    console.log(data);
})
reader.on("end",(data)=>{
    console.log("over");;
})
```

```js
//对象流
const { Transform } = require("stream");
const fs = require('fs');
const rs = fs.createReadStream("./test.json");

const toJSON = Transform({
    readableObjectMode:true, //可以往可读流里放对象 
    transform(chunk,encoding,cb){
        this.push(JSON.parse(chunk.toString()));
    }
})

const outJSON = Transform({
    writableObjectMode:true, //可以往可写流里放对象 
    transform(chunk,encoding,cb){
        console.log(chunk);
        cb();
    }
})
rs.pipe(toJSON).pipe(outJSON);
```
##  path
|参数|详情|
|--|--|
|path.delimiter|环境变量路径分隔符
|path.win32.delimiter|环境变量路径分隔符
|path.posix.delimiter|环境变量路径分隔符
path.sep)|文件路径分隔符
path.win32.sep)|文件路径分隔符
path.posix.sep)|文件路径分隔符
path.relative | 获取两个路径之间的相对路径
path.basename | 获取文件绝对路径
path.extname('ll.jpg')| 获取文件拓展名 
path.resolve()|将路径或路径片段的序列解析为绝对路径

##  stream 
```js
let { Duplex } = require("stream");
let index = 0;
//双工流  
let s = Duplex({
    read(){
        while(index++<3){
            this.push('a');
        }
        this.push(null);
    },
    write(chunk,encoding,cb){
        console.log(chunk.toString().toUpperCase());
        cb();
    }
});

process.stdin.pipe(s).pipe(process.stdout);

```

```js
//对象流
const { Transform } = require("stream");
const fs = require('fs');
const rs = fs.createReadStream("./test.json");

const toJSON = Transform({
    readableObjectMode:true, //可以往可读流里放对象 
    transform(chunk,encoding,cb){
        this.push(JSON.parse(chunk.toString()));
    }
})

const outJSON = Transform({
    writableObjectMode:true, //可以往可写流里放对象 
    transform(chunk,encoding,cb){
        console.log(chunk);
        cb();
    }
})
rs.pipe(toJSON).pipe(outJSON);
```
```js
const { Writable , Readable } = require('stream');
let i = 0;
let rs = Readable({
    highWaterMark:2,
    read(){
        if(i<10){
            this.push(""+i++);
        }else{
            this.push(null);
        }
    }
})
let ws = Writable({
    highWaterMark:2,
    write(chunk,encoding,callback){
        console.log(chunk.toString())
        callback()
    }
});

rs.pipe(ws);

setTimeout(function(){
    console.log(rs._readableState.length);
    console.log(ws._writableState.length);
})
```

```js
const { Transform } = require("stream");
// 转换流是实现数据转换的  
let t = Transform({
    transform(chunk,encoding,cb){
        this.push(chunk.toString().toUpperCase());
        cb()
    }
})
process.stdin.pipe(t).pipe(process.stdout);
```

## Tcp 协议
```1.传输层的TCP是基于网络层的IP协议的，而应用层的HTTP协议又是基于传输层的TCP协议的 ```
tcp协议的职责：
	为了方便通信将http 请求按序号分成多个报文段 ，把每个报文段按序重组并可靠的传输给对方
## IP协议
IP协议的职责是搜索对方的地址 一边中转一边传输  ```（寻址和路由）```
## http 协议
应用层 http 服务器是继承自传输层的tcp，它对请求和响应进行了包装。
1，是客户端跟的服务器沟通的一个协议
2，通过请求和响应达成通信
3，http 是一种不保存状态的协议

请求行
> HTTP/1.1      200     OK

请求头
>Host: localhost:8080
> User-Agent: curl/7.54.0
> Accept: */*
> Content-Length: 9
> Content-Type: application/x-www-form-urlencoded

请求体
###### http
方法|详情
--|--
http.createServer()|创建http服务

事件|详情
--|--
connection|客户端建联
request|客户端请求
close| 关闭服务器
error | 服务器错误

### req
```js
 //应用层 http 服务器是继承自传输层的tcp ；
//它对请求和响应进行了包装
const http = require("http");
const url = require("url");
//req  可读流
//res  可写流
/**
POST / HTTP/1.1
Host: localhost:8080
User-Agent: curl/7.54.0
Accept: 
Content-Length: 9
Content-Type: application/x-www-form-urlencoded
*/
const server = http.createServer();

server.on('connection',function(socket){
    console.log('客户端连接')
})

server.on('request',function(req,res){
    console.log(req.method);   //请求方法
    let urlobj = url.parse(req.url,true);//请求路径
    console.log(urlobj);
    console.log(urlobj.format(urlobj));
    console.log(req.headers);  //请求头
    let result = [];
    req.on("data",function(data){
        result.push(data);
    })
    req.on("end",function(){
       let r =  Buffer.concat(result);
       console.log(r.toString());
       res.end(r);
    })
})

server.on('close',function(req,res){
    console.log('服务器关闭')
})
server.on('error',function(error){
    console.log('服务器错误')
})
server.listen(8080,function(){
    console.log("http server started http://localhost:8080")
})

```

#### res

## url 解析url参数

* url.parse  解析路径信息为对象
* url.format   还原路径 
  

```js
const http = require("http");
/**
< HTTP/1.1 200 OK   
< Date: Mon, 04 Mar 2019 09:43:59 GMT   默认就有
< Connection: keep-alive
< Content-Length: 9
<  Transfer-Encoding: chunked  // 分块传输 
 */
const server = http.createServer(function(req,res){
    //writeHead 一旦调用会向客户端发送  setHeader 不会
    res.writeHead(200,"ok",{
        "Content-Type":"text/html;charset=utf8"
    });

    //当调用 writeHead 或者 write的时候向客户端发响应

    // res.statusCode = 200; // 设置状态码 
    // res.sendDate = false; // 响应头默认会设置 
    // res.setHeader("Content-Type","text/html;charset=utf8");
    // console.log(res.getHeader('Content-Type'));
    // res.removeHeader('Content-Type');
    // console.log(res.getHeader('Content-Type'));
    // res.write("hello");
    // res.write("world");
    // res.end();
});

server.listen(8080,function(){
    console.log("http server started http://localhost:8080")
})
```

http 可以作为服务端也可以作为客户端  作为客户端的两个重要的应用的场景就是爬虫和做```BFF```中间层

```js
//做中间层 发请求
const http = require("http");
const { StringDecoder } = require('string_decoder');
const decoder = new StringDecoder('utf8');

const options = {
    host:"localhost",
    port:8080,
    method:"POST",
    headers:{
        "Content-Type":"application/json"
    }
}
const req = http.request(options);

req.on("response",function(res){
    console.log(res.statusCode);
    console.log(res.headers);
    const result = [];
    res.on("data",function(data){
        result.push(data);
    })
    res.on("end",function(data){
        const str = Buffer.concat(result);
        console.log(decoder.write(str));
    })

})
req.write(`{"name":"xiajiang"}`);

req.end();
```

```js
//做服务端
const http = require('http');
const querystring = require('querystring');
const server = http.createServer();

server.on("request",function(req,res){
    const result = [];
    req.on("data",function(data){
        result.push(data);
    })
    req.on("end",function(){
        console.log("dara");
        const str = Buffer.concat(result).toString();
        console.log("dara");
        const contentType = req.headers["content-type"];
        const handle = {
            "application/x-www-form-urlencoded":function(datas){
                return querystring.parse(datas);
            },
            "application/json":function(datas){
                return JSON.parse(datas);
            }
        }
        const response = handle[contentType](str);
        res.end(JSON.stringify(response));

    })
    // res.end(`{"name":"夏季昂"}`);
})

server.listen(8080);
```
## zlib	压缩和解压
```js
const fs = require('fs');
const zlib = require('zlib');
const path  = require("path");

function gzip(src){
    fs.createReadStream(src)
    .pipe(zlib.createGzip())
    .pipe(fs.createWriteStream(src+'.gz'));
}
// gzip(path.join(__dirname,"msg.txt"));

function gunzip(src){
    fs.createReadStream(src)
    .pipe(zlib.createGunzip())
    .pipe(fs.createWriteStream(path.join(__dirname,path.basename(src,".gz"))))
}
gunzip(path.join(__dirname,"msg.txt.gz"))
```

## 响应内容进行压缩
```js
const http = require("http");
const path = require("path");
const url = require("url");
const fs = require("fs");
const zlib = require("zlib");
const mime = require("mime");
const { promisify } = require('util');
const stat = promisify(fs.stat);

http.createServer(async function(req,res){
    let { pathname } = url.parse(req.url);
    let filepath = path.join(__dirname,pathname);
    try{
        const statObj = await stat(filepath);
        res.setHeader("Content-Type",mime.getType(pathname));
        const acceptEncoding = req.headers['accept-encoding'];
        if(acceptEncoding){
            if(acceptEncoding.match(/\bgzip\b/)){
                res.setHeader('Content-Encoding',"gzip");
                fs.createReadStream(filepath).pipe(zlib.createGzip()).pipe(res);
            }else if(acceptEncoding.match(/\bdeflate\b/)){
                res.setHeader('Content-Encoding',"deflate");
                fs.createReadStream(filepath).pipe(zlib.createDeflate()).pipe(res);
            }else{
                fs.createReadStream(filepath).pipe(res);
            }
        }else{
            fs.createReadStream(filepath).pipe(res);
        }
    }catch(err){
        res.statusCode = 404;
        res.end()
    }
}).listen(8081);
```
## crypto  加密
对称加密
```js
//对称 加密
const crypto = require("crypto");
const path = require("path");
const fs = require("fs");

const str =  "xx";
const pk = fs.readFileSync(path.join(__dirname,"rsa_private.key"));
//使用秘钥进行加密
const cipher = crypto.createCipher("blowfish",pk);
cipher.update(str,'utf8');
const result = cipher.final("hex");
//使用秘钥进行解密
const decipher = crypto.createDecipher("blowfish",pk);
decipher.update(result,"hex");
let r = decipher.final("utf8");
console.log(r);
```
## 加盐算法
```js
const crypto  = require("crypto");
const path = require("path");
const fs = require("fs");
const key = fs.readFileSync(path.join(__dirname,"rsa_private.key"));
//密码123   加盐算法
const hmac = crypto.createHmac("sha1",key);
hmac.update("123");
const result = hmac.digest('hex');
console.log(result);
```

## yargs 获取命令行参数  (第三方模块) 
node 原生可以使用 process.argv  来获取 但操作起来不是很方便 所以可以使用像```yargs```
```commander```
等一些第三方的模块

```js
//yargs
#!/usr/bin/env node
const yargs = require("yargs");
let argv = yargs.options('n',{
    alias:"name",//别名
    demand:true,//必填
    default:'lxj',
    description:"请输入姓名"
})
.usage("hello [options]")
.help()
.example("hello -n lxj","执行hello 传入参数为lxj")
.alias("h","help")
.argv;

console.log(argv);
console.log(argv.name);
```
```js
//commander
const program = require('commander');
program
  .version(packageInfo.version)
  .command('run [name]', 'run specified task')
  .parse(process.argv);
```

## 浏览器缓存
#### 缓存的作用
* 减少了冗余数据的传输，节省了网费。
* 减少了服务器的负担，提高网站的性能

#### 缓存分为强制缓存和协商缓存 


* 强缓存如果生效不需要再和服务器发生交互，对比缓存不管生不生效都需要与服务端发生交互
* 两类缓存可以同时存在，强缓存的优先级高于对比缓存

##### 1.对比缓存（协商缓存）


 1. 通过最后修改时间来判断缓存是否可用(Last-Modified)：
 ![在这里插入图片描述](https://img-blog.csdnimg.cn/20190306104238259.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NjUzNDQ5,size_16,color_FFFFFF,t_70)

```js
/** 
 * 1.第一次访问服务器的时候，服务器会返回资源和缓存的标识，客户端则会把此资源缓存在本地的缓存数据库中。
 * 2.第二次客户端需要此数据的时候，要取得缓存的标识，然后去问下服务器我的资源是否是最新的。
 * 如果是最新的则直接使用缓存数据，如果不是最新的则服务器返回新的资源和缓存规则，客户端根据新的规则缓存新的数据
 */
const http = require("http");
const url = require("url");
const path = require("path");
const fs = require("fs");
const mime  = require("mime");

http.createServer(function(req,res){
    const { pathname } = url.parse(req.url,true);
    const filepath = path.join(__dirname,pathname);
    fs.stat(filepath,(err,stat)=>{
        if(err){
           return sendErr(req,res)
        }else{
            const ifModifiedSince = req.headers['if-modified-since'];
            const LastModified = stat.ctime.toGMTString();
            if(ifModifiedSince == LastModified){
                res.writeHead(304);
                res.end();
            }else{
                return send(req,res,filepath,stat)
            }
        }
    })
}).listen(8080);

function sendErr(req,res){
    res.end('Not Found');
}
function send(req,res,filepath,stat){
    res.setHeader("Content-Type",mime.getType(filepath));
    //把文件的最后修改时间发给客户端，客户端会把此时间保存起来 ，次再获取此资源的时候会把这个时间再发给服务器
    res.setHeader("Last-Modified",stat.ctime.toGMTString());
    fs.createReadStream(filepath).pipe(res);
}
```
>该方法存在的问题：
>1. ```某些服务器拿不到文件的最后修改时间，无法判断```
>2. ```某些文件修改的非常频繁，在秒以下的时间内进行修改 ，Last-Modified 只能精确到秒```
>3. ```一些文件修改时间改了，但内容没变，我们不希望客户端认为这个文件修改了```
>4. ```同一个文件处于多个CDN服务器上的时候，内容虽然一样但最后修改时间不一样```

### 2.Etag （协商缓存）
Etag是实体标签的缩写，根据实体内容生成一段hash字符串，可以标识资源的状态，资源发生改变时Etag也随之发生改变，ETag由服务端产生发给客户端。

* 第一次服务器返回的时候，会把文件的内容算出来一个标识（通常是md5值），发给客户端，
* 客户端看到Etag的时候，会把此标识符存在客户端，下次再访问服务器的时候，发给服务器
*  服务器根据两次的值对比判断是否返回新的内容。
![在这里插入图片描述](https://img-blog.csdnimg.cn/20190306111617873.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NjUzNDQ5,size_16,color_FFFFFF,t_70)
```js
const http = require("http");
const url = require("url");
const path = require("path");
const fs = require("fs");
const crypto = require("crypto");
const mime  = require("mime");

http.createServer(function(req,res){
    const { pathname } = url.parse(req.url,true);
    const filepath = path.join(__dirname,pathname);
    fs.stat(filepath,(err,stat)=>{
        if(err){
           return sendErr(req,res)
        }else{
            const ifNoneMatch = req.headers['if-none-match'];
            const out = fs.createReadStream(filepath);
            const md5 = crypto.createHash("md5");
            out.on("data",function(data){
                md5.update(data)
            })
            out.on("end",function(data){
                /**
                * mad5 ：
                * 相同的输入 相同的输出。
                * 不同的输入不同的输出。
                * 不能从输出反推输入
                */
               const Etag = md5.digest("hex");
               if(ifNoneMatch == Etag){
                   res.writeHead(304);
                   res.end();
               }else{
                   return send(req,res,filepath,Etag)
               }
            })
        }
    })
}).listen(8080);

function sendErr(req,res){
    res.end('Not Found');
}

function send(req,res,filepath,Etag){
    res.setHeader("Content-Type",mime.getType(filepath));
    //第一次服务器返回的时候，会把文件的内容算出来一个标识，发给客户端，
    //客户端看到Etag的时候，会把此标识符存在客户端，下次再访问服务器的时候，发给服务器
    res.setHeader("Etag",Etag);
    fs.createReadStream(filepath).pipe(res);
}
```
### 强缓存  
* 把资源缓存在客户端，如果客户端需要再次使用此资源的时候，先获取到缓存中的数据，看是否过期 如果过期了再请求服务器 
Cache-Control的值
no-cache  ： 不使用强缓存
no-store： 强缓存和协商缓存都不使用
关键代码 ，在响应头中设置
```js
res.setHeader("Cache-Control","max-age=30");
```
具体实现
```js
/** 
 * 强缓存
 * 把资源缓存在客户端，如果客户端需要再次使用此资源的时候，先获取到缓存中的数据，看是否过期
 * 如果过期了再请求服务器。
 */
const http = require("http");
const url = require("url");
const path = require("path");
const fs = require("fs");
const mime  = require("mime");

http.createServer(function(req,res){
    const { pathname } = url.parse(req.url,true);
    const filepath = path.join(__dirname,pathname);
    fs.stat(filepath,(err,stat)=>{
        if(err){
           return sendErr(req,res)
        }else{
            return send(req,res,filepath,stat)
        }
    })
}).listen(8080);

function sendErr(req,res){
    res.end('Not Found');
}
function send(req,res,filepath,stat){
    res.setHeader("Content-Type",mime.getType(filepath));
    res.setHeader("Cache-Control","max-age=30");
    fs.createReadStream(filepath).pipe(res);
}
```
## process  进程    
*  child_process  1.处理cup密集型的问题  2.多核的问题
*  child_process 可以开启多个子进程 ，多个子进程之间可以共享内存空间 可以通过子进程的相互通信实现信息的交换

#### ```spawn 开启子进程```
> ```fork exec execFile ```都是基于```spwan```的改进方法
```js
const { spawn } = require("child_process");
const path = require("path");

let p1  = spawn("node",["test1.js",'llll'],{
    cwd:path.join(__dirname,"test1"),
    stdio:[process.stdin,process.stdout,"pipe"]
});

p1.on("close",function(){
    console.log("子进程1关闭")
});
p1.on("exit",function(){
    console.log("子进程1退出")
});
```

```js
//test1.js
console.log(process.argv)
```
detach:  默认情况下父进程会等待所有的子进程完成后才能退出，但如果子进程设置了detach =true 的话 则此子进程可以单独存在；
```js
const { spawn } = require("child_process");
const path = require("path");
const fs = require("fs");
const fd = fs.openSync(path.join(__dirname,'msg.txt'),"w");
const p1 = spawn("node",["test4.js"],{
    stdio:["ignore",fd,"ignore"],
    cwd:path.join(__dirname,"test1"),
    detached:true
})

p1.on("error",function(err){
    console.log(err)
})
//让父进程先退出 子进程继续执行
p1.unref();
```
根据cpu.length 启动多个http服务
#### ```fork```
```js
// send 方法的第二个参数 可以是 http server net socket 
const { fork } = require("child_process");
const os = require("os");

const http = require("http");
const server = http.createServer(function(req,res){
    res.setHeader("Content-type",'text/html;charset=utf8');
    res.end("请求在父进程被处理")
});

server.listen(8080);

for(let i=0;i<os.cpus().length;i++){
    let p1 = fork("server.js",[],{
        cwd:__dirname
    });
    p1.send(`server:${i}`,server);
}
```

```js
//server.js
const http = require("http");
process.on("message",function(msg,server){
    const msgArr = msg.split(":");
    if(msgArr[0]=="server"){
        http.createServer(function(req,res){
            res.setHeader("Content-type",'text/html;charset=utf8');
            res.end("请求在子进程"+msgArr[1]+"被处理");
        }).listen(server)
    }
})
```
#### ```exec```
```js
//exec 执行一个shell 命令
const { exec } = require("child_process");
//用于使用 shell 执行命令
const p1 = exec("node test1.js a b c",{
    //timeOut:100000,
    // encoding:"utf8",
    cwd:__dirname
},function(err, stdout ,stdin){
    console.log(err);
    console.log(stdout)
});
```
#### ```execFile```
```js

const { execFile } = require("child_process");
//用于使用 shell 执行命令
const p1 = execFile("node",[ "test6.js" ,"a","b","c"],{
    cwd:__dirname
},function(err, stdout ,stdin){
    console.log(err);
    console.log(stdout)
});

```
## ```多语言的实现```
多语言的实现通常是服务端有各个国家的一堆语言包 ，然后根据客户端请求头中携带的 ```accept-language```字段 进行优先级的匹配 返回相应的内容
>伪代码
```js
const http = require("http");
const server = http.createServer(request);
server.listen(8080);
const lanPack = {
    en:{
        title:"welcome"
    },
    zh:{
        title:"欢迎光临"
    },
    default:"en"
}
function request(req,res){
    // 实现服务器和客户端的协议
    // Accept-Language: zh-CN,zh;q=0.9,en;q=0.8,jp;q=0.7
    let acceptLanguage = req.headers['accept-language'];
    if(acceptLanguage){
        let lans = acceptLanguage.split(",").map(item=>{
            let values = item.split(';');
            let name = values[0];
            let q = values[1]?parseFloat(values[1]):1;
            return {
                name,
                q
            }
        }).sort((a,b)=>b.q-a.q);
        let lan = lanPack['default']
        for(let i=0;i<lans.length;i++){
            if(lanPack[lans[i]["name"]]){
                lan = lans[i]["name"];
                break;
            }
        }
        res.setHeader("Content-Type","text/plan;charset=utf-8");
        res.end(lanPack[lan]["title"]);
    }
}
```
## ```防盗链```
防止资源被盗用 核心思想就是判断请求头中携带的refer 字段的值 跟请求资源的值是否一致， 当然也可以对允许访问的域名设置白名单
```js
//图片资源服务端代码
const http = require("http");
const url = require("url");
const fs = require("fs");
const path = require("path");
//建立白名单
const whiteHost=[
    "172.20.10.2",
    "localhost",
];

http.createServer(function(req,res){
    const refer = req.headers['referer']||req.headers['refer'];
    if(refer){
        // 引用地址
        const referHostName = url.parse(refer,true).hostname;
        //当前资源的请求地址
        const currentHostName = url.parse(req.url,true).hostname;
        if(!whiteHost.includes(referHostName)){
            res.setHeader('Content-Type','image/png');
            fs.createReadStream(path.join(__dirname,"404.png")).pipe(res);
            return 
        }
    }
    res.setHeader('Content-Type','image/png');
    fs.createReadStream(path.join(__dirname,"base.png")).pipe(res);
}).listen(3000);

```
## ```User-Agent```
识别不同的客户端