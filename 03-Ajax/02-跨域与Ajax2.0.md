## 一 跨域
#### 1.1 跨域的产生
如果一个域下的文件请求了和它不一样域（IP、端口、协议、二级域名）的资源，就会产生跨域请求。
跨域解决方案：
- iframe：包含跨域的文件，但无法对其内部进行dom操作、处理数据
- 后台请求：比如让本地php请求跨域资源，然后ajax访问本地的php；
- Flash
- -JSONP：json with padding
#### 1.2 解决跨域问题方式一 JSONP
script标签具备跨域的特性，即可以获取不同域下的文件。
比如使用script标签引入cdn上的jquery文件就是利用了script标签的跨域功能。
原理：由服务端返回一个预先定义好的Javascript函数的调用，并且将服务器数据以该函数参数的形式传递过来。

实现步骤：
服务端现在有一个数据[1,2,3]，现在前端通过script标签拿到了，可以使用变量名直接保存，但是这样产生了一个全局变量，更好的做法是用函数包裹起来：fn([1,2,3])

在资源加载进来之前定义一个函数，这个函数接收一个参数（数据），函数里利用这个参数进行数据处理；
通过script标签加载对应的远程文件资源，当远程文件资源被加载进来的时候，就会去执行前面定义好的函数，并且把资源数据当做参数传入。
```javascript
<script>
    function fn(data) {
        console.log(data);
    }
</script>
<script src="1.txt"></script>
```

此时仍然有问题，因为src加载文件是打开网页就会立即执行了该函数，我们需要按需加载。那么这里我们可以使用动态创建script标签的形式来解决该问题。

前端代码:注意,域名不同，核心是 通过script标签的src属性提交get请求
```javascript
<script type="text/javascript">
function fn(data){
    console.log(data);
}
</script>
<script type="text/javascript" src='http://www.section02.com/seciton02_jsonP.php?callback=fn'>
</script>
```
后台代码：
```php
<?php 
// echo "alert('天气不错哦')";
$callBack = $_GET['callback'];
$arr = array(
    'name' =>'西兰花' ,
    'color' =>'red' 
);
echo $callBack."(".json_encode($arr).")";
?>
```

#### 1.3 jQuery中的跨域
dataType: 'jsonp' 设置dataType值为jsonp即开启跨域访问。
jsonp 可以指定服务端接收的参数的“key”值，默认为callback
jsonpCallback 可以指定相应的回调函数，默认自动生成



## Ajax2.0
#### 2 cros
