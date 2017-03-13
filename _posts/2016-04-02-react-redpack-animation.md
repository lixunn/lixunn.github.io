---
layout: post
title: '用React加CSS3实现微信拆红包动画'
date: 2016-04-02 22:21:34.000000000 +09:00
tags: ['React', '动画']
---

> 微信红包曾经引爆过一系列的营销热潮，相信大家对于这种红包形式并不陌生，这里本着娱乐至上的精神用React简单地实现了拆红包的动画效果，供大家一起交流学习

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
用React.js来实现的话，主要通过判断state来控制红包现在是等待拆开还是已经拆开过，具体的代码如下

```javascript
import React from 'react';

class ReadPacket extends React.Component {
    constructor(props) {
        super(props);
        this.state = {
            animation: false,
            status: 0  // 0: 等待拆开 1: 拆开后
        };
    }

    render() {
        var bonus = this.props.thanks ? 0 : parseFloat(this.props.surveyInfo.bonus);

        if(this.state.status == 0) {
            return (
                <div className='redpack-container' id='redpack-container'>
                    <div className='redpack'>
                        <div className='topcontent'>
                            <div id='redpack-opened'>
                              <div className='redpack-avatar'>
                                <img src='http://placehold.it/80x80' alt='头像' width='80' height='80'/>
                              </div>
                            </div>
                            <h2 style={{marginTop: 80, color: 'white'}}>奖励</h2>
                            <span className='redpack-text'>点击下方按钮领取红包</span>
                            <div className='redpack-description white-text'>恭喜发财 大吉大利</div>
                        </div>

                        <div id='redpack-open' className={this.state.animation ? 'rotate' : ''}
                             onClick={this.openRedPacket.bind(this)}
                        >
                            <span>拆红包</span>
                        </div>
                    </div>
                </div>
            );
        } else if (bonus == 0) {
            // 谢谢参与
            return (
                <div className='redpack-container' id='redpack-container'>
                    <div className='redpack'>
                        <div className='topcontent-open'>
                            <div className='redpack-avatar'>
                                <span id='close'></span>
                            </div>
                            <h1 style={{marginTop: 180, color: 'white'}}> 谢谢参与 </h1>
                            <span className='redpack-text'>多多参与的奖励的机会更多哦</span>
                            <br/>
                            <a onClick={this._toWallet.bind(this)}
                               style={{cursor:'pointer',textDecoration: 'underline', color: 'white'}}>
                                去我的账户查看
                            </a>
                        </div>

                        <div id='redpack-opened'>
                          <div className='redpack-avatar'>
                            <img src='http://placehold.it/80x80' alt='头像' width='80' height='80'/>
                          </div>
                        </div>
                    </div>
                </div>
            );
        } else {
            // 显示奖励金额
            return (
                <div className='redpack-container' id='redpack-container'>
                    <div className='redpack'>
                        <div className='topcontent-open'>
                            <div className='redpack-avatar'>
                                <span id='close'></span>
                            </div>
                            <h1 className='white-text' style={{marginTop: 180}}> {bonus.toFixed(2)} </h1>
                            <span className='redpack-text'>奖励积分已经存入您的账户</span>
                            <a className='btn-flat white-text' onClick={this._toWallet.bind(this)}
                               style={{textDecoration: 'underline'}}>
                                去我的账户查看积分
                            </a>
                        </div>

                        <div id='redpack-opened'>
                          <div className='redpack-avatar'>
                            <img src='http://placehold.it/80x80' alt='头像' width='80' height='80'/>
                          </div>
                        </div>
                    </div>
                </div>
            );
        }
    }

    stopAnimation() {
        this.setState({animation: false});
    }

    showResult() {
        this.setState({status: 1});
    }

    openRedPacket() {
        this.setState({animation: true});
        setTimeout(this.stopAnimation.bind(this), 3000);
        setTimeout(this.showResult.bind(this), 4000);
    }

    _toWallet() {
      // 跳转到钱包
      window.location.hash = '/wallet';
    }
}


export default ReadPacket;
```

完整的CSS代码如下：
```CSS
/*----------------------------------------
  红包动画
------------------------------------------*/
.redpack-container{
  text-align: center;
  //width: 100%;
  margin: 0 auto;
  //padding: 15px 5px;
  //background-color: rgba(0,0,0,0.3);
  //height: 100%;
  //position: absolute;
}
.redpack{
  height: 450px;
  background: #A5423A;
  width: 300px;
  left: 0;
  top: 0;
  border-radius: 10px;
  margin: 0 auto;
}
@media (max-width: 370px) {
  .redpack{
    height: 420px;
    width: 100%;
  }
}
.topcontent{
  height: 300px;
  border: 1px solid #BD503A;
  background-color: #BD503A;
  border-radius: 10px 10px 50% 50% / 10px 10px 15% 15%;
  box-shadow: 0px 4px 0px -1px rgba(0,0,0,0.2);
}

.topcontent-open {
  height: 72px;
  border: 1px solid #BD503A;
  background-color: #BD503A;
  border-radius: 10px 10px 50% 50% / 10px 10px 22% 22%;
  box-shadow: 0px 4px 0px -1px rgba(0,0,0,0.1);
  transition: 1s ease-in-out;
}

.redpack-avatar{
  position: relative;
}
.redpack-avatar span{
  position: absolute;
  top: 10px;
  right: 15px;
  -webkit-transform: rotate(45deg);
  -ms-transform: rotate(45deg);
  transform: rotate(45deg);
  font-size: 2em;
  font-weight: bolder;
}
#close{
  color: rgba(0,0,0,0.5);
}
.redpack-avatar img{
  border: 1px solid #BD503A;
  border-radius: 50%;
  overflow: hidden;
  margin-top: 10%;
}
.topcontent h2{
  margin: 15px 0;
}
.redpack-text{
  color: #999;
}
.redpack-description{
  margin: 15px 0;
  font-size: 16px;
  font-weight: 600;
}
#redpack-open{
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
#redpack-open span{
  margin-top: 35px;
  display: inline-block;
}
#redpack-opened{
  width: 100px;
  height: 100px;
  //border: 1px solid #FFA73A;
  //background-color: #FFA73A;
  border-radius: 50%;
  color: #fff;
  font-size: 20px;
  display: inline-block;
  margin-top: -50px;
  //box-shadow: 0px 4px 0px 0px rgba(0, 0, 0, 0.2);
}
.rotate{
  -webkit-animation: anim .6s infinite alternate;
  -ms-animation: anim .6s infinite alternate;
  animation: anim .6s infinite alternate;
}
@-webkit-keyframes anim {
  from { -webkit-transform: rotateY(180deg); }
  to { -webkit-transform: rotateY(360deg); }
}
@-ms-keyframes anim {
  from { -ms-transform: rotateY(180deg); }
  to { -ms-transform: rotateY(360deg); }
}
@keyframes anim {
  from { transform: rotateY(180deg); }
  to { transform: rotateY(360deg); }
}

```

代码例子：