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

* 浏览器判断

```JavaScript
;(function($) {
  function detect(ua) {
    var os = this.os = {},
      browser = this.browser = {},
      webkit = ua.match(/WebKit\/([\d.]+)/),
      android = ua.match(/(Android)\s+([\d.]+)/),
      ipad = ua.match(/(iPad).*OS\s([\d_]+)/),
      iphone = !ipad && ua.match(/(iPhone\sOS)\s([\d_]+)/),
      webos = ua.match(/(webOS|hpwOS)[\s\/]([\d.]+)/),
      touchpad = webos && ua.match(/TouchPad/),
      kindle = ua.match(/Kindle\/([\d.]+)/),
      silk = ua.match(/Silk\/([\d._]+)/),
      blackberry = ua.match(/(BlackBerry).*Version\/([\d.]+)/),
      bb10 = ua.match(/(BB10).*Version\/([\d.]+)/),
      rimtabletos = ua.match(/(RIM\sTablet\sOS)\s([\d.]+)/),
      playbook = ua.match(/PlayBook/),
      chrome = ua.match(/Chrome\/([\d.]+)/) || ua.match(/CriOS\/([\d.]+)/),
      firefox = ua.match(/Firefox\/([\d.]+)/)

      // Todo: clean this up with a better OS/browser seperation:
      // - discern (more) between multiple browsers on android
      // - decide if kindle fire in silk mode is android or not
      // - Firefox on Android doesn't specify the Android version
      // - possibly devide in os, device and browser hashes

    if (browser.webkit = !! webkit) browser.version = webkit[1]

    if (android) os.android = true, os.version = android[2]
    if (iphone) os.ios = os.iphone = true, os.version = iphone[2].replace(/_/g, '.')
    if (ipad) os.ios = os.ipad = true, os.version = ipad[2].replace(/_/g, '.')
    if (webos) os.webos = true, os.version = webos[2]
    if (touchpad) os.touchpad = true
    if (blackberry) os.blackberry = true, os.version = blackberry[2]
    if (bb10) os.bb10 = true, os.version = bb10[2]
    if (rimtabletos) os.rimtabletos = true, os.version = rimtabletos[2]
    if (playbook) browser.playbook = true
    if (kindle) os.kindle = true, os.version = kindle[1]
    if (silk) browser.silk = true, browser.version = silk[1]
    if (!silk && os.android && ua.match(/Kindle Fire/)) browser.silk = true
    if (chrome) browser.chrome = true, browser.version = chrome[1]
    if (firefox) browser.firefox = true, browser.version = firefox[1]

    os.tablet = !! (ipad || playbook || (android && !ua.match(/Mobile/)) || (firefox && ua.match(/Tablet/)))
    os.phone = !! (!os.tablet && (android || iphone || webos || blackberry || bb10 || (chrome && ua.match(/Android/)) || (chrome && ua.match(/CriOS\/([\d.]+)/)) || (firefox && ua.match(/Mobile/))))
  }

  detect.call($, navigator.userAgent)
  // make available to unit tests
  $.__detect = detect

})(Zepto)
```
