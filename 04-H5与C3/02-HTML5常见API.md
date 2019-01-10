## 一 Web存储
web存储即把数据存储在浏览器中。传统方式我们以document.cookie来进行存储的，但是由于其存储大小只有4k左右，并且解析也相当的复杂，HTML5规范则提出解决方案：Storage 存储（WebSQL、IndexDB已经被w3c 放弃了）。分别有两种存储方式：
```
会话存储：
    设置：window.sessionStorage.setItem("name","lisi");
    获取：window.sessionStorage.getItem("name");
    删除：window.sessionStorage.removeItem("name");
    清除：window.sessionStorage.clear();
    说明：生命周期为关闭浏览器窗口,在同一个窗口下数据可以共享，可存储大约5M
    
本地存储：
    获取：window.localStorage
    获取：window.localStorage.getItem("name");
    删除：window.localStorage.removeItem("name");
    清除：window.localStorage.clear();
    说明：永久生效，除非手动删除,可以多窗口共享,可存储大约20M
```
## 二 历史管理
## 三 地理位置
HTML5提供了Geolocation地理位置支持。目前大多数浏览器都可以支持（IE9+）。  
出于安全考虑，部分最新的浏览器只允许通过HTTPS协议使用GeolocationAPI，在http协议下回跑出异常，当然本地开发不会有问题。  
访问地理位置方式：
```javascript
navigator.geolocation           //会提示用户是否允许获取该API的权限

//判断浏览器是否兼容代码
if (navigator.geolocation ) {
    //获取地理位置后执行的操作
} else { 
    //没有获取到地理位置执行的操作
 }
```
## 其他API
#### 检测用户网络状态
我们可以通过window.onLine来检测，用户当前的网络状况，返回一个布尔值
```js
window.online       //用户网络连接时被调用
window.offline      //用户网络断开时被调用
window.addEventListener("online",function(){
    alert("已经建立了网络连接")
});
window.addEventListener("offline",function(){
    alert("已经失去了网络连接")
});
```












