# webpack 优化

## loader 设置include或者exclude

+ 一般第三方包都是打包好的，无需再打包，特别是babel-loader、eslint-loader

```js
{
  test: /\.js$/,
  loader: 'babel-loader',
  include: [resolve('src'), resolve('test')]
}
```

## url-loader

+ 可以将一些比较小的静态文件进行encode，转成base64内联。

```js
{
  test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
  loader: 'url-loader',
  options: {
    // 小于10kb的会转成base64
    limit: 10 * 1024
  }
}
```

## image-webpack-loader

+ 使用image-webpack-loader进行图片压缩，可以和url-loader配合使用，先压缩，然后小的转base64

```js
{
  test: /\.(jpe?g|png|gif|svg)$/,
  loader: 'image-webpack-loader',
  // 保证在其他loader前进行处理
  enforce: 'pre',
}
```

## externals

+ 比如用到一些第三方库，可以不通过webpack打包获取，而且通过挂载在windows上、amd、commonjs等获取

+ 这个在做组件库时需要割掉vue的代码，做按需加载时也需要用到

```js
externals: {
  'Vue': 'window.Vue',
  'Vuex': 'window.Vuex',
  // 或者是commonjs、amd
  'Vue': {
    commonjs: 'vue',
    amd: 'vue'
  },
  'components/button': {
    commonjs: 'gs-ui/lib/button.js'
  }
}
```

## ModuleConcatenationPlugin

+ webpack 3新增的特性，用于将作用域提升，通过减少闭包的数量，能加快js的运行速度和一定程度减少体积

+ 会增加编译时间和破坏热更新，所以只在生成环境使用

+ webpack4中引入`mode`概念，在生成环境中会自动开启

```js
plugins: [
  new webpack.optimize.ModuleConcatenationPlugin()
]
```

## DefinePlugin

+ DefinePlugin将用"production"替换到process.env.NODE_ENV：

+ UglifyJS会移除掉所有if分支 – 因为"production" !== 'production'永远返回 false ，插件理解代码内的判断分支将永远不会执行

```js
plugins: [
  new webpack.DefinePlugin({
    'process.env.NODE_ENV': '"production"'
  })
]
```

## tree shaking

+ 利用es module的静态解析，移除无用依赖

+ babelrc中配置`modules: false`，让babel不要处理import、export，让webpack进行处理

+ webpack会在打包时标记无用代码，最后交给压缩工具进行移除

+ 更详细的解释可以看[这里](https://github.com/linrui1994/note/blob/master/2017-12-22__tree-shaking.md)

## uglifyjs-webpack-plugin

+ 支持`parallel`字段，可以进行并行化构建，可以显着加速构建

```js
plugins: [
  new UglifyJsPlugin({
    parallel: true
  })
]
```

## CommonsChunkPlugin

+ 用于提取一些公共代码，一般是用于提取第三方库，这个在上线部署时很有必要，因为出现bug，一般都是业务代码的问题，第三方库相对比较稳定，这个时候第三方库代码不应该受到影响，CommonsChunkPlugin可以减少热更的代价

```js
// 将第三方库单独打包成一个vender
new webpack.optimize.CommonsChunkPlugin({
  name: 'vendor',
  minChunks: function (module) {
    // any required modules inside node_modules are extracted to vendor
    return (
      module.resource &&
      /\.js$/.test(module.resource) &&
      module.resource.indexOf(
        path.join(__dirname, '../node_modules')
      ) === 0
    )
  }
})
```

+ 但是单独抽离vender是不够的，如果业务代码发生变化，还是会导致vender的hash值发生变化，缓存的作用也随着消失，解决这个问题需要再打一个包，一般命名manifest或者runtime

```js
new webpack.optimize.CommonsChunkPlugin({
  name: 'runtime',
  // 无限大代表需要在所有的模块中共享的代码才会打包进来，意味着没有任何模块打包进来，只有webpack的运行代码
  minChunks: Infinity
})
```

+ webpack4中已经移除，使用optimization.splitChunks和optimization.runtimeChunk代替

## NamedModulesPlugin

+ NamedModulesPlugin可以让webpack打包时的chunk id变得稳定，如果我们手动添加一个依赖，会使得chunk id发生变化，这个插件做的就是让id稳定下来

+ webpack4中在开发模式中自动开启（--mode development）

```js
plugins: [
  new NamedModulesPlugin()
]
```
## 懒加载

+ webpack2以上支持动态import()，webpack1使用require.ensure，返回一个promise

+ webpack遇到import()，会单独将其抽离成一个js文件，等到运行时才去加载

+ 一般用的多是在单页面中，像vue-router、react-router等都支持import()进行懒加载

```js
async function doSomeThing () {
  const {default: moment} = await import('moment')
  console.log(moment().format('YYYY-MM-DD'))
}

setTimeout(() =+ {
  doSomeThing()
}, 1000)
```

## 缓存处理

+ 一般情况下是让服务器添加相关的头，比如`Cache-Control`或者`Expires`

+ 也可以利用h5的manifest缓存，可以做离线缓存

+ 可以利用`appcache-webpack-plugin`，即使处于离线状态也是可以正常打开网页的，不过慢慢被淘汰，取而代之的是PWA

```js
plugins: [
  new AppCachePlugin({
    network: ['*'], // No network access allowed!
    output: 'cache.appcache'
  }),
]
```

```
// output
CACHE MANIFEST
# c5a4d08e79f57e7c0b18

/static/log26736681.png
/static/index.d156bde75c3265560b9b.js
/static/vendor.1fe523904e40ca0cfc76.js
/static/manifest.5969cefb99f458a69963.js
/static/index.5202b34274a8b8bad9c76962a800f30c.css
/static/../index.html

NETWORK:
*
```

+ 把输出的cache.appcache添加到html标签上就可以了