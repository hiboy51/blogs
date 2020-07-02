## Promise里隐藏的秘密

### 先说setTimeOut和setInterval

- 我们知道，设置定时器是可以指定时间的

  ```js
  let handle = setTimeOut(function() {
  // do stuff
  }, 10);
  ```

- js规定定时器的时间间隔：setTimeOut不低于4ms，setInterval不低于10ms，浏览器非活跃页面不低于1000ms。
各家的实现不一样
- js引擎是单线程的，异步事件会被维护在异步队列中。在每一个事件循环完后，才会执行异步队列中的事件。计时器即是
在每个主事件循环完成后才判断是否过时，然后执行。
- 所以有两个重要事实:
  1. 设置间隔低于最小间隔的，将按最小间隔实现，例如

      ```js
      // 按最小间隔执行
      setTimeOut(doStuff, 0)
      // 按最小间隔执行
      setTimeOut(doStuff, 2)
      // ! 缺省按最小间隔执行
      setTimeOut(doStuff)
      ```

  2. 定时器并不是准时执行的。取决于到时的主事件循环的执行时间

### 说回Promise

- 我们经常用Promise来忽略同步和异步的差别。使某个操作的差异在外部被抹平。例如：

  ```js
  new Promise((res, rej) => {
      if (condition) {
        // ! 返回同步结果
        res(100)
      }
      else {
          // ! 返回异步结果
          setTimeOut(res, 1000);
      }
  });
  ```

- 实际上，Promise为了统一同步和异步。在内部是将同步结果延迟到下一个事件循环再返回,类似于这样：

  ```js
  // 某同步值 const a = 100;
  setTimeOut(function() {
      res(a)
  })
  ```

- 基于此可以认为，Promise总是不会立即返回的，哪怕是 **Promise.resolve()** 或者 **Promise.reject()**

### 总结

在时效性非常强的场景下。我们不宜用Promise来实现同步异步的抽象。该结论同样适用于 **async await** 块，因为我们知道
 **async await** 只是Promise的语法糖
