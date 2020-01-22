####判断对象类型

```
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

```
