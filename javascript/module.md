# 模块化相关知识点

## 历史模块化标准方案

### AMD
异步模块定义，最古老模块标准，最初由 require.js、r.js、curl.js 库实现
特点:
- 支持异步加载代码
优点：
- 适合在浏览器环境中异步加载模块
- 可以并行加载多个模块
缺点：
- 提高了开发成本，代码的阅读和书写比较困难，模块定义方式的语义不顺畅
- 不符合通用的模块化思维方式，是一种妥协的实现

实现：
- RequireJS
- r.js
- curl

#### define 定义模块
```js
define('模块名',['依赖模块1','依赖模块2',...],function(dep1,dep2,...){
  // 回调函数
  // dep1依赖引用

  // 输出
  return {..}
})

// 例子
define('myModule', ['jquery'], function($) {
    // $ 是 jquery 模块的输出
    $('body').text('hello world');
    // 输出
    var a = 123
    return {a}
});
```

#### require加载模块
```js
require(['依赖模块1','依赖模块2',...], function (dep1,dep2,...){
　　// 使用
});

// 例子
require(['math'], function (math){
　alert(math.add(1,1));
});
```

### CommonJS
为 Node.js 服务器创建的模块系统。
服务器端采用同步加载完成
浏览器端不能采用"同步加载"（synchronous），只能采用"异步加载"（asynchronous）

特点：
- 所有代码都运行在模块作用域，不会污染全局作用域
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存。
- 模块加载的顺序，按照其在代码中出现的顺序。

优点：
- 服务器端模块便于重用
- NPM 中已经有将近20万个可以使用模块包
- 简单并容易使用
缺点：
- 同步的模块加载方式不适合在浏览器环境中，同步意味着阻塞加载，浏览器资源是异步加载的
- 不能非阻塞的并行加载多个模块

实现：
- Node.js
- Browserify，浏览器端的 CommonJS 实现

#### require 加载模块
```js
// 声明 require(‘文件路径’)
const 变量名  = require('加载文件路径') 

// 例子
const example = require('./example.js')
```

#### module对象
Node内部提供一个Module构建函数。所有模块都是Module的实例。
每个模块内部，都有一个module对象，代表当前模块。它有以下属性[module对象](https://javascript.ruanyifeng.com/nodejs/module.html)。
```js
// 例子
var jquery = require('jquery');
exports.$ = jquery;
console.log(module);

/* { id: '.',
  exports: { '$': [Function] },
  parent: null,
  filename: '/path/to/example.js',
  loaded: false,
  children:
   [ { id: '/path/to/node_modules/jquery/dist/jquery.js',
       exports: [Function],
       parent: [Circular],
       filename: '/path/to/node_modules/jquery/dist/jquery.js',
       loaded: true,
       children: [],
       paths: [Object] } ],
  paths:
   [ '/home/user/deleted/node_modules',
     '/home/user/node_modules',
     '/home/node_modules',
     '/node_modules' ]
} */
```

#### module.exports属性
module.exports属性表示当前模块对外输出的接口，其他文件加载该模块，实际上就是读取module.exports变量。
```js
// foo.js
module.exports = function(x) {
  console.log(x);
};

// main.js
var foo = require("./foo");
foo("Hi");
```

#### exports变量
为了方便，Node为每个模块提供一个exports变量，指向module.exports。这等同在每个模块头部，有一行这样的命令。
```js
var exports = module.exports;
```
exports对象添加方法
```js
exports.area = function (r) {
  return Math.PI * r * r;
};

// 不能直接将exports变量指向一个值，因为这样等于切断了exports与module.exports的联系
exports = function(x) {console.log(x)};
```

### UMD
通用的模块系统，与 AMD 和 CommonJS 都兼容

### CMD
Common Module Definition 规范和 AMD 很相似，尽量保持简单，并与 CommonJS 和 Node.js 的 Modules 规范保持了很大的兼容性。
优点：
- 依赖就近，延迟执行
- 可以很容易在 Node.js 中运行
缺点：
- 依赖 SPM 打包，模块的加载逻辑偏重

实现：
- Sea.js
- coolie

```js
define(function(require, exports, module) {
  var $ = require('jquery');
  var Spinning = require('./spinning');
  exports.doSomething = ...
  module.exports = ...
})
```
  
## ES6规范
ECMAScript6 标准增加了 JavaScript 语言层面的模块体系定义。ES6 模块的设计思想，是尽量的静态化，使得编译时就能确定模块的依赖关系，以及输入和输出的变量。CommonJS 和 AMD 模块，都只能在运行时确定这些东西。

```js
import "jquery";
export function doStuff() {}
```

优点：
- 容易进行静态分析
- 面向未来的 ECMAScript 标准
缺点：
- 原生浏览器端还没有实现该标准
- 全新的命令字，新版的 Node.js才支持

实现：
- Babel

### export 命令
- 必须提供对外的接口，必须要通过定义一个接口，模块之间才可以传值，建立了一一对应的关系，接口可以是一个变量或对象
  ```js
  // a.js
  const a = 12
  export a
  // 或
  export { b: 123 }

  // main.js
  import {a,b} from './a.js'
  ```
- export语句输出的接口，与其对应的值是动态绑定关系，即是可以取到模块内部引用实时的值
- export命令可以出现在模块的任何位置，只要处于模块顶层就可以，出现在块级作用域内就报错
   ```js
  // 这会报错
  if(true){
    export { b: 123 }
  }
  ```

### export default 命令
- 为了给用户提供方便，让他们不用阅读文档就能加载模块，就要用到export default命令，为模块指定默认输出
  ```js
  export default { b: 123 }
  ```
- 使用export default时，对应的import语句不需要使用大括号
  ```js
  // a.js
  export default { b: 123 }
  // main.js
  import a from './a.js'
  ```
- 一个模块只能有一个默认输出，因此export default命令只能使用一次
  ```js
  // 这会报错
  export default { a: 123 }
  export default { b: 456 }
  ```

### import命令
- import命令是编译阶段执行的，先于所有代码
- .js后缀可以省略
  ```js
  import { lastName } from './profile';
  ```
- 大括号里面的变量名，必须与被导入模块对外接口的名称相同
  ```js
  // a.js
  export const a = 1

  // 错误
  import {b} from './a.js'
  ```
- import命令输入的变量都是只读的，不允许在加载模块的脚本里面，改写接口，但是如果输入的变量是对象，更改属性是允许的
  ```js
  // wrong
  import { foo } from './module.js'
  foo = { a: 1 }

  // Right
  import { bar } from './module.js'
  bar.a = 1
  
  ```
- 模块文件名，由于不带有路径，必须通过配置，告诉引擎怎么取到这个模块
  ```js
  import {myMethod} from 'util';
  // 需要配置 alias: util
  ```
- import是静态执行，所以不能使用表达式和变量
  ```js
    // 报错
  import { 'f' + 'oo' } from 'my_module';
  // 报错
  let module = 'my_module';
  import { foo } from module;
  // 报错
  if (x === 1) {
    import { foo } from 'module1';
  } else {
    import { foo } from 'module2';
  }
  ```

### as关键字
- 将输入的变量重命名
  ```js
  import { originMoudle as newMoudleName } from './module.js'
  ```