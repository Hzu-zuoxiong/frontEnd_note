# BFC\(Block Formatting Context\)

## 什么是 BFC

BFC 全称为 Block Formatting Context，即块格式化上下文。块格式化上下文是页面 CSS 视觉渲染的一部分，用于决定块盒子的布局及浮动相互影响范围的一个区域。

## BFC 的创建方法

1. 浮动（元素的 float 不为 none）；
2. 绝对定位元素（元素的 position 为 absolute 或 fixed）；
3. 行内块（display: inline-block）；
4. 表格单元格（display: table-cell，HTML 表格单元格默认属性）；
5. overflow 的值不为 visible 的元素；
6. 弹性盒（display: flex 或 inline-flex）；

其中，最常见的为 overflow: hidden、float: left/right、position: absolute。

## BFC 的原理

1. BFC 元素垂直方向的边距发生重叠；
2. BFC 的区域不会与浮动元素的 box 重叠；
3. BFC 就是页面上的一个独立容器，容器里面的子元素不会影响到外面的元素；
4. 计算 BFC 高度时，浮动元素也要参与计算；

## 实际代码演示

```css
<style>
    * {
        margin: 0;
        padding: 0;
    }
    .box {
        background:#000;
        height: 100%;
        margin-left: 50px;
    }
    .left {
        background: red;
        border: 3px solid #F31264;
        width: 200px;
        height: 200px;
        float: left;
    }
    .right {
        background: green;
        border: 3px solid #F31264;
        width:400px;
        min-height: 100px;
    }
</style>

<div class="box">
    <div class="left"></div>
    <div class="right"></div>
</div>
```

显示效果：![](/assets/BFC1.png)可以看出，\('.left'\)红色盒子通过左浮动，脱离了原本的位置，\('.right'\)绿色被定位到黑色父元素左上角，与浮动红盒子发生重叠。

同时，黑盒未创建 BFC，因此在计算高度的时候没有考虑到红盒区域，发生了高度坍塌。

给黑盒设置 overflow: hidden 创建 BFC：

```css
.BFC {
        overflow: hidden;
}

<div class="box BFC">
    <div class="left"></div>
    <div class="right"></div>
</div>
```

![](/assets/BFC2.png)黑盒创建一个新的 BFC 后，计算高度时将红盒区域也考虑进去了。

为绿盒创建 BFC：

```html
<div class="box BFC">
  <div class="left"></div>
  <div class="right BFC"></div>
</div>
```

![](/assets/BFC3.png)一旦绿盒创建了新的 BFC 后就不与红盒发生重叠。

## 总结

在实际中，利用 BFC 可以闭合浮动，防止与浮动元素重叠，也可以利用 BFC 包含一个元素，防止这个元素与 BFC 外的元素发生 margin 坍塌。

### `BFC` 解决边距重叠问题

当相邻元素都设置了 `margin` 边距时，`margin` 将取最大值，舍弃小值。为了不让边距重叠，可以给子元素加一个父元素，并设置该父元素为 `BFC：overflow: hidden;`

```html
<div class="box" id="box">
  <p>Lorem ipsum dolor sit.</p>

  <div style="overflow: hidden;">
    <p>Lorem ipsum dolor sit.</p>
  </div>

  <p>Lorem ipsum dolor sit.</p>
</div>
```

## 参考：

[http://www.html-js.com/article/1866](http://www.html-js.com/article/1866)
