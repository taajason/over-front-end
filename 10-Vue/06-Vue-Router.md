## 前言

VueRouter是vue可以渐进引入的路由模块，但是既然已经引入了路由模块，项目已经具备了工程化概念，不妨直接使用webpack方式构建vue项目

## 一 vue-cli

vue-cli是vue的脚手架（为vue提供初始化环境的工具）。

安装vue-cli：
```
npm i vue-cli -g
vue                         # 查看是否安装成功
```

生成基础项目：
```
vue init webpack example    # 使用webpack模板生成项目example
```

main.js中的`/* eslint-disable no-new */`用户忽略eslint的检查（因为这里new Vue未被使用）。

## 二 vue-router

#### 2.1 前端路由

前端路由即：根据url分配对应的处理程序。vue-router中，通过管理url，实现组件与url的对应。  

#### 2.2 helloworld

第一步：在main.js中配置vue-router，
```js
import VueRouter from 'vue-router'

// 启用vue-router
Vue.use(VueRouter)
```

第二步：创建一个组件HomeComponent，并在main中引入，完整代码如下
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import App from './App'
import Home from '@/components/Home'

// 启用vue-router
Vue.use(VueRouter)

Vue.config.productionTip = false

let router = new VueRouter({
  routes: [
    {
      path: '/home',
      component: Home
    }
  ]
})

/* eslint-disable no-new */
// 变量名和属性名一致可以直接写为：router
new Vue({
  el: '#app',
  router: router,
  components: { App },
  template: '<App/>'
})

```

第三步：在跟组件App.vue上展示该组件
```js
  <div id="app">
    <router-view></router-view>
  </div>
```

访问：http://localhost:8080/#/home

#### 2.3 router.js

由于路由信息很多，推荐路由信息脱离main.js，单独配置。  

src下新建文件夹router，新建index.js：
```js
import Vue from 'vue'
import VueRouter from 'vue-router'
import Home from '@/components/Home'

// 启用vue-router
Vue.use(VueRouter)

/* eslint-disable no-new */
let router = new VueRouter({
  routes: [
    {
      path: '/home',
      component: Home
    }
  ]
})
export default router
```


## 三 路由的常见配置

#### 3.1 路由模式

路由的默认模式是hash模式：
```js
let router = new VueRouter({
  mode: 'history',                  // 默认是hash
  routes: [
    {
      path: '/home',
      component: Home,
      name: 'Home',                 // 可选项，给路由起个名字Home
      alias: '/index'               // 别名：访问/index渲染Home
    },
    {
      path: '*',                    // 配置在最后，当所有路由都未被匹配到则如何处理
      component: Error

      // redirect会替换掉浏览器中的地址，alias不会
      // redirect: '/index'         // 也可以配置重定向
      // redirect: {path:: '/home'} // 重定向方式二
      // redirect: {name: 'Home'}   // 重定向方式三，name是路由的名字
      // redirect: (to)=>{          // 重定向方式四,to是原来的目标路由
      //     return '/home'         // return值也可以写为  {path:} 或 {name:}
      // }

    }
  ]
})
```

哈希模式时，路由地址这样书写：
```js
  <div id="app">
    <a href="#/home">点击渲染home组件</a>
    <router-view></router-view>
  </div>
```

修改为history模式后，路由地址就可以直接书写为： `<a href="/home">`  

#### 3.2 router-link标签

采用history模式会带来新的问题：该模式下，a连接会引起页面刷新。vue为了解决这个问题，提供了router-link标签。
```js
  <div id="app">
    <router-link to="/home">显示home</router-link>
    <router-view></router-view>
  </div>
```

使用router-link替代a标签的好处：
- router-link在被解析仍然是a标签，但是其默认行为(刷新页面)被阻止了。  
- hash模式下，使用router-link也无需修改跳转地址为#/home。  

router-link的其他设置：  
- 跳转地址也支持动态绑定形式：` :to="home" `，此时home是该组件导出的数据对象
- 也支持对象形式配置：` :to="{path: '/home'}" `
- router-link默认生成的是a标签，也可以生成div、p等标签，添加属性：` tag="div" `，此时div会自动拥有监听点击事件的功能
- 默认的触发组件事件是点击事件，也可以修改为别的事件：添加属性：` event="mouseover" `
- 添加` exact `属性会让样式渲染变为不包含形式（精确匹配）。

贴士1：很多导航中，使用导航标签既包含图标又包含文字，router-link可以这样配置：
```js
<router-link :to="home" tag="li">
    <i><img src=""></i>
    <span>首页</span>
</router-link>
```  

贴士2：router-link生成的li元素，被点击的li上会带有router-link-active的class属性，可以配置激活状态的样式。如果觉得这个class名字很长也可以自己配置新名字：
```js
// 全局配置方式：所有生成的元素的router-link-active都被修改
let router = new VueRouter({
    linkActiveClass: 'isActive',            // 此时class名为isActive
})

// 局部修改：只支持当前组件
<router-link to="/home" active-class="isActive">显示home</router-link>
```

#### 3.3 <router-view>

router-view是组件渲染的地方，他也可以设置很多配置：
```js
<router-view class="center"></router-view>      <!-- 配置样式 -->
```

#### 3.4 嵌套路由

嵌套路由时，父路由不要使用name，name应该设置在默认子路由上
```js
{
    path: '/about',
    component: about,
    children: [
        {                           // 当访问 /about也会默认渲染children第一个
            path: '/work',          // 匹配地址为： /about/work
            component: work,
            name: about
        },
        {
            path: '/tel',          // 匹配地址为： /about/tel
            component: tel,
            name: tel
        }
    ]
}
```

注意：需要设置渲染在哪个位置，即填写router-view标签
```js
<ul class="nav">
    <!-- 默认渲染 about/work, exact如果不加，则会模糊匹配，点击about/tel时，第一个link的样式也是被点击状态 -->
    <router-link to="about" exact tag="li">         
        <a>work</a>
    </router-link>
    <router-link to="about/tel" exact tag="li">
        <a>tel</a>
    </router-link>
</ul>
```