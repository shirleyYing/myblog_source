title:  flex属性介绍
date: 2015/12/13
tags: [前端,移动端, css3, flex]
categories: css3 
---
# flex学习笔记
## 前言
flexmodel ：自适应盒模型
分为主轴和侧轴，也就是横轴和侧轴。用来决定子元素的排列方式。
![CSS3-Flexbox-Model](/imgs/CSS3-Flexbox-Model.jpg)


PS:最近在做的页面主要以一列展示为主，所以主要用到了垂直居中相关的内容。和以前用margin的方式相比，确实好用太多。本文所写属性也和垂直居中相关。还有些属性并没有写全。更多属性，请参考：[a-visual-guide-to-css3-flexbox-properties](https://scotch.io/tutorials/a-visual-guide-to-css3-flexbox-properties).
**支持情况：**

Chrome 29+
Firefox 28+
Internet Explorer 11+
Opera 17+
Safari 6.1+ (prefixed with -webkit-)
Android 4.4+
iOS 7.1+ (prefixed with -webkit-)
可以在 [here](http://caniuse.com/flexbox)看到更多细节和兼容性的内容。

## 属性介绍
### flex
````
.flex-container {
  display: -webkit-flex; /* Safari */
  display: flex;
}
````
用来开启盒模型，用在flex容器中。

### inline-flex
````
.flex-container {
  display: -webkit-inline-flex; /* Safari */
  display: inline-flex;
}
````
像内联元素一样使用它。
以上两个需要在父元素中使用，它的直系孩子会自动成为flex文档流，成为可伸缩的项目。
###  flex-direction
定义内部文档流的排序方向。取值：row，column，row-reverse，column-reverse
默认值: `row`
````
.flex-container {
  -webkit-flex-direction: row; /* Safari */
  flex-direction:         row;
}
````
flexbox-flex-direction-row：

![flexbox-flex-direction-row](/imgs/flexbox-flex-direction-row.jpg)
flexbox-flex-direction-column:

![flexbox-flex-direction-column](/imgs/flexbox-flex-direction-column.jpg)
### flex-wrap
定义是否换行。
values:`wrap-reverse`, `wrap`.
default values:`nowarp` 
````
.flex-container {
  -webkit-flex-direction: row; /* Safari */
  flex-direction:         row;
}
````
### justify-content
用在容器中定义子元素排列顺序。
values:
`flex-start`:从主轴开始的位置排列,
`flex-end`:从侧轴开始的位置排列,
`center`:剧中排列，height延展至父容器，width不延展
`space-between：居中排列，height延展至父容器，width不延展，但是用间距填满主轴方向。
`space-around`：和`space-between`一样，唯一的不同是每个item 左右两边会有间距。
default values:`flex-start`
````
.flex-container {
  -webkit-justify-content: flex-start; /* Safari */
  justify-content:         flex-start;
}
````
flex-start:
![flexbox-justify-content-flex-start](/imgs/flexbox-justify-content-flex-start.jpg)
flex-end:
![flexbox-justify-content-flex-end](/imgs/flexbox-justify-content-flex-end.jpg)
cneter:
![flexbox-justify-content-center](/imgs/flexbox-justify-content-center.jpg)
space-between:
![flexbox-justify-content-space-between](/imgs/flexbox-justify-content-space-between.jpg)
space-around:
![flexbox-justify-content-space-around](/imgs/flexbox-justify-content-space-around.jpg)

### align-item
 定义容器中子元素的垂直（侧轴）对齐方式。可以用于设置子元素的垂直居中。和`justify-content` 配合使用。前者确定子元素的水平排列方式，后者确定子元素的垂直对齐方式。 
 values:`stretch`,`flex-start`,`flex-end`,`center`,`baseline`
 `stretch:`： 侧轴方向延展。
 `flex-start`： 沿侧轴开始的位置排列，靠上
 `flex-end`： 沿侧轴结束的位置排列，靠下
 `center`：垂直居中
 `baseline`：每个item的文字基线对齐。
default values: `stretch`

````
.flex-container {
   -webkit-align-items: stretch; /* Safari */
    align-items:         stretch;
}
````
stretch:
![flexbox-align-items-stretch](/imgs/flexbox-align-items-stretch.jpg)
flex-start:
![flexbox-align-items-flex-start](/imgs/flexbox-align-items-flex-start.jpg)
flex-end:
![flexbox-align-items-flex-end](/imgs/flexbox-align-items-flex-end.jpg)
center:
![flexbox-align-items-center](/imgs/flexbox-align-items-center.jpg)
baseline：
![flexbox-align-items-baseline](/imgs/flexbox-align-items-baseline.jpg)

## example
垂直居中的例子：

````
.flex-container {
    display:flex;
    justify-content :space-between;
    align-items：center;
}
````

只是简单地展示效果，并没有做兼容性考虑。




