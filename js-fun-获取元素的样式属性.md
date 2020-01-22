####获取元素的样式属性

```
    function getSize(el) {
      // 如果内容区域没折叠， 则通过下面方式可以获取到元素的宽高，如果display为null通过普通的方式获取不到宽高， 需要特殊处理使用getStyle循环遍历去获取
      if (getStyle(el, "display") != "none") {
        return {
          width: el.offsetWidth || getStyleNum(el, "width"),
          height: el.offsetHeight || getStyleNum(el, "height")
        };
      }
      // 显示是因为只需要width和height两个属性，暂时没用到其他的，如果需要动态获取其他的，可以遍历出来
      // var _addCss = { display: "", position: "absolute", visibility: "hidden" };
      // var _oldCss = {};
      // for (let i in _addCss) {
      //   _oldCss[i] = getStyle(el, i);
      // }
      var _width = el.clientWidth || getStyleNum(el, "width");
      var _height = el.clientHeight || getStyleNum(el, "height");
      // Object.values把值遍历成一个数组
      // 例如： var obj = { foo: 'bar', baz: 42 };
      // console.log(Object.values(obj)); // ['bar', 42]
      // Object.values(_oldCss).forEach(function() {});
      return { width: _width, height: _height };
    }
```
