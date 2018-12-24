## 一 Hello World
```JavaScript
const koa = require('koa');

let app = new koa();

app.use(async (ctx)=>{
    ctx.body = 'hello world';
});

app.listen(3000);
```
## 二 路由中间件koa-router
#### 2.1 路由创建
Express自带了路由功能，而Koa则没有，需要额外安装koa-router模块：
```JavaScript
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();
const router = new Router({
    prefix:'/test'              //路由前缀---全局的
});

router.get('/',function (ctx,next) {
    ctx.body = 'index....';
});
router.get('/todo',function (ctx,next) {
    ctx.body = 'todo....';
});

app.use(router.routes());		//启动路由中间件
app.use(router.allowedMethods());	//根据ctx.status设置响应头

app.listen(3000);
```
#### 2.2 路由设计
```JavaScript
const Koa = require('koa');
const Router = require('koa-router');

const app = new Koa();

let home = new Router();     //子路由
let page = new Router();     //子路由

//http://localhost:8000/home/test
home.get('/test',async(ctx)=>{        
ctx.body = 'home test...';
}).get('/todo',async(ctx)=>{ 
//http://localhost:8000/home/to do      
ctx.body = 'home todo...';
});

page.get('/test',async (ctx)=>{
   ctx.body = 'page router...';
});


//父路由
let router = new Router();
//装载子路由
router.use('/home',home.routes());
router.use('/page',page.routes());

//加载路由中间件
app.use(router.routes());

app.listen(8000);
```
#### 2.3 koa中间件与Express的不同
Express中的中间件是按照顺序执行的；
koa中的中间件无论写在什么位置，都会先执行。因为Koa的中间件是洋葱模型！
#### 2.4错误处理中间件
```JavaScript
app.use((ctx,next)=>{
    next();
    if(ctx.status == 404){
        ctx.status == 404;
        ctx.body = '404 not found'
    } 
});
```
## 三 请求处理
#### 3.1 GET请求
```JavaScript
const Koa = require('koa');
const app = new Koa();
/*
ctx中封装了两个请求对象：
ctx:koa封装的上下文对象，包含req与res
ctx.request：node原生请求对象，适合做底层开发
 */
app.use(async function(ctx){
    // console.log(ctx.request);
    // console.log(ctx.req);
    console.log(ctx.url);           //login?name=lisi
    console.log(ctx.request.query); //{ name: 'lisi' }
    console.log(ctx.query);         //{ name: 'lisi' }
    console.log(ctx.querystring);   //name=lisi
    ctx.body = 'hello';
});
app.listen(8000);
```
#### 3.2 动态路由 /:
使用 /: 接收参数
```JavaScript
const Koa = require('koa');
const router = require('koa-router')();

let app = new Koa();

router.get('/news/:id/:name', async (ctx)=>{
// localhost:3000/news/1/lisi 输出{"id":"1","name":"lisi"}
    ctx.body = ctx.params;
});

app.use(router.routes());
app.listen(3000);
```

#### 2.3 POST请求
```JavaScript
const Koa = require('koa');
const app = new Koa();

app.use(async function (ctx) {
    if (ctx.url == '/' && ctx.method == 'GET') {
        let html = `<form action="/" method="post">
    <input type="text" id="username" name="username" value="">
    <input type="text" id="password" name="password" value="">
    <input type="submit" value="登录">
</form>`;
        ctx.body = html;
    } else if (ctx.url == '/' && ctx.method == 'POST') {
        let parseData = await parsePostData(ctx);
        ctx.body = parseData;
    } else {
        ctx.body = '404';
    }
});
app.listen(8001,()=>{
    console.log('start...');
});

//解析post方法
function parsePostData(ctx) {
    return new Promise((resolve, reject) => {
        try {
            let postdata = '';
            ctx.req.on('data', (data) => {
                postdata += data
            });
            ctx.req.addListener("end", function () {
                resolve(postdata);
            });
        } catch (error) {
            reject(error);
        }
    });
}

//转换为json方法
function parseQueryStr(queryStr){
    let queryData={};
    let queryStrList = queryStr.split('&');
    console.log(queryStrList);
    for( let [index,queryStr] of queryStrList.entries() ){
        let itemList = queryStr.split('=');
        console.log(itemList);
        queryData[itemList[0]] = decodeURIComponent(itemList[1]);
    }
    return queryData
}
```
#### 2.4 koa-bodyparser中间件处理post
```JavaScript
const Koa = require('koa');
const bodyParser = require('koa-bodyparser');

const app = new Koa();

app.use(bodyParser());
app.use(async (ctx)=>{
    if (ctx.url == '/' && ctx.method == 'GET') {
        let html = `<form action="/" method="post">
    <input type="text" id="username" name="username" value="">
    <input type="text" id="password" name="password" value="">
    <input type="submit" value="登录">
