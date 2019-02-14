## 一 RabbitMQ简介
RabbitMQ可以用于实现大并发时的消息队列，同时处理不同的web服务器之间通信需求。  
所谓队列，就是抢占资源时的排队，消息就是不同服务器见传送的数据单位，可以是json、文本等。  
使用消息队列的优势是：
- 1-应用解耦：消息对垒在处理项目需求时，在中间插入了隐含的基于数据的接口层，两边的处理过程都要实现这一接口。这允许你独立的扩展活修改两边的处理过程，只要遵守同样的接口约定即可。
- 2-可扩展性：可以方便的实现大消息入队和处理的频率
- 3-灵活性与峰值处理能力：消息队列可以应对临时的突发高峰值访问
- 4-缓冲提升性能
- 5-异步通信：很多消息不需要立即处理，消息队列可以提供异步处理机制。
RabbitMQ的安装：
```
docker安装：
sudo docker pull rabbitmq
docker run -d -e RABBITMQ_NODENAME=my-rabbit --name some-rabbit -p 5672:5672 rabbitmq:3

mac安装：
    brew install rabbitmq
    vim ~/.bash_profile    //添加： export PATH=$PATH:/usr/local/sbin
    source ~/.bash_profile
    rabbitmq-server       // 重新打开终端输入
    浏览器输入：http://localhost:15672/     //账号密码全输入guest即可登录

    如果显示找不到主机，请在hosts文件中添加:
    vim /private/etc/hosts
    127.0.0.1  localhost

```
## 二 HelloWorld
server.js
```js
const amqp = require("amqplib");

//连接本地127.0.0.1的rabbitmq队列，返回一个promise对象
const connect = amqp.connect("amqp://127.0.0.1");

connect.then((conn)=>{

    //接收到CTRL+C时关闭连接
    process.once("SIGINT", ()=>{
        conn.close;
    });

    //创建一个通道
    let channel =  conn.createChannel();

    channel.then((ch)=>{

        //监听 hello 队列，并设置durable持久化为false，表示队列保存在内存中
        let ok = ch.assertQueue("hello",{durable: false});

        ok = ok.then((_qok)=>{
            
            //让通道消费hello队列，
            return ch.consume("hello", (msg)=>{

                console.log("Received:",msg.content.toString());

            }, {noAck: true});

        });

        //消费成功后，打印一行文本，表示服务器正常工作，等待客户端数据
        return ok.then((_consumeOK)=>{
            console.log("Waiting for msg,press CTRL+C exit!");
        });

    });

}).then(null, console.warn);
```
client.js
```js
const amqp = require("amqplib");

const connect = amqp.connect("amqp://127.0.0.1");

connect.then((conn)=>{

    let channel = conn.createChannel();
        
    channel.then((ch)=>{

        let q = "hello";
        let msg = "hello world";

        let ok = ch.assertQueue(q, {durable: false});

        return ok.then((_qok)=>{
            ch.sendToQueue(q, new Buffer(msg));
            return ch.close();
        });
    
    });


}).then(null, console.warn);
```
## 三 工作队列
一个生产者配合多个消费者的队列：可以让队列不至于堆积过长，同时也能保证队列的响应时间。  
下列代码使用传统的callback模式：
server.js:
```js

```
client.js:
```js

```
