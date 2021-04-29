# 单线程与异步队列

## JS单线程
所谓"单线程"，就是指一次只能完成一件任务。如果有多个任务，就必须排队，前面一个任务完成，再执行后面一个任务，以此类推。

## JS任务的执行模式

### 同步（Synchronous）
就是JS的单线程模式，在主线程上排队执行的任务，只有前一个任务执行完毕，才能执行后一个任务

### 异步（Asynchronous）
- 不进入主线程、而进入"任务队列"（task queue）的任务，只有"任务队列"通知主线程，某个异步任务可以执行了，该任务才会进入主线程执行
- 在浏览器端，耗时很长的操作都应该异步执行，避免浏览器失去响应。由于JS执行环境是单线程的，但浏览器很多操作都是耗时很长（如AJAX，等请求服务端的操作），如果全部都采用同步执行，服务器性能会急剧下降，很快就会失去响应。所以，"异步模式"甚至是执行这些操作的唯一模式。

## 浏览器常驻线程
- GUI渲染线程：该线程负责页面的渲染
- JS引擎线程：负责JS的解析和执行
- 定时器触发线程：处理定时事件，比如setTimeout,setInterval
- 事件触发线程：处理DOM事件
- 异步http请求线程

\* 其中GUI渲染线程与JS引擎是互斥的
\* 定时器触发线程,事件触发线程,异步http与EventLoop密切相关，相关异步任务执行完毕后会被推入任务队列，例如：比如用户的点击事件，浏览器收到服务器的响应和settimeout中待执行的事件

## 术语解析
### 栈&帧
- 函数调用形成了一个由若干帧组成的栈，（进入一个Function执行，会在栈中推入一个帧, 栈会按照后进先出的原则）


| 语句                       | 当前帧     | 执行栈    |
| -------------------------- | ---------- | --------- |
| 执行bar(7)                 | (anoymous) | []        |
| 进入bar(b),执行let y = 3;  | bar        | [bar]     |
| 执行foo(x * y)             | bar        | [bar]     |
| 进入foo(b),执行let a = 10; | foo        | [foo,bar] |
| 执行return a + b + 11      | foo        | [foo,bar] |
| 执行return foo(x * y);     | bar        | [bar]     |
| 执行console.log(bar(7))    | (anoymous) | []        |

```js
function foo(b) {
  let a = 10;
  return a + b + 11;
}

function bar(x) {
  let y = 3;
  return foo(x * y);
}

console.log(bar(7)); // 返回 42
```

### 任务队列
- "任务队列"是一个事件的队列（也可以理解成消息的队列），"任务队列"负责通知主线程，某个异步任务可以执行了，把相关异步处理的回调函数进入主线程执行
- "任务队列"中的事件，除了IO设备的事件以外，还包括一些用户产生的事件（比如鼠标点击、页面滚动等等）
- "任务队列"是一个先进先出的数据结构

在浏览器宿主环境下，还有两个地方注意
- ”任务列队“未清空之前，浏览器不会有任何render操作
- 如果一个任务回调执行花费时间过长，浏览器无法执行其他task，浏览器会弹出”页面没有回应“警告
- “任务队列”处理完毕后，等待更多任务（即休眠，几乎不消耗 CPU 资源）
```js
document.body.addEventListener('click',()=>{
  // 只要点击，就会推入任务列表
  console.log('click')
})
setTimeout(()=>{
  // 只要到时间，就会推入任务列表
  console.log('async')
},1000)
console.log('sync')
```

## 事件循环 Event Loop
- 所有同步任务都在主线程上执行，形成一个执行栈（execution context stack）
- 主线程运行的时候，产生堆（heap）和栈（stack），栈中的代码会调用各种外部API，产生异步任务。只有完成一项异步任务。它们在"任务队列"中加入事件，表示相关的异步任务可以进入"执行栈"了
- 只要栈中的代码执行完毕，主线程就会去读取"任务队列"，依次执行那些事件所对应的回调函数。
- 主线程不断重复上面的第三步，称之为Event Loop（事件循环）

