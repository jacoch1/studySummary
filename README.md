# studySummary
规定一个单位时间，在这个单位时间内，只能有一次触发事件的回调函数执行，如果在同一个单位时间内某事件被触发多次，只有一次能生效。

实现
时间戳实现
注意:第一次事件会被触发,最后一次不会触发函数

throttle.js

function throttle(func, wait) {
  let _this, args;
  // 之前的时间戳
  let old = 0;
  return function () {
    // 保存this
    _this = this;
    //保存arguments
    args = arguments;

    // 获取当前时间戳
    let now = new Date().valueOf();
    if (now - old > wait) {
      // 立即执行
      func.apply(_this, args);
      lod = now;

    }
  }
}
注意和防抖函数进行区分

定时器实现
第一次不会被触发,最后一次会触发

function throttle(func, wait) {
  let _this, args, timeout;
  return function () {
    _this = this;
    args = arguments;

    if (!timeout) {
      timeout = setTimeout(() => {
        timeout = null;
        func.apply(_this, args);
      }, wait)
    }
  }
}
节流进阶
原生的节流函数有第三个参数,用于控制是否首次事件是否被立即调用,以及最后一次事件是否被调用

 container.onmousemove = _.throttle(doThrottle, 1000, {
      leading: false,   //是否立即执行
      trailing: true     //最后一次是否执行
      //两者不能同为false,否则就会产生bug,会导致触发事件立即执行一次后就失效了!
    })
实现

function throttle(func, wait, options) {
  let _this, args, timeout;
  let old = 0;     //时间戳
  //如果没有options就将其设置为空对象
  if (!options) options = {};

  let later = function () {
    old = new Date().valueOf();
    timeout = null;
    func.apply(_this, args);
  }
  return function () {
    _this = this;
    args = arguments;
    let now = new Date().valueOf();
    if (options.leading === false && !old) {
      old = now;
    }
    if (now - old > wait) {
      // 第一次直接执行
      if (timeout) {
        clearTimeout(timeout);
        timeout = null;
      }
      func.apply(_this, args);
      old = now;
    } else if (!timeout && options.trailing !== false) {
      //最后一次也被执行
      timeout = setTimeout(later, wait)
    }
  }
}
测试:

function doThrottle(e) {
      container.innerHTML = count++
    }
 container.onmousemove = throttle(doThrottle, 1000,{
      leading:false,
      trailing:true
    });
应用场景
DOM元素的拖拽功能的实现
射击游戏中的每隔一段时间射击
计算鼠标移动的距离
监听scroll滚动事件
搜索联想
高频点击提交,表单重复提交
防抖节流之间的异同比较
相同:

都可以通过setTimeout实现
目的都是:降低回调执行频率,节省计算资源
不同:

函数防抖: 在一段连续操作结束后,处理回调,利用clearTimeout和setTimeout实现.
函数节流:在一段连续操作中,每一段时间只执行一次,频率较高的时间中使用来提高性能.
函数防抖关注一定时间连续触发的事件只在最后执行,而函数节流侧重于一段时间内只执行一次!
© 2020 GitHub, Inc.
Terms
Privacy
Security
Status
Help
Contact GitHub
Pricing
API
Training
Blog
About
