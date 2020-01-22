####下拉框展开收起隐藏内容和旋转图标逻辑

```
import Vue from "vue";

// 创建名为fold的vue自定义指令，在bind函数中定义st，cst，islock，delay并初始化
Vue.direction("fold", {
  bind: function(el) {
    let st = null,
      cst = null,
      islock = null, // 判断是否单击展开或收起执行完了吗 防止点击展开时样式还没切换完又点收起等频繁操作导致的样式错乱问题
      delay = 0.4;

    // 定义getStyleNum函数 通过调用getStyle 替换单位为空字符串，只需要取到尺寸的值
    function getStyleNum(el, styleName) {
      return parseInt(getStyle(el, styleName).replace(/px|pt|em/gi, ""));
    }

    // 判断对象类型getType
    function getType(o) {
      var _t;
      // 1. 传进来的参数o赋值给_t, 再拿_t去做比较
      // 2. Object.prototype.toString.call方法返回一个表示该对象的字符串
      return ((_t = typeof o) == "object"
        ? (o == null && "null") ||
          Object.prototype.toString.call(o).slice(8, -1)
        : _t
      ).toLowerCase();
    }
    // 获取元素样式 getStyle函数
    // 1.window.getComputedStyle(el, null)[styleName] 是为了兼容IE的， IE直接使用元素点style取不到style样式
    function getStyle(el, styleName) {
      return el.style[styleName]
        ? el.style[styleName]
        : el.currentStyle
        ? el.currentStyle[styleName]
        : window.getComputedStyle(el, null)[styleName];
    }

    // 获取宽高属性 getSize函数
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

    // el就是fold块， 下面有两块，第一块el.children[0]是panel-title整个下拉框(这下面有两个子元素： 左边的文字和右边的箭头)
    // 第二块是下拉框下面的折叠收起的内容区域 my-content
    // 自定义属性data-type为yes是折叠的 no是展开的
    el.children[0].setAttribute("data-type", "yes"); // 这个yes控制展开收起
    el.children[0].children[1].setAttribute("data", "1"); // 1是控制右边小三角旋转

    el.children[1].setAttribute("style", "height: 0"); // 每个下拉框下面的内容区my-content域默认高度设置0
    el.children[0].children[1].setAttribute(
      "style",
      `transform: rotate(-90deg)`
    ); // 下拉框右边的小箭头原图是箭头往下， 默认折叠小三角默认旋转90度朝右

    // 给下拉框绑定单击事件
    el.children[0].addEventListener("click", function(e) {
      if (islock) {
        return;
      }
      islock = true;
      let rotateImg = null,
        target = null,
        ch = 0;
      // 点击下拉框时获取自定义属性data-type， 如果没有该属性或者该属性不为yes(则是展开状态)
      if (
        !e.target.getAttribute("data-type") ||
        e.target.getAttribute("data-type") != "yes"
      ) {
        // 这个if是判断下拉框点击的位置，因为下拉框中有文字和箭头，点击不同的时候父子层级的节点可能不一样
        // 此时是点击箭头时触发
        if (e.target.parentNode.getAttribute("data-type") == "yes") {
          // e.target.parentNode是fold块
          targte = e.target.parentNode.nextElementSibling;
          rotateImg = e.target.parentnode.children[1];
        } else {
          // 此时是点击下拉框中的标题文字时触发 此时e.target是下拉框中的left文字块
          target = e.target.parentNode.parentNode.nextElementSibling;
          rotateImg = e.target.parentNode.nextElementSibling;
        }
      } else {
        // 此时就是点击下拉框中的其他位置时触发
        target = e.target.nextElementSibling; // target 是获取的内容区域my-content
        rotateImg = e.target.children[1]; // rotateImg 是获取的下拉框中的小三角
      }
      // 获取下拉框折叠收起状态  若不为0则是折叠的， 点开设置展开过渡以及旋转三角形 并更新下拉框折叠收起的状态
      if (rotateImg.getAttribute("data") != 0) {
        rotateImg.setAttribute(
          "style",
          `transition: transform${delay}s;transform: rotate(0deg)`
        );
        rotateImg.setAttribute("data", "0");
      } else {
        rotateImg.setAttribute(
          "style",
          `transition: transform${delay}s; transform: rotate(-90deg)`
        );
        rotateImg.setAttribute("data", "1");
      }
      // clientHeight 是指元素本身高度 包括内边距和外边距
      // 如果内容区域大于0，则说明此时内容区域是展开的
      if (ch > 0) {
        target.setAttribute(
          "style",
          `height: ${ch}px; transition: height ${delay}s; overflow: hidden;`
        );
        // 如果内容区域大于0， 则设置高度为0使它折叠起来
        st = setTimeout(() => {
          target.setAttribute(
            "style",
            `height: 0px; overflow: hidden; transition: height ${delay}s;`
          );
          // 给高度设置初始高度 等上面动画执行完再重新给高度设置为0px。 不然点开折叠后第二次点击就点不动了
          cst = setTimeout(() => {
            target.setAttribute("style", "height: 0px;");
            islock = false;
            if (st) {
              clearTimeout(cst);
            }
          }, delay * 1000 - 50);
        }, 50);
      } else {
        // 此时内容区域是折叠的
        ch = getSize(target.children[0]).height;
        // target.setAttribute(
        //   "style",
        //   `height: 0px; transition: height${delay}s; overflow: hidden`
        // );
        st = setTimeout(() => {
          // 下面这段加不加上效果都是一样的
          target.setAttribute(
            "style",
            `height: ${ch}px; overflow: hidden; transition: height${delay}s`
          );
          cst = settimeout(() => {
            target.setAttribute("style", "");
            islock = false;
            if (cst) {
              clearTimeout(cst);
            }
          }, delay * 1000 - 50);
          if (st) {
            clearTimeout(st);
          }
        }, 50);
      }
    });
  }
});

```
