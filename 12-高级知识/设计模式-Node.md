## 说明
由于node也是采用了JS语言，与JS本身的设计模式大同小异。
## 1 单例模式
```js
var _instance = null;//定义初始化_instance  
module.exports = function(time){  
      function Car(time){  
          this.time = time;  
      }  
      this.getInstance = function(){  
          if(_instance != null){  
             return _instance;  
          }else{  
             return new Car(time);  
          }       
      }  
}  
```
## 2 适配器模式
```js
/*target.js*/  
module.exports = function(){  
    this.request = function(){//原接口  
        console.log('Target::request');  
    }  
}  
[javascript] view plain copy
/*adapter.js*/  
var util = require('util');  
var Target = require('./target.js');  
var Adaptee = require('./adaptee.js');  
  
function Adapter(){  
    Target.call(this);  
    this.request = function(){//重写原接口  
        var adapteeObj = new Adaptee();//重写的内容  
        adapteeObj.specialRequest();  
    }  
}  
util.inherits(Adapter, Target);//通过继承原模块, 获得原接口  
module.exports = Adapter;  
```
## 3 装饰模式 
```js
module.exports = function(){  
   this.dosomething = function(){  
      console.log("Nice to meet u.");  
   }  
}  

/*Decorator.js*/  
var util = require("util);  
var Base = require('./Base');  
  
function Decorator(){  
   Base.call(this);  
   this.dosomething = function(){  
          Base.dosomething();  
          console.log('I am a decorator');//拓展内容  
   }  
}  
util.inherits(Decorator, Base);//继承  
module.exports = Decorator;  
```
## 4 观察者模式
```js
/*被观察者*/  
module.exports = function(){  

    var m_obserSet = [];//观察者列表  
    var _self = this;  
  
    this.addObser = function(observer){  
        m_obserSet.push(observer);//添加观察者  
    }  
  
    this.doAction = function(){  
        console.log("Observable do some action");  
        _self.notifyAllObeser();  
    }  
  
    this.notifyAllObeser = function(){//发生动作  
        for(var key in m_obserSet){//逐个通知观察者  
            m_obserSet[key].update();//观察者执行回调  
        }  
    }  
  
}  
```
## 发布订阅模式的详解
发布订阅模式定义了对象间的一种一对多的关系，让多个观察者对象同时监听某一个主题对象，当一个对象改变时，所有依赖于它的对象都将得到通知。发布者和订阅者之间完全解耦，仅仅共享一个自定义事件的名称。
JS传统的事件就是观察者模式，在浏览器端，这样做基本已经足够了，但是在非浏览器环境下，通过自定义事件（即发布订阅），可以解决许多实际问题。
JS原生发布订阅模式：
```js
class PubSub {
    constructor () {
        this.eList = {};
    }
    on (eName,eHandle) {
        if (!(eName in this.eList)) {
            this.eList[eName] = [];
        }
        this.eList[eName].push(eHandle);
        return this;
    }
    emit (eName,...data) {
        const currentE = this.eList[eName];
        if (Object.property.toString.call(currentE) !== '[object Array]') {
            return false;
        }
        currentE.forEach((item)=>{
            item.apply(null,data);
        });
        return this;
    }
}

const p = new PubSub();

//订阅A事件
p.on('A',(...args)=>{
    console.log(args);
});
//订阅B事件
p.on('B',(...args)=>{
    console.log(args);
});

//发布A事件
p.emit('A', {
    name: 'zhaoyiming',
    work: 'FE'
});
//发布B事件
p.emit('B','B事件发布了');

```
Node中的原生实现：
```js
const Emitter = require('events').EventEmitter;

const e = new Emitter();

e.on('A',(stream)=>{
    console.log(stream + 'from eHandle1');
});

e.on('B',(stream)=>{
    console.log(stream + 'from eHandle2');
});

e.emit('A','A事件发布了');

```
## Node中高并发发布订阅案例
雪崩问题：在高访问量、大并发量的情况下，如果缓存失效，大量的请求同时涌入数据库中，数据库服务器无法承担压力进而影响网站整体的响应速度。
利用EventEmitter提供的once方法，通过发布/订阅模式可以解决雪崩问题。once方法的特点是，通过它添加的侦听器只能执行一次，执行之后就会将它与事件的关联删除。代码如下：
```js
var events = require('events');
var proxy = new events.EventEmitter();

var status = "ready";
var SQL = "select * from table1";

var select = function(callback)
{
    proxy.once('selected', callback);
    if(status === 'ready')
    {
        status = 'pending';
        db.select(SQL, function(results){
            proxy.emit('selected', results);
            status = 'ready';
        });
    }
};  
```
对于每一次查询，都会通过once方法订阅selected事件。当某个查询正在进行时，其他同时到达的查询处于pending状态，在订阅了selected事件后什么都不做，而请求的回调被压入事件队列中。当那个查询结束时，执行emit方法发布selected事件并更新状态。此时，那些订阅了selected事件的查询的回调函数被依次调用，并传入查询结果results作为参数。由于once函数的特点，每个查询的回调只会被执行一次。在执行回调的过程中，由于status变为ready，又可以响应其他的查询。
在这个过程中，只查询了数据库一次，其他的相同查询只是利用了这次查询的结果执行了回调而已。

## Node操作redis实现发布订阅

首先先说一下流程：
- 1. 保存数据到 Redis，然后将 member 值 publish 到 chat 频道（publish.js 功能）
- 2.readRedis.js 文件此前一直在监听 chat 频道，readRedis.js 文件接收到 member 后，用它作为条件去 Redis 中去查找，拿到 score 数据

publish.js 文件：
```js
var redis = require("redis");
var client = redis.createClient(6379, "127.0.0.1");
function zadd(key, score, member) {
    client.zadd(key, score, member, function () {
    client.publish("chat", member);//client将member发布到chat这个频道
    //然后订阅这个频道的订阅者就会收到消息
    });
}
for (var i = 0; i < 10; i++) {
    zadd("z", i, "" + i);//发布10次
    console.log("第" + i + "次");
}
```
readRedis.js 文件：
```js
var redis = require("redis");
var client = redis.createClient(6379, "127.0.0.1");
var client1 = redis.createClient(6379, "127.0.0.1");

function getRedisData() {

    //客户端连接redis成功后执行回调
    client.on("ready", function () {
        //订阅消息
        client.subscribe("chat");
        client.subscribe("chat1");
        console.log("订阅成功。。。");
    });
    client.on("error", function (error) {
        console.log("Redis Error " + error);
    });
    //监听订阅成功事件
    client.on("subscribe", function (channel, count) {
        console.log("client subscribed to " + channel + "," + count + "total subscriptions");
    });
    //收到消息后执行回调，message是redis发布的消息
    client.on("message", function (channel, message) {
        console.log("我接收到信息了" + message);
        dealWithMsg(message);
    });
    //监听取消订阅事件
    client.on("unsubscribe", function (channel, count) {
        console.log("client unsubscribed from" + channel + ", " + count + " total subscriptions")
    });
}

function dealWithMsg(message) {
    //按照message查询内容
    client1.zscore("z", message, function (err, reply) {
        console.log(message + "的内容是：" + reply);
    });
}
getRedisData();
```