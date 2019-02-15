## 1 单例模式
单例就是保证一个类只有一个实例，实现的方法一般是先判断实例存在与否，如果存在直接返回，如果不存在就创建了再返回，这就确保了一个类只有一个实例对象，比如全局缓存、浏览器的window对象。
案例：
单击登录按钮的时候，页面中会出现一个登录框，而这个浮窗是唯一的，无论单击多少次登录按钮，这个浮窗只会被创建一次。因此这个登录浮窗就适合用单例模式。
```js
var SingleObj = (function() {
    var instance;
    function init() {
        // do something
        return {
            // define public methods and properties
        };
    }
    return {
        getInstance: function() {
            if(!instance) {
                instance = init();
            }
            return instance;
        }
    }
})();
```
## 2 工厂模式
工厂模式可以通过传递不同的参数来获取不同的对象，可以解决创建对象过程中代码冗余；
案例代码：
```js
function catFactory(opts){
    var obj = new Object();
    obj.name = opts.name;
    obj.color = opts.color;
    obj.getInfo = function(){
        return '名称：'+obj.name +'， 颜色：'+ obj.color;
    }
    return obj;
}
var cat = catFactory({name: '波斯猫', color: '白色'});
console.log(cat.getInfo());
```
复杂工厂模式：
将其成员对象的实列化推到子类中，子类可以重写父类接口方法以便创建的时候指定自己的对象类型。
父类只对创建过程中的一般性问题进行处理，这些处理会被子类继承，子类之间是相互独立的，具体的业务逻辑会放在子类中进行编写。
实现流程：先设计一个抽象类，这个类不能被实例化，只能用来派生子类，最后通过对子类的扩展实现工厂方法。
```js
var XMLHttpFactory =function(){};　     //这是一个抽象工厂模式

XMLHttpFactory.prototype = {
    //如果真的要调用这个方法会抛出一个错误，它不能被实例化，只能用来派生子类
    createFactory:function(){
        throw new Error('This is an abstract class');
    }
}

var XHRHandler = function(){}; //定义一个子类

// 子类继承父类原型方法
extend( XHRHandler , XMLHttpFactory );

XHRHandler.prototype =new XMLHttpFactory(); //把超类原型引用传递给子类,实现继承

XHRHandler.prototype.constructor = XHRHandler; //重置子类原型的构造器为子类自身

//重新定义createFactory 方法
XHRHandler.prototype.createFactory =function(){
    var XMLHttp =null;
    if (window.XMLHttpRequest){

        XMLHttp =new XMLHttpRequest();

    }else if (window.ActiveXObject){

        XMLHttp =new ActiveXObject("Microsoft.XMLHTTP")
    }

    return XMLHttp;
}
```

优点：　
- 1、弱化对象间的耦合，防止代码的重复。在一个方法中进行类的实例化，可以消除重复性的代码。
- 2、重复性的代码可以放在父类去编写，子类继承于父类的所有成员属性和方法，子类只专注于实现自己的业务逻辑。
缺点：
当工厂增加到一定程度的时候，提升了代码的复杂度，可读性下降。而且没有解决对象的识别问题，即怎么知道一个对象的类型。
## 3 代理模式
代理模式的中文含义就是帮别人做事，javascript的解释为：把对一个对象的访问, 交给另一个代理对象来操作。
## 4 发布订阅模式
定义对象间的一种一对多的依赖关系，以便当一个对象的状态发生改变时，所有依赖于它的对象都得到通知并自动刷新，也被称为是发布订阅模式。
它需要一种高级的抽象策略，以便订阅者能够彼此独立地发生改变，而发行方能够接受任何有消费意向的订阅者。
应用场景：博客园里面有一个订阅的按钮（貌似有bug），比如小A,小B,小C都订阅了我的博客，当我的博客一有更新时，就会统一发布邮件给他们这三个人，就会通知这些订阅者
发布订阅模式的流程如下：
- 1. 确定谁是发布者(比如我的博客)。
- 2. 然后给发布者添加一个缓存列表，用于存放回调函数来通知订阅者。
- 3. 发布消息，发布者需要遍历这个缓存列表，依次触发里面存放的订阅者回调函数。
- 4. 退订（比如不想再接收到这些订阅的信息了，就可以取消掉）
```js
var EventCenter = (function(){
    var events = {};
    /*
    {
      my_event: [{handler: function(data){xxx}}, {handler: function(data){yyy}}]
    }
    */
    // 绑定事件 添加回调
    function on(evt, handler){
        events[evt] = events[evt] || [];
        events[evt].push({
            handler:handler
        })
    }
    function fire(evt, arg){
        if(!events[evt]){
            return
        }
        for(var i=0; i < events[evt].length; i++){
            events[evt][i].handler(arg);
        }
    }
    function off(evt){
        delete events[evt];
    }
    return {
        on:on,
        fire:fire,
        off:off
    }
}());

var number = 1;
EventCenter.on('click', function(data){
    console.log('click 事件' + data + number++ +'次');
});
EventCenter.off('click');   //  只绑定一次
EventCenter.on('click', function(data){
    console.log('click 事件' + data + number++ +'次');
});

EventCenter.fire('click', '绑定');
```

## 5 适配器模式

使原本由于接口不兼容而不能在一起工作的那些类可以一起工作。Adapter，Adaptee,Target.

## 6 装饰模式

不改变基类的情况下，为基类增添新属性和方法。

## 7 观察者模式


