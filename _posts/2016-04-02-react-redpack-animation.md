---
layout: post
title: '用React加CSS3实现微信拆红包动画'
date: 2016-04-02 22:21:34.000000000 +09:00
tags: ['React', '动画']
---

> 微信红包曾经引爆过一系列的营销热潮，相信大家对于这种红包形式，这里本着娱乐至上的精神用React简单地实现了拆红包的动画效果，供大家一起交流学习

# 用CSS3绘制红包
```css
.redpack {
  height: 450px;
  background: #A5423A;
  width: 300px;
  left: 0;
  top: 0;
  border-radius: 10px;
  margin: 0 auto;
}
.topcontent {
    height: 300px;
    border: 1px solid #BD503A;
    background-color: #BD503A;
    border-radius: 10px 10px 50% 50% / 10px 10px 15% 15%;
    box-shadow: 0px 4px 0px -1px rgba(0,0,0,0.2);
}
#redpack-open {
    width: 100px;
    height: 100px;
    border: 1px solid #FFA73A;
    background-color: #FFA73A;
    border-radius: 50%;
    color: #fff;
    font-size: 20px;
    display: inline-block;
    margin-top: -50px;
    box-shadow: 0px 4px 0px 0px rgba(0, 0, 0, 0.2);
}
```
```html
<div class='redpack'>
  <!--  红包的顶部盖子 -->
  <div class="topcontent"></div>
  <!-- 拆红包的按钮 -->
  <div id="redpack-open"></div>
</div>
```

在线Demo演示: https://jsbin.com/sawaxid/edit?html,css,output

<a class="jsbin-embed" href="http://jsbin.com/sawaxid/9/embed?output">JS Bin on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.12"></script>


# 用React来区分不同的状态的转换
