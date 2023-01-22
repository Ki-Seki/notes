title:      CSS 学习笔记
summary:    CSS 学习笔记
author:     小 K
datetime:   2021-09-16 16:41
            2021-10-05 22:11
tags:       笔记
            CSS

[TOC]

# 1. CSS 学习笔记

## 1.1. 技巧

### 1.1.1. 使用 width、max-width 和 margin: auto;

利用 `max-width` 替换 `width`，并加以 `margin: auto`，即可制作美观的响应式界面。

`max-width` 指定了默认的宽度，如果屏幕不够，则自动减小显示宽度。

### 1.1.2. 浮动导致元素脱离文档流

```css
/* .clearfix 是要浮动元素的最后一个 */
.clearfix::after {
  content: "";
  clear: both;
  display: table;
}
```

注：清除两侧浮动，就是指元素两侧均不能出现浮动元素

## 1.2. 原理

### 1.2.1. HSL 颜色

> 色相（hue）：色相通俗的说就是“颜色”，色相的改变就是颜色的改变，色相的调节伴随着红橙黄绿蓝紫的变化。
>
> 饱和度（saturation）：饱和度通俗的说就是“色彩的纯度”，饱和度的改变会影响颜色的鲜艳程度，以红色为例子，越高，越接近红色，越低则越接近灰色（黑白）
> 
> 明度（lightness）：明度通俗的说就是“光照度”，明度的改变就是光照在物体上带来的改变，明度的调节伴随着越高，光越强，越泛白（就像过曝一样，往白色上偏离）；越低，光越弱，越往黑里偏
> 
> 作者：D老爷
> 链接：https://www.zhihu.com/question/23173178/answer/23843763
> 来源：知乎
> 著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### 1.2.2. 样式属性名的命名规律

由不同的连字符连接从大到小的描述细节。这样子就可以实现缩写。比如 `border` 是 `border-width`, `border-style`, `border-color` 的缩写；`border-top` 是 `border-top-color`, `border-top-style`, `border-top-width` 的缩写。

### 1.2.3. 文档流（Normal Flow）

在文档流中，内联元素默认从左到右流，遇到阻碍或者宽度不够自动换行，继续按照从左到右的方式布局；块级元素单独占据一行，并按照从上到下的方式布局。脱离文档流的方法：`float: [left|right]`， `position: absolute`，`position:fixed`

### 1.2.4. 动效设计

可以使用 CSS 的转换（Transform）、过渡（Transition）和动画（Animation）技巧。转换实现 2D、3D 变换，配合过渡，实现动效。动画 = 转换 + 过渡。因此，利用动画可以更方便的制作动效。

### 1.2.5. CSS 中的 %

* width、height、relative：width相对于父元素的宽；height相对于父元素的高进行计算。relative:top、bottom相对父元素的高;left 、right相对于父元素的宽进行计算。
* border-raudis：相对自身标签的宽高设置每个边角的垂直和水平半径。
* margin: left、right、top、bottom相当于父元素的宽度进行
* absolute：top、bottom相对定位元素的高;left 、right相对于定位元素的宽进行计算。同时位于absolute中的其他属性如width heiht margin等都相当于定位元素进行计算。
* line-hight设置内联元素垂直居中时,%相对于文本的行高进行计算，非父元素。

### 1.2.6. 布局的方法

* table 布局：使用 `<table></table>` 或 `display: table`
* float
* position
* flex
* RWD：以 media query 为主
* CSS Grid Layout Module

## 1.3. 牢记

### 1.3.1. CSS 的一些口诀

* 链接样式的顺序：link visited hover active，即 LoVe, HA!
* 四个方向的顺序：上右下左，即从上开始顺时针旋转
* 四个角的顺序，从左上开始顺时针旋转
* `img` 的 `display` 默认是 `inline-block`

### 1.3.2. 易错点

* border 的 style 属性总要定义才可以定义 border 的其他属性
* 链接样式顺序不能错