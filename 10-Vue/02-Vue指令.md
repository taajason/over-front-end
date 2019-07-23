## 一 常见指令

#### 1.1 v-cloak 解决闪烁

解决插值表达式闪烁问题：当网速较慢时，`{{}}`等内容会被显示在浏览器上，可以通过`v-cloak`指令解决。

执行过程：

当vue没有请求过来的时候，标签上的 v-cloak 属性对应的css样式起作用（隐藏掉了）；vue请求过来之后，这个属性就移出了，就可以显示对应的内容，不会出现闪烁了。

```js
// css
<style>
    [v-cloak]{
        display: none;
    }
</style>

// html
<div v-cloak>{{msg}}</div>
```

#### 1.2 v-text v-html 解析文本

`v-text`，`v-html`的作用与`{{}}`作用一样，区别：
- 前二者指令没有`{{}}`表达式闪烁问题，但是该指令的属性值会覆盖标签中的内容。  
- `v-html`能够将数据中的标签文本解析出来，其他指令不行

#### 1.3 v-bind 绑定属性
设置某个标签的某个属性的值为变量，当解析的时候，就会通过此变量去找到对应的变量值，然后替换；

v-bind中可以写合法的js表达式：v-bind会把绑定的属性值当做js的代码去解析执行，那么在此处可以进行表达式相关的操作，表达式解析的结果当做实际的属性值去输出；

```js
//html
<input type="button" v-bind:title="mytitle">

//js
new Vue({
    el: "#app",
    data: {
        mytitle: "自定义title"
    }
})
```

注意：
- v-bind可以省略，直接写冒号即可
- v-bind中可以使用表达式： `:title="mytitle + '123'"`

#### 1.4 v-on 绑定事件
每当触发对应事件的时候，就会调用vue的事件绑定机制，然后找到并执行v-on绑定的方法。
```js
//html
<input type="button" v-on:click="show">

//js
new Vue({
    el: "#app",
    data: {
        mytitle: "自定义title"
    },
    methods: {
        show: function(){
            alert(this.mytitle);        // this代表new出来的vm实例
        }
    }
})
```

注意：v-on可以缩写为@  

v-on提供一些事件修饰符，如：`@click.stop="clickHandler"`
- stop:阻止冒泡
- prevent:阻止默认事件
- capture：添加事件侦听器时使用事件捕获模式
- self：只有当事件在该元素本身（非子元素）触发时触发回调
- once：事件只触发一次

self与stop区别：
```js
<div  id="outter">
    <div  id="inner">
        <button id="btn">

        </button>
    </div>
<div>
```

为三个元素都绑定一个点击事件：
- 默认当点击button时，事件冒泡，事件被依次触发三次
- 当btn为stop时，则阻止冒泡，事件只被btn触发
- 当inner为self时，则事件冒泡到inner处不触发，且继续冒泡到outter处触发

#### 1.5 v-model 双向绑定

```js
    <!-- 单向绑定：无法修改界面 -->
    <h4>{{msg}}</h4>

    <!-- 单向绑定：修改input内容不会修改data内容 -->
    <input type="text" :value="msg">

    <!-- 双向绑定：修改input内容不会修改data内容 -->
    <input type="text" v-model="msg">
```

注意：v-model用于表单元素中。

#### 1.6- vue样式支持

内联方式：
```js
//html
<div id="app">
    <h2 :style="{'background-color': 'green'}">test1</h2>
    <h2 :style="styleObj1">test2</h2>
    <h2 :style="[styleObj1,styleObj2]">test3</h2>
</div>

//js
    new Vue({
        el: "#app",
        data: {
            msg: "hello",
            isActive: true,
            styleObj1: {'background-color': 'green' },
            styleObj2: {'font-size': '100px' },
        },
        methods: {

        }
    });

```

分离方式：
```js
    //css
    <style>
        .green {
            background-color: green;
        }
        .big {
            font-weigth: 200;
        }
        .active {
            letter-spacing: 0.5em;
        }
    </style>

    //html
    <div id="app">
        <h2 :class="['green', 'big']">test1</h2>
        <h2 :class="['green', 'big', isActive?'active':'']">test2</h2>
        <h2 :class="['green', 'big', {'active': isActive}]">test3</h2>
        <h2 :class="{ green:true, big:true }">test4</h2>
        <h2 :class="classObj">test5</h2>
    </div>

    //vue
    new Vue({
        el: "#app",
        data: {
            msg: "hello",
            isActive: true，
            classObj: { green:true, big:true }
        },
        methods: {

        }
    });
```

#### 1.7 v-for

```js
<div id="app">
    <h2 v-for="item in arr">{{item}}</h2>
    <h2 v-for="item in 5">{{item}}</h2>         <!-- 从1开始 -->
    <h5 v-for="(item, i) in arr">{{i}}</h5>
</div>

//数据为： arr: ["a","b","c"]
```

注意：2.20版本以上的vue中，key是必须的

#### 1.8 v-if与v-show

```js
    <h2 v-if="flag">test1</h2>          <!-- 会创建/移除元素，切换性能消耗高 -->
    <h2 v-show="flag">test1</h2>        <!-- 只会显示和隐藏:display，初始渲染消耗高 -->
    <button @click="flag=!flag">点击</button>
```