## 利用事件循环优化场景
- [拆分 CPU 过载任务](https://zh.javascript.info/event-loop#yong-li-1-chai-fen-cpu-guo-zai-ren-wu)
- [进度指示](https://zh.javascript.info/event-loop#yong-li-2-jin-du-zhi-shi)
- [在事件之后做一些事情](https://zh.javascript.info/event-loop#yong-li-3-zai-shi-jian-zhi-hou-zuo-yi-xie-shi-qing)

### 定时器延迟问题
 - 【HTML5标准规定】setTimeout()的第二个参数的最小值（最短间隔），不得低于4毫秒, 如果低于这个值，就会自动增加，老版本的浏览器都将最短间隔设为10毫秒
 - 【GUI渲染线程阻塞】DOM的变动（尤其是涉及页面重新渲染的部分），通常不会立即执行，而是每16毫秒执行一次，这时使用requestAnimationFrame()的效果要好于setTimeout()
 - 【任务队列】setTimeout()只是将事件插入了"任务队列"，必须等当前执行栈执行完成，另外有可能存在比setTimeout更早的任务队列，无法保证回调函数在setTimeout()指定的时间执行

## 宏任务与微任务

### 宏任务 Macrotask
 "任务队列"下的众多的事件回调就是属于宏任务，所以"任务队列"又称”宏任务列表“
- DOM事件、setTimeout与setInterval等绝大多数的宿主环境（Nodejs、浏览器）系统（包括GC回收）发起都属于宏任务

### 微任务 Macrotask
在相对于管理Web API所属事件的“宏任务列表”，还有一种特殊的管理js引擎发起的异步列表，称为“微任务列表”，在chrome的performance面板中会带有“Run Microtasks标识”
- 微任务执行是穿插各个宏任务之间，微任务未执行完毕时，不会执行下一个宏任务
- 如果微任务与宏任务同时触发回调，微任务会优先执行
- Promise, MutationObserver 等绝大多数的由js发起的属于微任务

### queueMicrotask方法调用微任务
为了允许第三方库、框架、polyfills 能使用微任务，Window 暴露了 queueMicrotask() 方法，而 Worker 接口则通过WindowOrWorkerGlobalScope mixin 提供了同名的 queueMicrotask() 方法。

### 使用微任务场景
使用微任务的最主要原因简单归纳为：确保任务顺序的一致性，即便当结果或数据是同步可用的，也要同时减少操作中用户可感知到的延迟而带来的风险。
#### 场景一
(例如保证条件性使用 promises 时的顺序例子)[https://developer.mozilla.org/zh-CN/docs/Web/API/HTML_DOM_API/Microtask_guide]
相当于使用 Promise.resolve(data)作用，增加一个微任务，平衡了两个子句。

#### 场景二
合并批量操作
下面的代码片段创建了一个函数，将多个消息放入一个数组中批处理，通过一个微任务在上下文退出时将这些消息作为单一的对象发送出去。适合埋点等高频触发需要合并一次网络请求场景。
```js
const messageQueue = [];

let sendMessage = message => {
  messageQueue.push(message);

  if (messageQueue.length === 1) {
    queueMicrotask(() => {
      const json = JSON.stringify(messageQueue);
      messageQueue.length = 0;
      setTimeout(()=>{console.log(json),0})
    });
  }
};

sendMessage('1');
sendMessage('2');
sendMessage('3');

// => ["1","2","3"]
```

### 更详细时间循环步骤
1. 从 宏任务 队列中出队（dequeue）并执行最早的任务。
2. 执行所有 微任务：
   - 当微任务队列非空时：
   - 出队（dequeue）并执行最早的微任务。
3. 执行渲染，如果有
4. 如果宏任务队列为空，则休眠直到出现宏任务
5. 转到步骤 1。