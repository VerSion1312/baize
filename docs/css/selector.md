## 1. 基本选择器

1. 选择一类标签

   > 格式为：
   >
   > 标签{
   >
   > }

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <style>
           h1{
               color: orange;
               background: blue;
               border-radius: 10px;
           }
       </style>
   </head>
   <body>
   <h1>标签选择器</h1>
   </body>
   </html>
   ```

2. 类选择器

   > 格式为
   >
   > .类名{
   >
   > }

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <style>
           /*  类选择器的格式 .class的名称{}
               好处：可以多个标签归类，是同一个class，可以复用
           */
           .demo1{
               color: blue;
           }
           .demo2{
               color: red;
           }
           .demo3{
               color: aqua;
           }
       </style>
   </head>
   <body>
   <h1 class = "demo1">类选择器：demo1</h1>
   <h1 class="demo2">类选择器：demo2</h1>
   <h1 class="demo3">类选择器：demo3</h1>
   </body>
   </html>
   ```

3. id选择器

   > 此选择器要求id全局唯一，格式为：
   >
   > #id{
   >
   > }

   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <title>Title</title>
       <style>
           /*  id选择器：id必须保证全局唯一
               #id名称{}
               不遵循就近原则，优先级是固定的
               id选择器 > 类选择器  >  标签选择器
           */
           #demo1{
               color: aqua;
           }
           .demo2{
               color: red;
           }
           #demo2{
               color: orange;
           }
           h1{
               color: blue;
           }
       </style>
   </head>
   <body>
   <h1 id="demo1">id选择器：demo1</h1>
   <h1 class="demo2" id = "demo2">id选择器：demo2</h1>
   <h1 class="demo2">id选择器：demo3</h1>
   <h1>id选择器：demo4</h1>
   <h1>id选择器：demo5</h1>
   </body>
   </html>
   ```



## 2. 层次选择器

1. 后代选择器

   ```html
   /*后代选择器*/
   <style>
   body p{
   	background:gold;
   }
   </style>
   ```

2. 子代选择器

   > 此选择器只会选择下一代子元素

   ```html
   /*子选择器*/
   <style>
   body>p{
   	background:orange;
   }
   </style>
   ```

3. 相邻兄弟选择器

   ```html
   /*相邻兄弟选择器：只有一个，相邻（向下）*/
   <style>
   .active+p{
   background: red
   }
   </style>
   
   <body>
   	<p class="active">p1<p>
   	<p>p2</p>
   </body>
   ```

4. 通用选择器

   ```html
   <style>
   /*通用兄弟选择器，当前选中元素的向下的所有兄弟元素*/
   	.active~p{
   	background:red;
   }
   </style>
   <body>
   	<p class="active">p1<p>
   	<p>p2</p>
   </body>
   ```

   

## 3. 结构伪类选择器

```html
<style>
        /*ul的第一个子元素*/
        ul li:first-child{
            background: aqua;
        }

        /*ul的最后一个子元素*/
        ul li:last-child{
            background: blue;
        }
        /*选中p1：定位到父元素，选择当前的第一个元素
            选择当前p元素 的父级元素，选中父级元素的第一个，并且是当前元素才生效！
        */
        p:nth-child(1){
            background: orange;
        }


        /*选中父元素下的，第2个p元素*/
        p:nth-of-type(2){
            background: red;
        }
    </style>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <!--避免使用，class，id选择器-->
    <style>
        /*ul的第一个子元素*/
        ul li:first-child{
            background: #a13d30;
        }
        /*ul的最后一个子元素*/
        ul li:last-child{
            background: red;
        }

        /*选中p1：定位到父元素，选择当前的第一个元素
        选择当前p元素的父级元素，选中父级元素的第一个子元素为p的
        按顺序
        */
        p:nth-child(2){
            background: blue;
        }

        /*选中父元素下的p元素的第二个，按类型*/
        p:nth-of-type(1){
            background: yellow;
        }

        a:hover{
            background: black;
        }
    </style>
</head>
<body>
    <!--<h1>h1</h1>-->
    <p>p1</p>
    <p>p2</p>
    <p>p3</p>
    <ul>
        <li>li1</li>
        <li>li2</li>
        <li>li3</li>
    </ul>

    <a href="">链接标签</a>
</body>
</html>
```



## 4. 属性选择器

id+class结合使用，更准确。

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <style>
         .demo a{
            display: block;
            height: 50px;
            width: 50px;
            float:left;
            border-radius: 10px;
            background: blue;
            text-align: center;
            color: beige;
            text-decoration: none;
            margin-right: 5px;
            font: bold 20px/50px Arial;
        }
         /*属性名，属性名=属性值（正则）
         =表示绝对等于
         *=表示包含
         ^=表示以...开头
         $=表示以...结尾
         存在id属性的元素  a[]{}
         */
        /* a[id]{
             background: red;
         }*/

         /*id=first的元素*/
       /* a[id=first]{
            background: aqua;
        }*/

        /*class中有links元素*/
       /* a[class = "links item2 first2"]{
            background: orange;
        }*/
        /*a[class*="links"]{
            background: black ;
        }*/
        /*选中href中以http开头的元素*/
        a[href^="http"]{
            background: orange;
        }
    </style>

</head>
<body>
<p class="demo">
    <a href="http://www.baidu.com" class="links item first" id="first">1</a>
    <a href="/adad/faf" class="links item2 first2" >2</a>
    <a href="qwe123" class="links item3 first3" >3</a>
    <a href="eweqe" class="links item4 first4" >4</a>
    <a href="rrrrr" class="links item5 first5" >5</a>
    <a href="ttt" class="links item6 first6" >6</a>
    <a href="yyy" class="links item7 first7" >7</a>
</p>
</body>
</html>
```

> = 绝对等于
>
> *= 包含这个元素
>
> ^= 以这个开头
>
> $= 以这个结尾