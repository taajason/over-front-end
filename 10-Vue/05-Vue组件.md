## 一 Vue组件创建

贴士：模板中的元素必须只有一个根元素（外围包裹元素）。

#### 1.1 创建方式一:extend

```html

    <div id="app">
        <my-com1></my-com1>                      <!-- 驼峰需要使用-连接 -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        // 创建全局组件
        Vue.component('myCom1', Vue.extend({
            template: '<h3>h3组件</h3>'         // 通过template属性，指定结构
        }));                                     // 这里使用驼峰命名

        let vm = new Vue({
            el: "#app"
        });
    </script>
```

#### 1.2 创建组件方式二：component

```html

    <div id="app">
        <my-com2></my-com2>                      <!-- 驼峰需要使用-连接 -->
    </div>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>
        Vue.component('myCom2', {
            template: '<h3>1111</h3>'
        })
        let vm = new Vue({
            el: "#app"
        });

    </script>
```

#### 1.3 创建组件方式三：template标签
```html

    <div id="app">
        <my-com3></my-com3>                      <!-- 驼峰需要使用-连接 -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <template id="tmp1">
        <h3>组件</h3>
    </template>

    <script>

        Vue.component('myCom3', {
            template: '#tmp1'
        })

        let vm = new Vue({
            el: "#app"
        });

    </script>
```

#### 1.4 创建私有组件

```html
    <div id="app">
        <my-com4></my-com4>                      <!-- 驼峰需要使用-连接 -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>

        let vm = new Vue({
            el: "#app",
            components: {
                myCom4: {
                    template: '<h3>com4</h3>'
                }
            }
        });

    </script>
```

当然这里的template也可以引入一个template标签。

## 二 组件的数据

#### 2.1 组件内使用数据

组件内也可以又data属性，但是该属性是个函数，且必须返回一个对象：
```html
    <div id="app">
        <my-com4></my-com4>                      <!-- 驼峰需要使用-连接 -->
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>

        let vm = new Vue({
            el: "#app",
            components: {
                myCom4: {
                    template: '<h3>{{msg}}</h3>',
                    data: function(){
                        return {
                            msg: '组件内部数据'
                        }
                    }
                }
            }
        });

    </script>
```

#### 2.2 组件切换

除了可以使用自定义true/false方式来切换组件外，vue本身也提供了组件切换机制：
```html
    <div id="app">
        <component :is="'com1'"></component>
    </div>

    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>

    <script>

        Vue.component('com1', {
            template: '<h3>111</h3>'
        });

        Vue.component('com2', {
            template: '<h3>222</h3>'
        });

        let vm = new Vue({
            el: "#app"
        });

    </script>
```

#### 2.3 父组件向子组件传值

```html
    <div id="app">
        <son v-bind:parent-msg="msg"></son>
    </div>

    <script>

        let vm = new Vue({
            el: "#app",
            data: {
                msg: "father..."
            },
            components: {
                son: {
                    props: [            // 父组件通过属性传递过来的数据需要在这里再次定义才能使用
                        'parentMsg'     // 下滑线对应大写     
                    ],
                    template: '<h4>子组件得到的父组件数据:{{parentMsg}}</h4>'
                }
            }
        });

    </script>
```

注意：
- 父组件的数据并没有传递给子组件的data，而是props
- data中一般放置请求到的数据
- data中的数据都是可读写的，而props中的数据是只读的

父组件也可以向子组件传递方法：`<son @sonfunc="show"></son>`

```html
    <div id="app">
        <son @sonfunc="show"></son>
    </div>

    <script>

        let vm = new Vue({
            el: "#app",
            data: {
                msg: "father..."
            },
            methods: {
                show: function(){
                    console.log("show...")
                }
            },
            components: {
                son: {
                    props: [            // 父组件通过属性传递过来的数据需要在这里再次定义才能使用
                        'parentMsg'     // 下滑线对应大写     
                    ],
                    methods: {
                        myclick: function(){
                            this.$emit('sonfunc');
                        }
                    },
                    template: '<button @click="myclick">点击触发父组件传递过来的事件</button>'
                }
            }
        });

    </script>
```