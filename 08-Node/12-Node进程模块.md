## 一 Process	
进程管理方面，Node提供了内置的模块Process，并暴露了全局对象process。
```
process.cwd();		//获取程序当前目录，即完整路径
process.chdir(‘/usr/local’’);	//改变程序当前目录，注意：参数必须是完整路径
process.pid;			//获取程序进程
process.title;			//获取进程名称
process.version		//获取node版本号
	process.versions		//获取版本属性
process.config			//查看node配置
process.execPath		//获取当前进程可执行文件绝对路径
process.argv			//获取当前进程命令行参数数组
process.platform		//获取系统信息
process.arch			//获取CPU架构
process.env			//获取当前shell环境变量参数
```
标准输出流：
node的console其实是通过Process模块的stdout.write()方法来实现的。
```
process.stdout.write(‘hello world’);
console.log与stdout.write()的区别是多了换行符
```
标准错误流：
process.stderr.write(‘err’);

标准输入流：
```
//控制台接受输入
process.stdin.setEncoding('utf8');
process.stdin.on('readable', function () {
    let chunk = process.stdin.read();
    if(chunk != null){
        process.stdout.write(chunk);
    }
});
//控制台结案数输入
process.stdin.on('end',function () {
    process.stdout.write('end');
});
```


杀死进程：
```
process.on('SIGHUP',function () {
    console.log('get SIGHUP');
});
setTimeout(function () {
    process.exit(0);    //真正的杀死进程
},1000);
process.kill(process.pid,'SIGHUP'); //kill方法只是发送一个sighup信号
```

异步方法：process.nextTick();	//比setTimeout效率高很多
```
console.time('timeout---');
setTimeout(function () {
    console.log('test timeout');
},0);
console.timeEnd('timeout---');

console.time('timeout---');
process.nextTick(function () {
    console.log('test nextTick');
});
console.timeEnd('timeout---');
```
Process模块是单线程的，无法充分利用多核CPU，但是优势是对单核CPU的高效率利用。为了解决无法利用多核的情况，node提供了child_process模块。该模块提供了4个创建子进程的方法：spawn() exec() execFile() fork()， 后三个都是基于第一个spawn()方法实现的。利用这些方法，可以轻松的实现多进程任务、操作shell、进程通信。
```
const { spawn } = require('child_process');
//创建子进程，改进程负责查询
let ls = spawn('ls', ['-lh','/var']);   
ls.stdout.on('data', (data) => {
    console.log(`stdout: ${data}`);
});
```
注意：以上代码需要linux环境执行，以上代码也可以使用exec()方法完成，二者区别是：
spawn方法的参数必须放到arg数组中，不能放到命令行参数中，即这些参数不能带空格。exec不存在这个问题。
spawn方法是异步中的异步，即在子进程开始执行时，就开始从一个流总将数据从子进程返回给Node，实际开发中，该方法可以用来处理图像、二进制数据；exec是同步中的异步，即尽管exec是异步，但是必须要等到子进程结束后再一次性返回所有的buffer数据。
通过上述方法，我们发现可以使用child_process模块的spawn方法绑定系统命令，如cat/exit等，实现命令行操作。
注意：node中的exit和close事件虽然都是退出、结束的意思，但是exit事件后，标准输入输出流仍然在开启，而close事件后是在一个子进程中所有的标准输入、输出流被终止时触发，因为多进程有时候会共享一个标准输入、输出流。
在Node中，正常退出时，退出码定义为0，非正常为1-3之间的数字。

fork方法：
该方法具备child_process实例所有方法，且返回对象具备内置通讯频道。
该方法在缺省情况下，派生的node进程的stdout、stderr会关联到父进程，如果要更改行为，可将options对象中的silent属性设置为true。
fork创建的进程运行完成后不会自动退出，需要process.exit()
这些派生的node进程是全新的V8实例，假设每个node进程大致需要至少30毫秒启动时间和10M内存，也就是说不能创建成百上千的实例。
node本身存在多个线程，但是运行在V8上的JS是单线程的。 


for与进程通信：
```
main.js:
const cp = require('child_process');
var n = cp.fork('./sub.js');      //fork一个子进程
n.on('message',function (info) {
    console.log("info:" + JSON.stringify(info));
});
n.send({
    main:'sub'
});

sub.js:
process.on('message',function (data) {
    console.log("data:" + JSON.stringify(data));
});

process.send({
    sub:'test'
});
```
运行main.js得到结果：
```
data:{"main":"sub"}
info:{"sub":"test"}
```
注意：send()方法是同步的。


延迟操作：
在JS中，使用setTimeout执行一个很小的延迟是可以接受的，但是Node有一个更好的方案：process.nextTick，可以用来包装一个同步操作。
process.nextTick方法可以把一个回调放在下一次事件轮询的队列头上，所以可以用来延迟执行，且比setTimeout更效率。
