# webpack性能优化

## 分析
### 分析速度
使用 speed-measure-webpack-plugin 分析打包总耗时，分析每个loader和plugins耗费时间

### 打包体积分析
使用webpack-bundle-analyzer查看打包出来文件大小和依赖

## 使用新版本
没啥好说

## 压缩文件插件
- js 压缩 terser-webpack-plugin 定义parallel 加快压缩
- 去除CSS purgecss-webpack-plugin 擦除无用的 CSS 
- 图片压缩 image-webpack-loader

## 拆分代码
### 开启tree-shaking
需要在webpack配置optimization.usedExports为true, 在product模式下webpack会把无引用的export过滤
但代码必须使用 ES6 Modules 语法才会有效果，并且只适合代码不含任何副作用前提下有效

### entry拆分依赖配置
使用 dependOn 选项你可以与另一个入口 chunk 共享模块
```js
module.exports = {
  //...
  entry: {
    app: { import: './app.js', dependOn: 'react-vendors' },
    'react-vendors': ['react', 'react-dom', 'prop-types'],
  },
};
```
### splitChunks配置
webpack的splitChunks是根据一定条件自动拆分chunks，如果需要对chunks进行自定义拆分，例如把第三方的插件分配为一个大的chunk，其余公共文件分配为一个chunck，可以在这里配置

## 打包速度提升
### 使用多线程提升打包速度
使用thread-loader或者HappyPack插件加速打包速度

### 预先编译资源模块
DllPlugin插件
我们在打包的时候，一般来说第三方模块是不会变化的，所以我们想只要在第一次打包的时候去打包一下第三方模块，并将第三方模块打包到一个特定的文件中，当第二次 webpack 进行打包的时候，就不需要去 node_modules 中去引入第三方模块，而是直接使用我们第一次打包的第三方模块的文件就行。
#### 详细
拆分为dll公共模块打包与业务代码打包两部分，分别设置不同的webpack配置文件
```js
// ./build/webpack.dll.js
module.exports = {
  mode: 'development', // 环境
  entry: {
    // 公共模块入口
    vendors: ['lodash'], // 将 lodash 打包到 vendors.js 下
  },
  output: {
    // 输出配置
    path: resolve('dll'),
    library: '_dll_[name]_[fullhash]',
    filename: '_dll_[name]_[fullhash].js',
  },
  plugins: [
    // 清除结果文件夹
    new CleanWebpackPlugin({
      root: resolve('public'),
    }),
    // 输出dll文件，同时输出记录引用信息manifest
    new webpack.DllPlugin({
      name: '_dll_[name]_[fullhash]', //和output.library中一致，值就是输出的manifest.json中的 name值
      path: path.join(__dirname, '../dll', '[name].manifest.json')
    })
  ]
}

// ./build/webpack.config.js
module.exports = {
  mode: 'development',

  entry: {
    app: resolve('/src/index.js'),
  },
  output: {
    path: resolve('dist'),
    filename: 'js/[name].[fullhash].js',
    publicPath: '/',
    clean: true
  },

  plugins: [
    // 读取html模板
    new HtmlWebpackPlugin({
      template: resolve('/index.html'),
      filename: 'index.html'
    }),
    // 对生成的库文件进行分析，生成库文件与业务模块的映射关系
    new webpack.DllReferencePlugin({
      // context: path.resolve(__dirname, '..'),
      manifest: require('../dll/vendors.manifest')
    }),
    // 把dll文件动态添加到html中
    new AddAssetHtmlPlugin([{
      filepath: path.resolve(__dirname, '../dll/*.js'),
      outputPath: 'js',
      publicPath: 'js',
      includeSourcemap: false
    }]),
  ],
}
```
输出结果
```
dist
└───js
│   │   _dll_vendors_ffd0b68e117822e0ce8e.js
│   │   app.de551faa871d724329dc.js
│   │   app.de551faa871d724329dc.js.map
└───index.html
```

### 编译缓存
- babel-loader添加缓存文件，提升二次构建速度

### 合理使用 sourceMap
在对应的环境使用对应的 sourceMap 很重要
【开发环境】 cheap-module-eval-source-map
【生成环境】 不使用 或 cheap-module-source-map

## 其他
### 合理配置 chunk 的哈希值
在生产环境打包，一定要配置文件的 hash，当文件内容没有改变时候读取浏览器缓存的文件。js文件使用chunkhash、css文件使用contenthash、其他静态资源如（例如图片、字体）使用hash
  - 【fullhash】(原hash)所有文件哈希值相同，只要入口文件内任一文件改变内容，所有哈希值都改变，对于单个chunks，没有任何缓存意义
  - 【chunkhash】根据不同的入口文件(Entry)进行依赖文件解析、构建对应的chunk，生成对应的哈希值。例如公共库和程序入口文件区分开，单独打包构建，采用chunkhash，只要我们不改动公共库的代码，就可以保证其哈希值不变
  - 【contenthash】contenthash 计算与文件内容本身相关,例如如果一个入口文件index.js引用了一个index.css文件，如果更改index.js，index.css的chunkhash也会跟着变，起不了缓存效果，如果css使用contenthash，只要内容不变化就不会变化
 

### 作用域提升
使用ModuleConcatenationPlugin（作用域提升）插件，预编译所有模块，把所有的变量与函数的作用域提升到一个闭包中，主要是优化代码在浏览器中执行速度。
webpack会根据代码决定是否作用域提升

### 预加载资源 webpackPrefetch
告诉浏览器哪些资源需要预加载
