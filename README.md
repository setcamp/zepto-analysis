<h1 align="center">zepto-reading</h1>
解读zepto设计和源码分析

<p align="center"><img src="http://www.zeptojs.cn/skin/logo.png" /></p>



* 事件委托

```JavaScript
//事件委托
$.fn.delegate = function(selector, event, callback) {
  return this.each(function(i, element) {
    add(element, event, callback, selector, function(fn) {
      return function(e) {
        //如果事件对象是element里的元素,取与selector相匹配的
        var evt, match = $(e.target).closest(selector, element).get(0)
        if (match) {
          //evt成了一个拥有preventDefault，stopImmediatePropagatio,stopPropagation方法，currentTarge,liveFiredn属性的对象,另也有e的默认属性
          evt = $.extend(createProxy(e), {
            currentTarget: match,
            liveFired: element
          })
          return fn.apply(match, [evt].concat([].slice.call(arguments, 1)))
        }
      }
    })
  })
}
//取消事件委托
$.fn.undelegate = function(selector, event, callback) {
  return this.each(function() {
    remove(this, event, callback, selector)
  })
}
```
