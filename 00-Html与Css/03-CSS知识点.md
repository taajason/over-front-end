## 一 居中对齐
#### 1.1 内容水平居中text-align:center
该属性可以让盒子内的内容居中，包括：文字、行内元素、行内块元素，如下所示span被居中：
```html
#div {
    background-color: aquamarine;
    text-align: center;
    width: 500px;
}
<div id="div">
    <span>天安门</span>
</div>
```
注意：text-align:center只能让内容水平居中，让盒子有了高度，不能垂直居中。
文字居中的办法：
- 给文字设置标签，然后用定位。（比较精准，但是麻烦）
- text-align: center;					 (方便，但是不能精准控制)
- 给定两边padding或者margin。（比较方便，精准控制）
#### 1.2 块级元素盒子居中 margin:0 auto;
```
margin:0 auto; 	能让盒子本身水平居中，0是上下外边距
margin:auto;    上下左右都是居中
```
原理：auto是自动充满的意思，当书写margin-left:auto; 左侧自动充满，盒子会在右边出现，当书写margin-right:auto; 右侧自动充满，盒子会在左侧出现。如果左右都自动充满，盒子自然就居中了。
当然，在一些公司盒子水平居中会直接书写：
```
margin-left:atuo;
margin-right:auto;
```
这样让左右都自动填充，与题目中的写法道理一样，但是可以省略解析上下 0 的步骤。
#### 1.3 非标准流居中
脱标（浮动、定位等）的盒子，margin:0 atup 完全失效。
居中办法：left:50% ,再往左走自己宽度的一半
```
left:50%;
margin-left:-宽度一半
```
## 二 内外边距误区
#### 2.1 平级盒子外边距合并
```html

<div class="div1">div1</div>
<div class="div2">div2</div>

.div1 {
    width: 100px;
    height: 100px;
    background: aqua;
    margin-bottom: 100px;
}
.div2 {
    width: 100px;
    height: 100px;
    background: yellowgreen;
    margin-top: 100px;
}
```
此时div1和div2的边距并不是200px，而时100px，这是浏览器本身的问题，一般开发中直接避免同时使用外边距即可。
#### 2.2 父子盒子外边距塌陷
如图所示，开发中经常遇到图中父子嵌套的盒子，要求子盒子往下移动一点距离，可以修改父盒子的内边距或者修改子盒子的外边距。
![](/images/JavaScript/css-03.png)
如果添加外边距 margin-top: 100px;，那么会造成下面意向不到的效果：父子盒子一起往下移动了。
![](/images/JavaScript/css-04.png)
注意：外边距塌陷只会存在于垂直外边距上。
解决方案：
- 方案一：给父盒子指定1像素的上边框或者内边距
- 方案二：给父盒子添加overflow:hidden(触发bfc)
完整代码：
```html
<div class="father">
    <div class="son">son div</div>
</div>

    <style>
        .father {
            width: 300px;
            height: 300px;
            background: aqua;
            margin-bottom: 100px;
            overflow: hidden;
        }
        .son {
            width: 100px;
            height: 100px;
            background: yellowgreen;
            margin-top: 100px;
        }
    </style>

```
#### 2.3 子盒子内边距没有撑开父盒子
很多时候，我们需要如下场景：子盒子son的内容往右移动一点：
![](/images/JavaScript/css-04.png)
常见的做法是：给子盒子内边距，但是我们会担心给了子盒子内边距是否会撑开父盒子。
```html
<div class="father">
    <div class="son">son div</div>
</div>

<style>
    .father {
        width: 200px;
        height: 200px;
        background: aqua;
    }
    .son {
        background: yellowgreen;
        padding-left: 50px;
    }
</style>
```
事实上没有撑开父盒子，因为子盒子没有宽度，默认等于父盒子宽度。
## 三 CSS可见性与overflow
```
overflow:hidden     将容器中超出部分隐藏   
display: none;      将页面中的元素进行隐藏，不占位置（block为显示）
visibility:hidden   隐藏盒子，占据位置
opacity:0         	隐藏盒子，占据位置
position/top/left/...-999px 隐藏盒子，而且占位置。
text-indent:        （文本缩进）可以实现内容移除效果
```
overfllow的解释：
```
overfllow:visible;  默认值，内容不会被修剪，会超出元素框
overfllow:hidden;   内容被修剪，超出隐藏；
overfllow:scroll;   内容被修剪，超出以滚动条查看；
overfllow:auto;     如果内容出现修剪，则滚动条查看。
```
## 四 盒子宽高计算
盒子的宽高计算非常常见，实际工作中，经常使用像素值来确定width和height。
盒子的计算规范：
```
盒子height/width = 内容height/width + padding + border + margin
内容height/width = 内容height/width + padding + border
```
注意：
- 宽高计算只适用于块级元素，对行内元素无效，img和input除外。
- 计算盒子模型总高度，还要考虑两个盒子的垂直外边距合并
- 如果一个盒子没有给定宽度/高度，那么padding不会影响盒子大小。
## 五 规避脱标流
浮动、绝对定位、固定定位都会造成脱标，但是
- 浮动可以清除浮动影响；
- 固定定位在浏览器上不会随着屏幕滚动而动
- 绝对定位会以有相对定位的父级左上角为原点

在网页布局中，布局优先标准流，然后考虑浮动和定位。
如果要让一个元素实现模式转化： 优先使用display
如果想让一个块级元素移动到另一侧： margin-left: auto;
## 六 图片相关问题
#### 6.1 图片文字对齐 vertical-align
同时有图片和文字的地方，图片默认和文字底线对齐。
vertical-align用来设置元素的垂直对其方式，默认值是baseline，middle可以实现文字图片垂直居中显示，使用vertical-align起作用的元素只有：行内块元素和table。
#### 6.2 精灵图
精灵图是一种处理网页背景图像的方式（背景图片），可以给一个元素将精灵图设置为背景图片，使用坐标找到指定的背景图片，减少请求压力
浏览器坐标系：圆点往右为正，圆点往上移动为负
## 七 滑动门
小知识：white-space:nowrap; 可以强制在同一行显示所有文本，直到文本结束或者遇到br标签。
需求：导航栏目中，不会随着文字的增加而变形。
```html
<a href="#">
    <span>首页2222222333</span>
</a>

<style>
    span {
        display: inline-block;
        height: 30px;
        background: url("sabar.jpg") no-repeat right;
        padding: 5px 10px 5px 10px;
    }
</style>
```
## 八 布局
#### 8.1 版心
版心：即可视区，指网页中主题内容所在的区域，一般在浏览器窗口水平居中显示，常见的宽度值是960px,980px,1000px,1200px。
#### 8.2 通栏
不设宽度的div很容易只做通栏，但在很多场景下，通栏时，内部的文字图片内容仍然是和版心位置一致的，比如常见的导航。
实现方案：导航通栏，导航内部设置一个盒子和版心一致。