</form>`;
        ctx.body = html;
    } else if(ctx.url == '/' && ctx.method == 'POST'){
        let postData= ctx.request.body;
        ctx.body=postData;
    } else {
        ctx.body = '404';       //{"username":"22222","password":"22555"}
    }
});
app.listen(8001);
```
## 四 模板渲染与EJS
```
cnpm install --save koa-views   //koa对模板的支持需要该中间件
npm install --save ejs
```
```JavaScript
const Koa = require('koa');
const views = require('koa-views');
const path = require('path');
const app = new Koa();

// 加载模板引擎
app.use(views(path.join(__dirname, './views'), {
    extension: 'ejs'
}));

app.use( async ( ctx ) => {
    let title = 'hello koa2';
    await ctx.render('index', {
        title
    });
});

app.listen(3000,()=>{
    console.log('[demo] server is starting at port 3000');
});
```

## 五 静态资源管理 koa-static
```JavaScript
const Koa = require('koa');
const static = require('koa-static');

const app = new Koa();

// http://localhost:3000/1.css
app.use(static(__dirname+ '/public'));

app.use( async ( ctx ) => {
    ctx.body = 'hello world';
});

app.listen(3000, () => {
    console.log('run')
});
```
## 六 cookie
```JavaScript
app.use(async (ctx)=>{
    if(ctx.url === '/index'){
        ctx.cookies.set(
            'MyCookie','Test',{
                //domain:'localhost', // 写cookie所在的域名
                // path:'/index',      // 只在该路径下需要cookie
                maxAge:1000*60*60*24,   // 有效时长
                //expires:new Date('2018-12-31'), // 失效时间
                //httpOnly:false,  // 是否只用于http请求中获取
                //overwrite:false  // 是否允许重写
            }
        );
        ctx.body = 'cookie is ok';
    }else{
        if( ctx.cookies.get('MyCookie')){
            ctx.body = ctx.cookies.get('MyCookie');
        }else{
            ctx.body = 'Cookie is none';
        }
    }
});
```
//注意：koa不支持直接设置cookie值为中文，中文一般使用Buffer转换。
```JavaScript
(new Buffer('李四')).toString('base64');				//汉字转换为base64
(new Buffer('dwadwadwa','base64')).toString();		//base64转换为汉字
```
## 七 session中间件 koa-session
```JavaScript
const Koa = require('koa');
const router = require('koa-router')();
const session = require('koa-session');
let app = new Koa();

//配置session的中间件
app.keys = ['some secret hurr'];   /*cookie的签名*/
const CONFIG = {
    key: 'koa:sess', /**默认 */
    maxAge: 86400000,  /*cookie的过期时间*/
    overwrite: true, /** (boolean) can overwrite or not (default true)没有效果，默认 */
    httpOnly: true, /**  true表示只有服务器端可以获取cookie */
    signed: true, /** 默认 签名 */
    rolling: true, /** 在每次请求时强行设置 cookie，这将重置 cookie 过期时间（默认：false）*/
    renew: false, /** (boolean) renew session when session is nearly expired */
};
app.use(session(CONFIG, app));

router.get('/',async (ctx)=>{
    if (ctx.session.info) {
        ctx.body = ctx.session.info + '登录成功';
    } else {
        ctx.body = '还没设置session';
    }
});

router.get('/set',async (ctx)=>{
    ctx.session.info = 'lisi';
    ctx.body = 'session已设置';
});

app.use(router.routes());
app.listen(3000);
```