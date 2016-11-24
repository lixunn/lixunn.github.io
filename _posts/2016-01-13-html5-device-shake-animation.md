---
layout: post
title: HTML5摇一摇（下）—如何实现签筒摇动动画
date: 2016-01-13 21:23:04.000000000 +09:00
tags: [HTML5, 微信, 摇一摇]
---

演示demo：“[摇一摇，万福签](http://xunli.xyz/assets/demo/shake)”

上一篇博客([HTML5摇一摇—如何判断设备摇动（上）](http://xunli.xyz/2016/01/12/html5-device-shake/))中讲到了如何利用HTML5中的DeviceOrientationEvent和DeviceMotionEvent来实现手机摇一摇的判断，这次主要来讲如何利用CSS3实现签筒摇动的动画

## 半透明遮罩层
> 当签筒摇动动画出发时，半透明遮罩层显示出来用来凸显签筒摇动的动画
```css
/* 半透明遮罩层 */
.cover {
    background: rgba(0, 0, 0, 0.7) no-repeat top right;
    position: fixed;
    width: 100%;
    height: 100%;
    top: 0;
    left: 0;
    display: none;
}
```

## CSS3动画关键帧
> 了解动画原理的人应该都知道，所谓的动画其实就是由若干个关键帧构成，当一连串的关键帧连续播放时就形成了我们所看见的动画

这里我们所用到的素材其实就是6张连续摇动的签筒所拼成的一张大图：
![签筒摇动素材](http://7xort8.com1.z0.glb.clouddn.com/blog_shake-animation.png)

实现这个动画有两个思路：
1. 把动画分成一步，动画开始以结尾端点为起点开始执行，然后在0%，20,40,80,100执行了背景图的切换
2. 第二种是动画分成5步，然后每一步在100%处的时候调用最后一张背景图
这里我们就拿第二种实现方法来举例

接下来我们利用CSS3中的`@keyframes`来定义的动画的关键帧，其中`－737px －2px`表明每一步动画都会往右移737像素并上移－2px
```css
@keyframes play {
    100% {
        background-position: -737px -2px;
    }
}
/*为了兼容性考虑，可能还需要加上浏览器厂商的前缀，
例如下面给只出了webkit内核的浏览器厂商前缀，
其他的还有-moz-animation，-o-animation */
@-webkit-keyframes play {
    100% {
        background-position: -737px -2px;
    }
}
```

## 调用我们定义的CSS3关键帧
这里我们利用`steps()`这个函数把动画平分成5等份，每份0.8s（100%处的时候调用最后一张背景图），`infinite`参数则会让动画无限循环播放
```css
.sprite {
    width: 145px;
    height: 300px;
    display: inline-block;
    overflow: hidden;
    background: url('./img/animation.png') no-repeat -2px -2px;
    animation: play 0.8s steps(5) infinite;
    -webkit-animation: play 0.8s steps(5) infinite;
}
```

## 让按钮动起来
按钮的动画用到了[贝赛尔曲线](http://cubic-bezier.com/#.17,.67,.83,.67)来优化运动效果

`cubic-bezier(x1, y1, x2, y2))`本质上是一个时间函数，文档定义如下：
> x1, y1, x2, y2
> Are <number> values representing the abscissas and ordinates of the P1 and P2 points defining the cubic Bézier curve. x1 and x2 must be in the range [0, 1] or the value is invalid.


```css
@keyframes shake {
    0% {
        transform: scaleY(1) translate(30px);
    }
    50% {
        transform: translate(-30px);
    }
    100% {
        transform: scaleY(1) translate(30px);
    }
}

/*这里用到了贝赛尔曲线来优化运动的效果*/
.shakeBtn {
    width: 76%;
    max-width: 300px;
    display: block;
    margin: 0 auto;
    cursor: pointer;
    animation: shake 1.2s cubic-bezier(0, 0, 0.36, 1) infinite;
}
```
