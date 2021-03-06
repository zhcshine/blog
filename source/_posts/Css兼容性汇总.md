---
title: css兼容性汇总
categories:
- 前端
date: 2016-11-10 10:07:27
tags:
- Css
---

### ie6,ie7中overflow:hidden失效问题

> 在父元素中使用position:relative;可以hack iframe自适应高度

### 不建议使用iframe

在css里面设置一个min-height
```css
.class {
min-height: 100px;
}
```
主页面嵌入如下js代码
```js
function setIframeHeight(iframe) {
    if (iframe) {
        var iframeWin = iframe.contentWindow || iframe.contentDocument.parentWindow;
        if (iframeWin.document.body) {
            iframe.height = iframeWin.document.documentElement.scrollHeight || iframeWin.document.body.scrollHeight;
        }
    }
};
window.onload = function () {
    setIframeHeight(document.getElementById('external-frame'));
};
```
iframe标签里
```html
<iframe src="backtop.html" frameborder="0" scrolling="no" id="external-frame" onload="setIframeHeight(this)"></iframe>
```
css代码
```css
ul {
    display: block;
    width: 100%;
    height: 100%;
    text-indent: 0;
    padding: 0;
    position: absolute;
    top: -100%;
    left: 0;  
}
```
js代码
```js
var defaultTopPosition = $('#indexCellcate').css('top');
```
浏览器差异
```markdown
ie7，ie8 返回-205.422px  
现代chrome，firefox -205.422px  
但是ie9及以上会返回0px
如果将css中的top设置为-205.422px，则无差异。  
```
> 但响应式布局要求百分百设置,我的解决思路: 获取对应元素的高度来替代top值
```js
var defaultTopPosition = cellcate.children('li').height();
defaultTopPosition = -parseInt(defaultTopPosition);
```
    