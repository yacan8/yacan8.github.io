

## 功能类

### html-webpack-plugin

自动生成html，基本用法：

```js
new HtmlWebpackPlugin({
  filename: 'index.html', // 生成文件名
  template: path.join(process.cwd(), './index.html') // 模班文件
})
```

### mini-css-extract-plugin && extract-text-webpack-plugin

提取css样式，对比：
* mini-css-extract-plugin 为webpack4及以上提供的plugin，支持css chunk
* extract-text-webpack-plugin 只能在webpack3 及一下的版本使用，不支持css chunk

基本用法 extract-text-webpack-plugin：

```js
const ExtractTextPlugin = require("extract-text-webpack-plugin");
 
module.exports = {
  module: {
    rules: [
      {
        test: /\.css$/,
        use: ExtractTextPlugin.extract({
          fallback: "style-loader",
          use: "css-loader"
        })
      }
    ]
  },
  plugins: [
    new ExtractTextPlugin("styles.css"),
  ]
}
```

基本用法 mini-css-extract-plugin：

```js
const MiniCssExtractPlugin = require("mini-css-extract-plugin");
module.exports = {
  plugins: [
    new MiniCssExtractPlugin({
      filename: "[name].css", // 主文件名
      chunkFilename: "[id].css"  // chunk文件名
    })
  ],
  module: {
    rules: [
      {
        test: /\.css$/,
        use: [
          {
            loader: MiniCssExtractPlugin.loader,
            options: {
              publicPath: '/'  // chunk publicPath
            }
          },
          "css-loader"
        ]
      }
    ]
  }
}
```

### copy-webpack-plugin

拷贝资源插件

基本用法：

```js
new CopyWebpackPlugin([
  {
    from: path.join(process.cwd(), './vendor/'),
    to: path.join(process.cwd(), './dist/'),
    ignore: ['*.json']
  }
])
```

### webpack-bundle-analyzer

编译模块分析插件

基本用法：

```js
new BundleAnalyzerPlugin({
  analyzerMode: 'server',
  analyzerHost: '127.0.0.1',
  analyzerPort: 8889,
  reportFilename: 'report.html',
  defaultSizes: 'parsed',
  generateStatsFile: false,
  statsFilename: 'stats.json',
  statsOptions: null,
  logLevel: 'info'
}),
```

### progress-bar-webpack-plugin

编译进度条插件

基本用法：

```js
new ProgressBarPlugin()
```

### webpack.ProvidePlugin

自动加载模块，如 $ 出现，就会自动加载模块；$ 默认为'jquery'的exports

用法：

```js
new webpack.ProvidePlugin({
  $: 'jquery',
})
```

### webpack.DefinePlugin 

定义全局常量

用法：

```js
new webpack.DefinePlugin({
  'process.env': {
    NODE_ENV: JSON.stringify(process.env.NODE_ENV)
  }
})
```

### wbepack.IgnorePlugin

忽略regExp匹配的模块

用法：

```js
new webpack.IgnorePlugin(/^\.\/locale$/, /moment$/)
```

### SplitChunksPlugin

### uglifyjs-webpack-plugin

### DllPlugin && DllReferencePlugin && autodll-webpack-plugin

### happypack && thread-loader

### hard-source-webpack-plugin && cache-loader

### stats-webpack-plugin && PrefetchPlugin

### chunk-manifest-webpack-plugin && webpack-manifest-plugin

### webpack-md5-hash

### compression-webpack-plugin

### optimize-css-assets-webpack-plugin

### assets-webpack-plugin

### clean-webpack-plugin

### speed-measure-webpack-plugin