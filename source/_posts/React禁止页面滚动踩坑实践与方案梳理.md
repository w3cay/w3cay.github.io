---
title: React禁止页面滚动踩坑实践与方案梳理
tags:
  - Javascript
  - React
abbrlink: dc49b55
date: 2018-07-02 23:09:08
---

最近在使用 React 技术栈重构一个单页应用，其中有个页面是实现城市选择功能，主要是根据城市的首字母来快速跳转到相应位置，比较类似原生 APP 中的电话联系人查找功能，页面如图
![功能界面](/images/scroll-issue/2250902-8418da4d28a9107e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 主要问题
在上下滑动右侧 fixed 定位的元素时，页面会跟着一起滑动

![滚动右侧整个页面跟着滚动](/images/scroll-issue/2250902-c0ed1030d3fc5d08.gif?imageMogr2/auto-orient/strip)

当然这个现象在开发过程中应该会经常遇到，比如弹起 modal 框时，如果 modal框的内容高度小于框高度，滑动内容也会导致页面跟着滑动， 那么在 React 中像往常一样处理
```
<div className="nonius"
  id="nonius"
  onTouchStart={this.sidebarTouchStart.bind(this)}
  onTouchMove={this.sidebarTouchMove.bind(this)}
  onTouchEnd={this.sidebarTouchEnd.bind(this)}
>
```
使用 React 提供的事件绑定机制，分别绑定三个 handler ，在  onTouchMove 事件中，我希望通过 preventDefault 能够阻止父级元素的滚动
```
sidebarTouchMove(e) {
  e.preventDefault();
  ...
}
```
但实际的反馈却事与愿违，在调试中，我发现 Chrome 是有警告的，并且没有达到想要的效果
![chorme 开发工具警告提示](/images/scroll-issue/2250902-74d74a702fbd05a7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


根据警告提示，找到的原因是
> AddEventListenerOptions defaults passive to false. With this change touchstart and touchmove listeners added to the document will default to passive:true (so that calls to preventDefault will be ignored)..
 If the value is explicitly provided in the AddEventListenerOptions it will continue having the value specified by the page.
 This is behind a flag starting in Chrome 54, and enabled by default in Chrome 56. See https://developers.google.com/web/updates/2017/01/scrolling-intervention

来源: https://www.chromestatus.com/features/5093566007214080

根据 chrome 的提示得知，是因为 Chrome 现在默认把通过在 document 上绑定的事件监听器 passive 属性（后面细说）默认置为 true，这样就会导致我设置的  e.preventDefault() 被忽视了。当然 Chrome 的这个做法是有道理，是为了提高页面滚动的性能，那么为了防止带来的副作用，官方考虑的很周到，给我们提供了一个 CSS 属性专门用来解决这个问题

```
#fixed-div {
  touch-action: none;
}
```

> In rare cases this change can result in unintended scrolling. This is usually easily addressed by applying a [touch-action](https://developer.mozilla.org/en-US/docs/Web/CSS/touch-action): nonestyle to the element where scrolling shouldn't occur.

https://developer.mozilla.org/zh-CN/docs/Web/CSS/touch-action

加上了这个属性，感觉世界总算和平了，But！在 ios 系统上测试发现，这个属性 x 用没有，查了下 Can I Use
![can i use 截图](/images/scroll-issue/2250902-7ce4d43eec6721f7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

确定无误，就是不支持，所以这个属性只在 Chrome 安卓等机型下是支持的，ios 这个就用不了，理想很丰满，显示很骨感。既然不兼容，那只能降级处理了，为了保证良好的功能体验，感觉是还要从 passive 上做处理，说到 passive 根据 [MDN文档：addEventListener](https://developer.mozilla.org/en-US/docs/Web/API/EventTarget/addEventListener#Improving_scrolling_performance_with_passive_listeners) 的介绍，为了提高页面滚动性能，大多浏览器都默认把 touchstart 和 touchmove 在文档元素上直接注册的这个事件监听器属性设置成 passive：true ，而通过 AddEventListener 注册的事件依然没有变化

既然现在默认将事件 passive 的属性默认设置为 true ,那我就显式设置为 false 好了，查遍 React 的文档，也没发现事件监听器可以支持配置这个属性的，在 github 上发现这个帖子 [Support Passive Event Listeners #6436 ](https://github.com/facebook/react/issues/6436) 目前看依然是 open 状态的，现在不确定有没有支持这个属性
## 解决方案
既然这样，只能单独对 touchmove 通过 AddEventListener 方法去注册事件监听了
```
// 为元素添加事件监听   
document.getElementById('nonius').addEventListener("touchmove", (e) => {
  // 执行滚动回调
  this.sidebarTouchMove(e)
}, {
  passive: false //  禁止 passive 效果
})
```
 加上这个方法后，this.sidebarTouchMove(e) 方法中的     e.preventDefault() 方法就可以正常使用了，而且没有警告提示，问题到此就算解决了
## 总结
总结下，这里的坑主要是 chrome 和 safari 平台的标准不统一导致的，新的标准出台，其它宿主环境不能很好的支持，当然 react 官方对这个属性的支持也比较慢，同样的前端 UI 框架 Vue  就处理的很棒
![vue对passive属性支持的相关语法](/images/scroll-issue/2250902-12cacb6e99c0daf6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
不小心暴露了，我是个 Vue粉，233
ok，完 ~


