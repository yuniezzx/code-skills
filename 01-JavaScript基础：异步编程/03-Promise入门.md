
## 概述
promise 中文翻译为承诺，或者说是期约。表示对一上不存在结果的一个替身。早期的期约机制在 jQuery 和 Dojo 中出现，随着 CommonJS 项目实现的 Promises/A 规范的流行，最终于 ECMAScript6 中以相同名字制定了 Promise/A+ 规范，即 Promise 类型。

一个 Promise 是一个代理，它代表一个在创建 promise 时不一定已知的值。它允许你将处理程序与异步操作的最终成功值或失败原因关联起来。当主线程读取异步代码时会和同步一样被调用，但此时返回的是一个 Promise，主线程接收到 Promise 继续执行，异步代码被添加到队列等待执行。当队列中异步代码执行后会关联到Promise，此时Promise 会改变状态，从待`定变`为`已兑现`或`已拒绝`。

一个 Promise 必然处于以下几种状态之一：
- 待定（pending）：初始状态，既没有被兑现，也没有被拒绝。
- 已兑现（fulfilled）：意味着操作成功完成。
- 已拒绝（rejected）：意味着操作失败。

![promise](https://github.com/yuniezzx/Images/blob/main/code-markdown/JS/promises.png?raw=true)

### 创建 Promise
Promise 在网络请求中经常用到，这里通过XMLHttpRequest 或者更现代的 fetch API 来创建一个基于 Promise 的 AJAX 调用。

```javascript
function ajaxRequest(url) {
  return new Promise((resolve, reject) => {
    const xhr = new XMLHttpRequest();
    xhr.open('GET', url, true);

    xhr.onload = function () {
      if (xhr.status >= 200 && xhr.status < 300) {
        resolve(xhr.responseText);
      } else {
        reject(new Error('Request failed with status code: ' + xhr.status));
      }
    };

    xhr.onerror = function () {
      reject(new Error('Network error occurred'));
    };

    xhr.send();
  });
}

// 使用 Promise
ajaxRequest('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => {
    console.log('成功获取数据:', response);
  })
  .catch(error => {
    console.error('请求失败:', error);
  });
```

说明：
1. `ajaxRequest()` 函数返回的是值是一个 Promise;
	> 注意： new Promise 是个同步代码，此时返回值为 `pending` 状态；内部的会调函数会放入队列等待执行。
2. Promise 接受两个参数，resolve（成功时调用）和 reject（失败时调用）。
3. XMLHttpRequest 的设置：使用 xhr.open('GET', url, true) 设置请求，xhr.onload 回调函数处理成功的响应，通过 resolve 返回结果；而 xhr.onerror 处理网络错误，通过 reject 抛出错误。
4. 使用 Promise：我们通过 .then() 来处理成功的返回结果，并通过 .catch() 捕获错误。

#### 静态方法
`Promise.resolve()` 和` Promise.reject()`，它们是 Promise 的静态方法，用于快速创建已经处于**已完成（fulfilled）或已拒绝（rejected）**状态的 Promise。

##### Promise.resolve():
Promise.resolve(value)：用于返回一个已经成功的 Promise，value 是该 Promise 的成功结果。
无论是同步值还是 Promise 对象，Promise.resolve() 都会将其转化为一个 Promise 对象。

```javascript
Promise.resolve(value);
// 如果 value 是一个普通的值，那么返回一个立即被解决（resolved）的 Promise，then 直接处理这个值。
// 如果 value 是一个 Promise，Promise.resolve() 直接返回这个 Promise。
```

示例：

```javascript
// 1.立即返回一个成功的 Promise
const p = Promise.resolve('成功的结果');

p.then(result => {
  console.log(result);  // 输出: 成功的结果
});

// 2.将现有的 Promise 转换为成功状态
const existingPromise = new Promise((resolve) => resolve('已完成的 Promise'));
const p = Promise.resolve(existingPromise);

p.then(result => {
  console.log(result);  // 输出: 已完成的 Promise
});

// 3.使用 Promise.resolve() 包装一个同步值
const value = 42;
const p = Promise.resolve(value);

p.then(result => {
  console.log(result);  // 输出: 42
});
```

##### Promise.reject()
Promise.reject(reason)：用于返回一个已经**失败（rejected）**的 Promise，reason 是该 Promise 的拒绝原因（通常是错误信息）。

Promise.reject() 会返回一个已经被拒绝的 Promise，传递的 reason 会被作为 catch() 或 then() 的错误处理函数的参数。

示例：

```javascript
// 1. 立即返回一个失败的 Promise
const p = Promise.reject(new Error('失败的原因'));

p.catch(error => {
  console.error(error.message);  // 输出: 失败的原因
});

// 2. 使用 Promise.reject() 模拟失败的异步操作
function asyncOperation() {
  return Promise.reject('操作失败');
}

asyncOperation()
  .then(result => {
    console.log(result);  // 不会执行，因为 Promise 被拒绝
  })
  .catch(error => {
    console.error('错误:', error);  // 输出: 错误: 操作失败
  });
```


### 链式调用
Promise.prototype.then()、Promise.prototype.catch() 和 Promise.prototype.finally() 方法用于将进一步的操作与已敲定的 Promise 相关联。由于这些方法返回 Promise，因此它们可以被链式调用。

```javascript
const myPromise = new Promise((resolve, reject) => {
  setTimeout(() => {
    resolve("foo");
  }, 300);
});

myPromise
  .then(handleFulfilledA, handleRejectedA)
  .then(handleFulfilledB, handleRejectedB)
  .then(handleFulfilledC, handleRejectedC);

```
`.then()` 最多接收两个参数；及成功状态的的 Promise 和失败状态的 Promise。上列 `myPromise` 中返回的状态如果是 `fulfilled`则调用 `handleFulfilledA`, 反之返回的是 `rejected` 则调用 `handleRejectedA`。

而且 `.then()` 中即使缺少返回 Promise 对象的回调函数，处理程序仍然会继续进行下一个链式调用。因此，在最终的 .catch() 之前，可以安全地省略每个链式调用中处理已拒绝状态的回调函数。

```javascript
myPromise
  .then(handleFulfilledA)
  .then(handleFulfilledB)
  .then(handleFulfilledC)
  .catch(handleRejectedAny);
```

所谓的 `.catch()` 其实就是 `.then(null, onRejected)` 的简写形式，只处理错误的情况。通常放在链式末尾进行错误处理。

当 `.catch()`写在多个 `.then()` 之间时，它的作用和行为稍有不同。具体来说，.catch() 只会捕获它之前链条中的错误，而不是捕获之后 .then() 中的错误。

```javascript
ajaxRequest('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => {
    console.log('成功获取数据:', response);
    return JSON.parse(response); // 假设返回的数据是 JSON 格式，解析 JSON
  })
  .catch(error => {
    console.error('捕获解析错误:', error); // 这个 catch 只会捕获 JSON.parse 相关的错误
  })
  .then(parsedData => {
    console.log('解析后的数据:', parsedData);
  })
  .catch(error => {
    console.error('捕获最终错误:', error); // 捕获整个链条中的剩余错误
  });
```

最后，`.finally()`用于无论 Promise 的状态是**成功**还是**失败**，都执行指定的回调函数。finally() 通常用于处理清理操作，比如关闭连接、释放资源等，无论操作结果如何都需要执行的操作。

```javascript
ajaxRequest('https://jsonplaceholder.typicode.com/posts/1')
  .then(response => {
    console.log('成功获取数据:', response);
    return JSON.parse(response);
  })
  .catch(error => {
    console.error('捕获到错误:', error);
    return { error: '解析失败，返回默认值' };
  })
  .finally(() => {
    console.log('请求完成，不论成功或失败，都执行这个回调');
  });
```

说明：
-  无论 AJAX 请求是否成功、JSON 解析是否出错，finally() 中的回调都会在 .then() 和 .catch() 之后执行。
-  finally() 中不会接收到 resolve 或 reject 的值，它的主要作用是用于清理工作或结束操作。


> finally() 是为了确保某些操作总是会被执行，例如关闭数据库连接、清理资源等。
> 它通常在 .then() 和 .catch() 之后使用，确保它无论 Promise 成功还是失败都会执行。





### 参考链接

- JavaScript高级程序设计（第 4 版）
- [JavaScript Promise迷你书（中文版）](http://liubin.org/promises-book/#ch2-promise-resolve)
