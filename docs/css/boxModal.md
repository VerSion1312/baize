## 1. 什么是 CSS 盒模型?

页面布局中，一个元素的外边距（margin）、 边框（border）、内边距（padding）、内容（content）组成一个盒模型。盒模型可分为**标准盒模型** 和 **代替IE盒模型**。

## 2. 标准盒模型

在标准模型中，如果你给盒设置 `width` 和 `height`，实际设置的是内容区域( *content box*)的宽高。 `padding` 和 `border` 再加上设置的宽高一起决定整个盒子的大小。

**示例：**

```
.box {
  width: 100px;
  height: 50px;
  margin: 10px;
  padding: 25px;
  border: 5px solid black;
}
```

如果使用标准盒模型，元素总宽度 = 160px（100+25+25+5+5），总高度 = 110px (50 + 25 + 25 + 5 + 5)，即内容区域`content box`加 `padding` 和 `border` 。

**注**: margin 不计入实际大小 —— 当然，它会影响盒子在页面所占空间，但是影响的是盒子外部空间。

## 3. IE盒模型

你可能会认为盒子的大小还要加上边框和内边距，这样很麻烦。CSS 还有一个 IE 盒模型。使用这个模型，所有宽度都是可见宽度，所以内容宽度是该宽度减去边框和填充部分。使用上面相同的样式得到总宽高是 width = 100px, height = 50px。

默认浏览器会使用标准盒模型。如果需要使用 IE 盒模型，您可以通过为其设置 `box-sizing: border-box` 来实现。 这样就可以告诉浏览器使用 `border-box` 来定义区域，从而设定您想要的大小。

```
.box {
  box-sizing: border-box;
}
```

## 4. box-sizing 属性

CSS 中的 **`box-sizing`** 属性用于告诉浏览器如何计算一个元素是总宽度和总高度

<iframe class="interactive" frameborder="0" height="400" src="https://interactive-examples.mdn.mozilla.net/pages/css/box-sizing.html" title="MDN Web Docs Interactive Example" width="100%" style="box-sizing: border-box; margin: 1.25rem 0px 0px; width: 703.703px; height: auto; aspect-ratio: 16 / 9; color: rgb(22, 25, 29); font-family: system-ui, -apple-system, BlinkMacSystemFont, &quot;Segoe UI&quot;, Helvetica, Arial, sans-serif, &quot;Apple Color Emoji&quot;, &quot;Segoe UI Emoji&quot;; font-size: 16px; font-style: normal; font-variant-ligatures: normal; font-variant-caps: normal; font-weight: 400; letter-spacing: normal; orphans: 2; text-align: start; text-indent: 0px; text-transform: none; widows: 2; word-spacing: 0px; -webkit-text-stroke-width: 0px; white-space: normal; background-color: rgb(255, 255, 255); text-decoration-thickness: initial; text-decoration-style: initial; text-decoration-color: initial;"></iframe>



在 [CSS 盒模型](https://developer.mozilla.org/zh-CN/docs/CSS/Box_model) 的默认定义里，你对一个元素所设置的 [`width`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/width) 与 [`height`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/height) 只会应用到这个元素的内容区。如果这个元素有任何的 [`border`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/border) 或 [`padding`](https://developer.mozilla.org/zh-CN/docs/Web/CSS/padding) ，绘制到屏幕上时的盒子宽度和高度会加上设置的边框和内边距值。这意味着当你调整一个元素的宽度和高度时需要时刻注意到这个元素的边框和内边距。当我们实现响应式布局时，这个特点尤其烦人。

box-sizing 属性可以被用来调整这些表现:

- `content-box` 是默认值。如果你设置一个元素的宽为 100px，那么这个元素的内容区会有 100px 宽，并且任何边框和内边距的宽度都会被增加到最后绘制出来的元素宽度中。

  - 尺寸计算公式：

    `width` = 内容的宽度

    `height` = 内容的高度

- `border-box` 告诉浏览器：你想要设置的边框和内边距的值是包含在 width 内的。也就是说，如果你将一个元素的 width 设为 100px，那么这 100px 会包含它的 border 和 padding，内容区的实际宽度是 width 减去(border + padding)的值。大多数情况下，这使得我们更容易地设定一个元素的宽高。

  - 尺寸计算公式：

    *`width` = border + padding + 内容的宽度*

    *`height` = border + padding + 内容的高度*