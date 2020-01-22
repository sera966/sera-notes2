####获取元素样式

```
// 1.window.getComputedStyle(el, null)[styleName] 是为了兼容IE的， IE直接使用元素点style取不到style样式

function getStyle(el, styleName) {
  return el.style[styleName]
    ? el.style[styleName]
    : el.currentStyle
    ? el.currentStyle[styleName]
    : window.getComputedStyle(el, null)[styleName];
}

```
