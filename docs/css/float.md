## 1. 标准文档流

块级元素：独占一行 h1~h6 、p、div、 列表…

行内元素：不独占一行 span、a、img、strong

>  注： 行内元素可以包含在块级元素中，反之则不可以。

## 2. display

属性值：

1. block：块元素

2. inline：行内元素

3. inline-block：是块元素，但是可以内联，在一行

4. none：不显示


> display也是一种实现行内元素排列的方式，但是我们很多情况用float

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <!--block 块元素
        inline 行内元素
        inline-block 是块元素，但是可以内联 ，在一行
    -->
    <style>
        div{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            display: inline-block;
        }
        span{
            width: 100px;
            height: 100px;
            border: 1px solid red;
            display: inline-block;
        }
    </style>
</head>
<body>
<div>div块元素</div>
<span>span行内元素</span>
</body>
</html>
```



## 3. float:left/right左右浮动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="css/style.css" type="text/css">
</head>
<body>
<div id="father">
    <div class="layer01"><img src="images/1.png" alt=""></div>
    <div class="layer02"><img src="images/2.png" alt=""></div>
    <div class="layer03"><img src="images/3.png" alt=""></div>
    <div class="layer04">
        浮动的盒子可以向左浮动，也可以向右浮动，知道它的外边缘碰到包含或另一个浮动盒子为止
    </div>
</div>
</body>
</html>
```

```css
div{
    margin: 10px;
    padding: 5px;
}
#father{
    border: 1px #000 solid;
}
.layer01{
    border: 1px #F00 dashed;
    display: inline-block;
    float: left;/*向左浮动*/
    clear: both;/*清楚浮动*/
}
.layer02{
    border: 1px #00F dashed;
    display: inline-block;
    float: left;
    clear: both;
}
.layer03{
    border: 1px #060 dashed;
    display: inline-block;
    float: left;
    clear: both;
}
.layer04{
    border: 1px #666 dashed;
    font-size: 12px;
    line-height: 23px;
    float: left;
    clear: both;
}
```



## 4. overflow以及父级边框塌陷

> right：右侧不允许有浮动元素
> left：左侧不允许有浮动元素
> both：两侧不允许有浮动元素
> none：

解决塌陷问题方案：
方案一：增加父级元素的高度；

```css
#father{
    border: 1px #000 solid;
    height: 800px;
}
```

方案二：增加一个空的div标签，清除浮动

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <link rel="stylesheet" href="css/style.css" type="text/css">
</head>
<body>
<div id="father">
    <div class="layer01"><img src="images/1.png" alt=""></div>
    <div class="layer02"><img src="images/2.png" alt=""></div>
    <div class="layer03"><img src="images/3.png" alt=""></div>
    <div class="layer04">
        浮动的盒子可以向左浮动，也可以向右浮动，知道它的外边缘碰到包含或另一个浮动盒子为止
    </div>
    <div class="clear"></div>
</div>
</body>
</html>
```

```css
div{
    margin: 10px;
    padding: 5px;
}
#father{
    border: 1px #000 solid;
    height: 800px;
}
.layer01{
    border: 1px #F00 dashed;
    display: inline-block;
    float: left;/*向左浮动*/
}
.layer02{
    border: 1px #00F dashed;
    display: inline-block;
    float: left;
}
.layer03{
    border: 1px #060 dashed;
    display: inline-block;
    float: right;
}
/*
clear：right；右侧不允许有浮动元素
clear：left； 左侧不允许有浮动元素
clear：both； 两侧不允许有浮动元素
clear：none；
 */
.layer04{
    border: 1px #666 dashed;
    font-size: 12px;
    line-height: 23px;
    display: inline-block;
    float: right;
    clear: left;
}
.clear{
    clear: both;
    margin: 0;
    padding: 0;
}
```

```html
<div class = "clear"></div>
<style>
	.clear{
		clear:both;
		margin:0;
		padding:0;
}
</style>
```

方案三：在父级元素中增加一个overflow：hidden

```css
overflow:hidden/*隐藏*/
overflow：scoll/*滚动*/
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <style>
        #content{
            width: 200px;
            height: 150px;
            overflow: scroll;
        }
    </style>
</head>
<body>

<div id="content">
    <img src="images/1.png" alt="">
    <p>
        this is a paragraph
    </p>
</div>
</body>
</html>
```

方案四：父类添加一个伪类:after

```css
#father:after{
	content:'';
	display:block;
	clear:both;
}
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
    	div{
            margin: 10px;
            padding: 5px;
        }
        #father{
            border: 1px #000 solid;
        }
        #father:after{
            content: '';
            display: block;
            clear: both;
        }
        .layer01{
            border: 1px #F00 dashed;
            display: inline-block;
            float: left;/*向左浮动*/
        }
        .layer02{
            border: 1px #00F dashed;
            display: inline-block;
            float: left;
        }
        .layer03{
            border: 1px #060 dashed;
            display: inline-block;
            float: right;
        }
        /*
        clear：right；右侧不允许有浮动元素
        clear：left； 左侧不允许有浮动元素
        clear：both； 两侧不允许有浮动元素
        clear：none；
         */
        .layer04{
            border: 1px #666 dashed;
            font-size: 12px;
            line-height: 23px;
            display: inline-block;
            float: right;
        }
    </style>
</head>
<body>
<div id="father">
    <div class="layer01"><img src="../lesson06/images/1.png" alt=""></div>
    <div class="layer02"><img src="images/2.png" alt=""></div>
    <div class="layer03"><img src="images/3.png" alt=""></div>
    <div class="layer04">
        浮动的盒子可以向左浮动，也可以向右浮动，知道它的外边缘碰到包含或另一个浮动盒子为止
    </div>
    <div class="clear"></div>
</div>
</body>
</html>
```



## 5. display与float的对比

1. **display：**方向不可以控制
2. **float：**浮动起来的话会脱离标准文档流，所以要解决父级边框塌陷的问题。