## 一 Buffer
#### 1.1 Buffer的创建
可以将Buffer暂且看作Node内置的一种数据类型，类似String/Number这样直接使用。
Buffer类用于操作二进制数据，类内的参数用于分配长度，分配后不能更改。
Buffer的创建：
注意：new Buffer() 的方式已经在新版Node中废弃了。
buffer[index]:获取或设置在指定index索引未知的8位字节内容
```JavaScript
var bf = new Buffer('test','utf-8');
console.log(bf);
//此length长度和字符串的长度有区别，指buffer的bytes大小
for(var i = 0; i < bf.length; i++){
    console.log(bf[i].toString(16));
    console.log(String.fromCharCode(bf[i]));
}
```
#### 1.2 Buffer的实例方法
```JavaScript
buf.write(string,[offset],[length],[encoding]):根据参数offset，将参数string数据写入buffer
buf.toString([encoding],[length]):返回一个解码的string类型
buf.toJSON():返回一个JSON表示的Buffer实例，JSON.stringify将会默认调用来字符串序列化这个Buffer实例
buf.slice([start],[end]):返回一个新的buffer，这个buffer和老的buffer引用相同的内存地址
buf.copy(targetBuffer,[targetStart],[sourceStart],[sourceEnd])：进行buffer的拷贝，拷贝不会影响老的buffer。
```
```JavaScript
var str = 'test';
console.log(new Buffer(str));
var bf = new Buffer(3);         //只能写入前三位;test
bf.write(str);
console.log(bf);
```
#### 1.3 Buffer的静态方法
```JavaScript
Buffer.isBuffer(buf);		//判断是不是Buffer
Buffer.byteLength(str);	//，获取字节长度，第二个参数为字符集，默认utf8
Buffer.concat(list[, totalLength])	//Buffer的拼接
```
## 二 Stream流
使用readFile方法是极其占用内存的（一次加载大文件），读取完毕后再发送，读和发没有同时进行，这样不但影响性能，也会让网络应用、磁盘应用之间永远只有一个在疯狂活动，应该读取一点，发送一点，这样网络、磁盘都在积极使用。
使用流可以解决上述问题：
读取流：fs.createReadStream		req
写入流：fs.createWriteStream		res
读写流：压缩、加密
```JavaScript
const fs = require('fs');
let rs = fs.createReadStream('./1.jpg');        //读取流
let ws = fs.createWriteStream('./2.jpg');       //写入流
rs.pipe(ws);
//在服务器上应用：
const http = require('http');
const fs = require('fs');
http.createServer((req,res)=>{
    let rs = fs.createReadStream('./1.jpg');
    rs.pipe(res);
}).listen(8000);
```
注意：上述案例中，读取1.jpgg，写入到2.jpg，流是有方向的，从读取端流向写入端
流的结束事件是finish不是end。其实req，res也是流，因为只有流才有on事件。


