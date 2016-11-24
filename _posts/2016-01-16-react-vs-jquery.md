---
layout: post
title: 为什么是React－浅谈React与jQuery的思维差异
date: 2016-01-16 18:50:35.000000000 +09:00
tags: ['jQuery', 'React']
---

> 为什么越来越多的互联网公司都在转向React.js去开发前端组件，除了性能因素外，很大一部分原因是因为用jQuery去写很复杂的DOM操作，后期代码会变得越来越难维护。现在大部分的 Web APP 都有一个特点：后端的Model层很简单，但是随着产品业务的拓展前端View却变得越来越复杂，这个时候如果还在用纯jQuery去写DOM操作将会变得很不直观，代码也会变的很臃肿进而变的越来越难以维护，下面就拿一个微博发送框的简单例子来展示一些React的简洁与强大。
>
> 注意：
> 1. React并不适合所有项目，需要结合实际情况综合考虑
> 2. jQuery与React并不是一个层面上的东西，jQuery只是一个工具库，这里只是展示两种编程模式的思维差异
> 3. 理解一个技术的思想比学会怎么用它更重要，同时我们还需要知道不同的技术间的区别的核心在哪，这样我们才能学会用合适的技术去解决合适的问题
> 4. 这里的tweet box只是一个简化版的UI模型，仅用来说明jQuery和React的思维差异，并不具备发Twitter的功能
> 5. 你也可以写出简洁的jQuery代码。但是你必须想出良好的代码结构，每次想要增加新功能的时候还需要特别注意是否影响代码的重构，使用React会帮助你团队内部拥有更好的代码结构，页面性能也会得到相应的提高


## 需要实现的效果
1. tweet box为空时，tweet button显示为灰色不可点击状态
2. tweet box下方显示还可以输入的字符数量
3. 点击add photo，剩余字符数量及add photo按钮状态发生改变（假定图片占用23个字符）


## 先来看看React版本：
React.js的提供了一整套Virtual DOM，所有的操作都在这个Virtual DOM上，只有当事件发生的时候，`state`才发生改变，之后，React自动调用`render()`来更新UI

下面是我托管在jsBin上的Demo：
<a class="jsbin-embed" href="http://jsbin.com/verazi/embed?js,output">"React Tweet Box Demo" on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.9"></script>

**React代码处理问题的思路**：
1. 创建一个状态变量用来追踪textarea中的字符的数量
2. 在textarea的onChange事件发生时改变其状态
3. 使用render()重新渲染tweet box，显示还可以输入的字符的数量

```js
var TweetBox = React.createClass({
  getInitialState: function() {
    return {
      text: "",
      photoAdded: false
    };
  },
  handleChange: function(event) {
    this.setState({ text: event.target.value });
  },
  togglePhoto: function(event) {
    this.setState({ photoAdded: !this.state.photoAdded });
  },
  remainingCharacters: function() {
    if (this.state.photoAdded) {
      return 140 - 23 - this.state.text.length;
    } else {
      return 140 - this.state.text.length;
    }
  },
  render: function() {
    return (
        <div className="well clearfix">
            <textarea className="form-control" onChange={this.handleChange}></textarea>
            <br/>
            <span>{ this.remainingCharacters() }</span>
            <button className="btn btn-primary pull-right"
                    disabled={this.state.text.length === 0 && !this.state.photoAdded}>
                Tweet
            </button>
            <button className="btn btn-default pull-right" onClick={this.togglePhoto}>
                {this.state.photoAdded ? "✓ Photo Added" : "Add Photo" }
            </button>
        </div>
    );
  }
});

React.render(
  <TweetBox />,
  document.body
);
```


## 再来看看熟悉的jQuery版本
> 这里用到了Bootstrap框架，但是仅仅只是为了好看，如果不熟悉BootStrap的话请自动忽略CSS Class
因为jQuery只是一个工具库，它处理问题的思路是，用选择器选择DOM元素，有需要的话就对某个DOM元素进行监听，然后再事件监听函数里进行相对应的处理

<a class="jsbin-embed" href="http://jsbin.com/geqisi/embed?js,output">"jQuery Tweet Box Demo" on jsbin.com</a><script src="http://static.jsbin.com/js/embed.min.js?3.35.9"></script>

**jQuery处理问题的思路**
1. 给按钮或者文本框绑定监听事件
2. 如果事件触发则开始判断，各个DOM对象的当前状态（通常会借助css class来表示状态）
3. 改变对应的DOM来实现想要达到的效果（tweet box下方显示还可以输入的字符数量，按钮是否可点击）

```html
<div class="well clearfix">
    <textarea class="form-control"></textarea><br>
    <span>140</span>
    <button class="js-tweet-button btn btn-primary pull-right" disabled>Tweet</button>
    <button class="js-add-photo-button btn btn-default pull-right">Add Photo</button>
</div>
```

```javascript
// 给文本框绑定事件，监听是否有输入
$("textarea").on("input", function() {
    if ($(".js-add-photo-button").hasClass("is-on")) {
        // add phtot按钮已经点击，剩余输入文本数量再减23
        $("span").text(140 - 23 - $(this).val().length);
    } else {
        // 计算剩余文本数量
        $("span").text(140 - $(this).val().length);
    }

    if ($(this).val().length > 0 || $(".js-add-photo-button").hasClass("is-on")) {
            // 如果文本框里有内容或者add phtot按钮已经点击，tweet button设置为可点击状态
        $(".js-tweet-button").prop("disabled", false);
    } else {
            // tweet button设置为不可点击状态
        $(".js-tweet-button").prop("disabled", true);
    }
});

// 给添加照片的按钮绑定点击事件监听
$(".js-add-photo-button").on("click", function() {
    if ($(this).hasClass("is-on")) {
        $(this).removeClass("is-on").text("Add Photo");  // 切换add photo按钮显示状态

        $("span").text(140 - $("textarea").val().length);
        if ($("textarea").val().length === 0) {
            // 切换tweet按钮前需要先判断textarea当前状态
            $(".js-tweet-button").prop("disabled", true);
        }
    } else {
        $(this).addClass("is-on").text("✓ Photo Added");  // 切换add photo按钮显示状态

        $("span").text(140 - 23 - $("textarea").val().length);
        $(".js-tweet-button").prop("disabled", false);
    }
});
```

## 总结
![react-vs-jquery](http://7xort8.com1.z0.glb.clouddn.com/blog_react-vs-jquery.png)
1. 在React中，只有当事件发生的时候，state才发生改变，之后，React自动调用render()来更新UI
2. state成为了事件以及render()之间过渡：
    - 每个事件不需要担心哪一部分的DOM发生变化，他们只需要设置state就可以了
    - 相应的，当你写render()的时候，你也只需要担心现在的state是什么
3. jQuery没有中间的过渡层'state'，我们需要花费很大的精力来解决它们之间相互的联系(对于复杂的组件，建议使用React而不是jQuery)
4. React中把各个UI组件独立出来，有利于提高UI组件的复用率同时降低各个UI组件的耦合
5. 新手在直接操作DOM时很难写出高效而又优雅的代码，从而使得前端代码满满变得越来越难以维护

也就是说，React适合用在那些DOM操作复杂的单页面应用，有利于提高代码可读性以及提高页面性能（可以参考React Diff算法）；
jQuery则是个用来帮你完成一些基本操作的工具库


如果需要阅读完整的教程，请阅读原文：
[React.js Introduction For People Who Know Just Enough jQuery To Get By](http://reactfordesigners.com/labs/reactjs-introduction-for-people-who-know-just-enough-jquery-to-get-by/?utm_source=javascriptweekly&utm_medium=email)
