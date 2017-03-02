---
layout: post
title: HTML5摇一摇（上）—如何判断设备摇动
date: 2016-01-12 14:00:12.000000000 +09:00
tags: [HTML5, 微信, 摇一摇]
---

> 刚刚过去的一年里基于微信的H5营销可谓是十分火爆，通过转发朋友圈带来的病毒式传播效果相信大家都不太陌生吧，刚好最近农历新年将至，我就拿一个“摇签”的小例子来谈一谈HTML5中如何调用手机重力感应的接口

演示demo：“[摇一摇，万福签](http://xunli.xyz/assets/demo/shake)”

## 什么是重力感应
说到重力感应有一个东西不得不提，那就是就是陀螺仪，陀螺仪就是内部有一个陀螺，陀螺仪一旦开始旋转，由于轮子的角动量，陀螺仪有抗拒方向改变的特性，它的轴由于陀螺效应始终与初始方向平行，这样就可以通过与初始方向的偏差计算出实际方向。


## 手机中的方位轴
![w3c-deviceorientation](http://7xort8.com1.z0.glb.clouddn.com/w3c-deviceorientation.png)

![alpha](http://7xort8.com1.z0.glb.clouddn.com/c-rotation.png)
![beta](http://7xort8.com1.z0.glb.clouddn.com/a-rotation.png)
![gamma](http://7xort8.com1.z0.glb.clouddn.com/b-rotation.png)

## 在Web应用中调用手机陀螺仪接口
具体实现摇一摇可以通过HTML5中的DeviceOrientationEvent或者DeviceMotionEvent，二者的区别在于DeviceOrientation只是判断用户设备的偏转角度，而DeviceMotion则可以计算用户手机移动的加速度
```javascript
//摇一摇(使用DeviceOrientation事件, 本质是计算偏转角)
if(window.DeviceOrientationEvent){
    var lastAcc;    // 用来存储上一次的deviceorientation事件
    $(window).on('deviceorientation', function(event) {
        var delA = Math.abs(event.alpha - lastAcc.alpha);    // alpha轴偏转角
        var delB = Math.abs(event.beta - lastAcc.beta);    // beta轴偏转角
        var delG = Math.abs(event.gamma - lastAcc.gamma);    // gamma轴偏转角
        if ( (delA > 15 && delB > 15) || (delA > 15 && delG > 15) || (delB > 15 || delG > 15)) {
            // 用户设备摇动了，触发响应操作
            // 此处的判断依据是任意两个轴篇转角度大于15度
            alert('摇了');
        }
        lastAcc = event;    // 存储上一次的event
    });
```

```javascript
//摇一摇(使用DeviceMotion事件, 推荐,应为可以计算加速度)
if(window.DeviceMotionEvent) {
    var speed = 25;    // 用来判定的加速度阈值，太大了则很难触发
    var x, y, z, lastX, lastY, lastZ;
    x = y = z = lastX = lastY = lastZ = 0;

    window.addEventListener('devicemotion', function(event){
        var acceleration = event.accelerationIncludingGravity;
        x = acceleration.x;
        y = acceleration.y;
        if(Math.abs(x-lastX) > speed || Math.abs(y-lastY) > speed) {
            // 用户设备摇动了，触发响应操作
            // 此处的判断依据是用户设备的加速度大于我们设置的阈值
            alert('摇了');
        }
        lastX = x;
        lastY = y;
    }, false);
}
```


## 摇一摇的代码判断逻辑
```javascript
var isStarted = false;    // 是否开始摇动

// 开始摇签
function start() {
    isStarted = true;
    $('.qiancover').hide();    //把封面背景上的静态签筒隐藏
    $('.decode').hide();    // 解签页面隐藏
    $('.result').show();    // 把签筒摇动的div显示出来
    // setTimeout(showDecode, 3000);
}

// 显示正在解签
function showDecode() {
    $('.result').hide();    // 把签筒摇动的div隐藏起来
    $('.decode').show();    // 显示正在解签
    setTimeout(jumpToDecode, 3000);
}

// 跳至签文页面
function jumpToDecode(){
    var urls = ["#", "#"];    // 用来存签文结果页面
    var jumpTo = urls[parseInt(Math.random() * urls.length)];    // 随机跳转至签文结果页面
    window.location = jumpTo;
};
```


示例代码：https://github.com/lionrock/HTML5-Example/tree/master/wechat-divination


参考文档：[DeviceOrientation Event Specification](http://www.w3.org/TR/orientation-event/)
