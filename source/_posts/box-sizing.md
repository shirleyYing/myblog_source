title: 用box-sizing 解决移动端不同屏幕手机适配问题
date: 2016-01-10 17:45:22
tags: [css3, html5, mobile, terminal]
categories: css3 

---
# 用box-sizing 解决移动端不同屏幕手机适配问题

手机页面由于要适配不同屏幕大小。所以宽度都要用百分数，如果遇到占满整个屏幕宽度，还是有border的div要怎么办？

<!-- more -->
css3里面的一个属性：`box-sizing` 直接解决了问题。
比方说这样一个页面。
![view](/imgs/mobile.jpg)
移动端的h5页面，需要设配不同屏幕尺寸，但是`width` 又是不算border的。那也就是说`width:100%` 就不能用了，但是我怎么知道有多少尺寸等着我啊。多次尝试未果，打算用背景图来解决。牛逼闪闪的师父给了我这个建议：**box-sizing**。

box-sizing：content-box| border-box

content-box: 就是现代浏览器盒模型的计算方法，width就是你自己写的width，padding，border都不算在里面。

border-box： width是总的占的尺寸。所有都算。

像这个页面，上半部分绿色的框需要占满整个屏幕，下半部分press按钮又需要在下半部分的高度里垂直居中。刚刚的box-sizing搞定了上半部分。下半部分的垂直居中，同样遇到了 `height` 不固定的问题。用flex做垂直居中是个比较好的选择。同样需要用到  `box-sizing`

### 解决方案

把上半部分的高度用px固定死当然会适配几种尺寸，然后下半部分用一个div。绝对定位，占满整个屏幕，然后padding-top把它的位置调下来。

box-sizing 又一次起作用了。如果不加box-sizing，你的高度就要爆掉了。然后flex垂直居中，完美。


代码就不上了，比较懒~

### 另一个问题
做到这还遇到了另外一个问题：div 层次的问题。
```
.top{
    .loading{}
}
.bottom{
    .button{}
}
```

首先效果是先出loading几秒，然后淡入首页，但是loading出现的时候，下面的bottom块也在。第一个想法肯定是加z-index了，但是，加了没用。
原因：
它放在top里面。top和bottom是平级的。也就是说，用了绝对定位后，bottom就在top前面了。top里面的loading index再高，父元素不给力，也是没办法的。所以，在top上加`z-index` 就好了。之前看过一篇特别棒的写 z-index的文章，贴在这里。
[http://www.w3cplus.com/css/what-no-one-told-you-about-z-index.html]

第一次做移动端的页面，确实要考虑很多适配问题，之后遇到其他的，继续记录。
