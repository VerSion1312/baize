在页面内引入css样式一共有三种方式：

- 内部样式
- 外部样式
- 行内样式

以下是一个示例：

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>

    <!--内部样式-->
    <style>
        h1{
            color: green;
        }
    </style>

    <!--外部样式-->
    <link rel="stylesheet" href="css/style.css" />
</head>
<body>

<!--优先级：就近原则-->
<!--行内样式：在标签元素中，编写一个style属性，编写样式即可-->
<h1 style="color: red">这是标签</h1>
</body>
</html>
```



其中，外部样式导入时有两种方式：

- 链接式

  > ```html
  > <link rel="stylesheet" href="css/style.css" />
  > ```

- 导入式

  > ```html
  > <style>
  >     @import url("css/style.css");
  > </style>
  > ```
  >
  > 此种方式是css2.1才有的