## 五 操作符
#### 5.1 操作符分类
算术运算符：
- 一元运算符：正号、负号、++、--、平方等一个变量就能运算
- 二元运算符：+ - * / % 等两个变量才能运算
- 三元运算符：表达式？值1：值2;（表达式成立取值1，不成立取值2）
逻辑运算符：|| && !
比较运算符：<  >  ==  >=  <=
赋值运算符：=、+=、-=、*=、/=、%=
#### 5.2 优先级
1   () 
2   !、-（负数）、++、-- （正数省略+）（一元运算）
3   *、/、% 
4   +、- （加，减）（二元运算）
5   <、<=、<、>= （一级逻辑运算）
6   ==、!=、===、!==、 （二级逻辑运算）
7   && （三级级逻辑运算）
8   || 
9   ?: （三元运算）
10  =、+=、-=、*=、/=、%= （赋值运算）
#### 5.3 自增自减
```javascript
// i++:当i++参与运算，先将i原来的值赋值给变量，自己再加1（先赋值后计算）
// ++i:当++i参与运算，先将变量加1，然后将加1后的值赋值给另外一个变量 （先计算后赋值）

var i = 1;
var m = i++;
console.log(m); //1

var j = 1;
var n = ++j;
console.log(n)  //2
```
## 六 流程控制
#### 6.1 if判断
```
if(条件1){程序1}
if(条件1){程序1}else{程序2}
if(条件1) 都是·{程序1}else if(条件2){程序2}...else{程序n}
```
#### 6.2 switch判断
```
switch (值1) {
    case value1: 
        程序1；
        break;
    case value2: 
        程序2；
        break;
    default: 
        程序3；
}
```
注意：
break可以省略，如果省略，代码会继续执行下一个case；
switch 语句在比较值时使用的是全等操作符，因此不会发生类型转换
（例如，字符串 "10" 不等于数值 10）；
switch中的变量数据类型必须和case后面值的数据类型一致。
#### 6.3 for循环
```javascript
for(var i = 0; i < 10; i++){

}
```
#### 6.4 while循环
```javascript
var i = 10;
while(i <= 10) {
    console.log(i);
    i++
}
```
#### 6.5 do while循环
```javascript
do {
    console.log(1);
} while( i > 10){
    console.log(2);
}
```
代码首先执行do中的代码语句.然后判断条件是否满足。如果条件满足，那么继续执行do中的代码语句。否则不再执行。
#### 6.6 break与continue
在循环中使用了break，代码立马结束本循环（循环不再执行）；
continue语句以后的代码不再执行，当程序遇到continue会立马结束本次循环，进入到一下次循环。
## 七 函数
#### 7.1 函数声明
```javascript
//方式一：直接量声明
function fn1(){
    console.log("函数1");
}
fn1();

//方式二：Function关键字声明
var fn2 = new Function("console.log('函数2')");
fn2();

//方式三：函数表达式
var fn3 = function(){
    console.log("函数3");
}
fn3();
```
#### 7.2 全局变量和局部变量
全局变量：又称成员变量，在哪里都可访问到，如进入script立即定义的变量和没有var的变量。
局部变量：函数内部的变量，只有函数内部可以访问到。
隐式全局变量：隐藏的全局变量不好被发现。
```javascript
function fn() {
    // b和c就是隐式全局变量（等号）
    var  a  =  b  =  c  =  1;  
    // b和c就是隐式全局变量（分号） 
    var  a = 1;  b = 2;  c = 3;     
}
```
#### 7.3 变量提升与预解析
在函数体内部声明变量，会把该声明提升到函数体的顶端， 是只提升声明、不赋值。
```javascript
function fn(){
    console.log(num);   //输出undefined。因为num其实在console上已经声明
    var num = 1;
}
fn();
```
变量提升：在预解析的时候，成员变量和函数被提升到最高位置，方便其他程序访问。
变量提升特点：成员变量只提升变量名，不提升变量值，但是函数会提升所有内容。
当使用的变量在定义变量之前时，很容易出现变量提升。
#### 7.4 参数
arguments存储的是传递过来的实参，JS在创建函数的同时，会在函数内部创建一个arguments对象实例，arguments对象只有函数开始时才可用。
arguments对象并不是一个数组，访问单个参数的方式与访问数组元素的方式相同。
arguments对象的长度由实参个数决定，而不是由形参个数决定。
```javascript
function fn(a,b){
    console.log(fn.length);     //输出：函数的形参的个数 2
    console.log(arguments);     //输出：{ '0': 1, '1': 2 }
    console.log(arguments.length); // 输出实参个数2
}
fn(1,2);
```
#### 7.5 返回值
在函数内部用return来返回计算结果，一个函数只能返回一个值，同时会终止代码的执行。
如果函数没有显式的使return语句 ，那么函数有默认的返回值：undefined；
如果函数使用return，但return后面没有任何值，函数返回值也是：undefined；
#### 7.6 函数加载问题
JS加载的时候，只加载函数名，不加载函数体。所以如果想使用内部的成员变量，需要调用函数。
#### 7.7 匿名函数
匿名函数就是没有名字的函数，书写相对简洁。
匿名函数的调用有三种方法：
- 直接调用或自调用。(function(){alert(1)})()
- 事件绑定。
- 定时器。
## 八 数组
#### 8.1 数组的使用
```javascript
  var arr1 = [1,3,5,7,9];
  var arr2 = new Array(1,3,5);
  console.log(arr1.length);
  console.log(arr2[2]);
```
#### 8.2 数组的增删排序
```javascript
puhs();     //数组最末位添加元素
unshift();  //数组最前面添加元素
pop();      //删除数组最末位元素
shift();    //删除数组第一个元素
reverse();  //数组反转
sort();     //从小到大依次排序  
slice(i,j); //从第i个取到第j个  
splice(m,n); //删除数组中从第m个元素开始，取出n个组成新数组
```
#### 8.3 数组常见API
```javascript

arr1 instanceof arr2;//可以判断数组A是否属于数组B，结果为布尔；
Array.isArray(arr);  //H5新API，判断是否为数组，结果为布尔。 

arr.toString();     //数组转字符串，每一项逗号分隔
arr.valueOf();      //同上用法，但是返回数组对象本身
//案例：
var arr = [1,2,3];
console.log(arr.valueOf());     //[1,2,3]
console.log(arr.toString());    //1,2,3  
var arr = [123,456,789];
console.log(arr.join("abc"));   //123abc456abc789

//数组合并concat
var arr1 = [123,456,789];
var arr2 = ["ab","张三"];
var arr3 = arr1.concat(arr2);
```
## 九 JS语法中的坑
#### 9.1 NaN undefined
```javascript
console.log(“abc”/18);  //结果是NaN
```
undefined和任何数值计算为NaN;
NaN 与任何值都不相等，包括 NaN 本身;

null和undefined有最大的相似性。看看null == undefined的结果(true)也就更加能说明这点。但是null ===undefined的结果(false)。

和数字运算时，
10 + null			结果为：10；
10 + undefined	结果为：NaN。

任何数据类型和undefined运算都是NaN;
任何值和null运算，null可看做0运算。


Boolean类型中：true数值为1；false为0；
null的数值类型为0；
undefined无数值类型或者为NaN;