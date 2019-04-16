## 一 graphicsmagick 图片处理
一般使用graphicsmagick软件在服务端处理图片，安装完毕后还需要设置环境变量。
```
//格式转换
gm convert a.bmp a.jpg
//更改当前目录下*.jpg的尺寸大小，并保存于目录.thumb里面
gm mogrify -resize 320x200 danny.jpg
//在Node中使用
npm i gm -S
//头像剪裁案例：
gm("./danny.jpg").crop(141,96,152,181).write("./2.jpg",function(err){
     //141  96 是宽高 。  152  181是坐标
});

```