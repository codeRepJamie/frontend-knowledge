# webpack相关知识点

## Loader与Plugin区别
### Loader
loader 用于对模块的源代码进行转换。loader 可以使你在 import 或"加载"模块时预处理文件。因此，loader 类似于其他构建工具中“任务(task)”，并提供了处理前端构建步骤的强大方法。loader 可以将文件从不同的语言（如 TypeScript）转换为 JavaScript，或将内联图像转换为 data URL。loader 甚至允许你直接在 JavaScript 模块中 import CSS文件！

loader在 module.rules 中配置，作为模块的解析规则，类型为数组。每一项都是一个 Object，内部包含了 test(类型文件)、loader、options (参数)等属性。

### Plugin
插件是 webpack 的支柱功能。webpack 自身也是构建于，插件目的在于解决 loader 无法实现的其他事。基于事件流框架 Tapable，插件可以扩展 Webpack 的功能，在 Webpack 运行的生命周期中会广播出许多事件，Plugin 可以监听这些事件，在合适的时机通过 Webpack 提供的 API 改变输出结果

Plugin 在 plugins 中单独配置，类型为数组，每一项是一个 Plugin 的实例，参数都通过构造函数传入。

## Webpack构建流程

### 简单版
- 初始化
  启动构建，读取与合并配置参数，加载 Plugin，实例化 Compiler
- 编译
  从 Entry 出发，针对每个 Module 串行调用对应的 Loader 去翻译文件的内容，再找到该 Module 依赖的 Module，递归地进行编译处理
- 输出
  将编译后的 Module 组合成 Chunk，将 Chunk 转换成文件，输出到文件系统中

### 详细版
- 初始化参数（entry-option）
  从配置文件和 Shell 语句中读取与合并参数，得出最终的参数

- 开始编译 （run）
  用上一步得到的参数初始化 Compiler 对象，加载所有配置的插件，执行对象的 run 方法开始执行编译
- 确定入口(make)
  根据配置中的 entry 找出所有的入口文件
- 编译模块(make)
  从入口文件出发，调用所有配置的 Loader 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理
- 完成模块编译(build:before-resolve->build-module->normal-module-loader->program)
  在经过上面使用 Loader 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系

- 输出资源(seal)
  根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 Chunk，再把每个 Chunk 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
- 输出完成(emit)
  在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统

![image](/assest/weboack-flow.png)