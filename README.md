# webpack-test
webpack4.0配置
安装
npm install webpack webpack-cli -D

配置
新建 webpack.config.js

html模板插件 html-webpack-plugin

每次构建清除文件插件 clean-webpack-plugin

package.json

scripts 可自行添加配置

'scripts': { 'dev': 'webpack-dev-server', 'build': 'webpack' }

本地服务 npm install webpack-dev-server --save-dev devServer

css处理 npm install style-loader css-loader node-sass sass-loader --save-dev

抽离CSS mini-css-extract-plugin

压缩CSS npm install optimize-css-assets-webpack-plugin --save-dev

添加w3c前缀 npm install postcss-loader autoprefixer --save-dev

js处理 babel npm i -D babel-loader @babel/core @babel/preset-env

压缩js npm install uglifyjs-webpack-plugin --save-dev

避免升级带来的影响 给CSS、JS打版本（加hash值）

处理图片 npm install file-loader --save-dev

处理字体 文件 图片 url-loader 类似file-loader 包含file-loader 文件转化为base64

合并webpack 配置文件 npm install webpack-merge --save-dev

resolve 解析后缀 @/index/index.js 可以省略.js 别名：例如@/view/index.vue

外部扩展 externals 如果cdn引用了jQuery 那就使用这个 不会打包到打包文件中 因为配置了 这是一个外部引用 externals: { 'jquery': 'jQuery' }, js 文件中 就可以使用jquery

webpack优化 npm install --save-dev webpack-bundle-analyzer

const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const {CleanWebpackPlugin} = require('clean-webpack-plugin')
const MiniCssExtractPlugin = require('mini-css-extract-plugin')
const OptimizeCssAssetsPlugin = require('optimize-css-assets-webpack-plugin')
const UglifyJsPlugin = require('uglifyjs-webpack-plugin')
// entry 字符串 数组 对象三种方式
module.exports = {
  mode: 'production',
  entry: './src/main.js',
  // 解析后缀  别名匹配 @/view/...
  resolve :{
    extensions: ['.js', '.scss'],
    alias: {
      '@': path.resolve(__dirname, 'src')
    }
  },
  externals: {
    'jquery': 'jQuery'
  },
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: '[name].[hash:6].js'
  },
  devServer: {
    contentBase: './dist',
    host: 'localhost',
    port: 3000,
    compress: true, // 服务器压缩
    hot: true // 热更新
  },
  module: {
    rules: [
      {
        test: /\.(sa|sc|c)ss$/,
        use: [
        MiniCssExtractPlugin.loader,
        {
          loader: 'css-loader',
          options: {
            sourceMap: true
          }
        },
        {
          loader: 'postcss-loader',
          options: {
            ident: 'postcss',
            sourceMap: true,
            plugins: (loader) => [
              require('autoprefixer')
            ]
          }
        },
        {
          loader: 'sass-loader',
          options: {
            sourceMap: true
          }
        }
      ]
      },
      {
        test: /\.(jpg|png|jpeg)$/,
        use: [
          {
            loader: 'url-loader',
            options: {
              limit: 10000
            }
          }
        ]
      },
      {
        test: /\.js$/,
        exclude: /node_modules/,
        use:[
          {
            loader: 'babel-loader'
          },{
            loader: 'eslint-loader',
            options: {
              fix: true
            }
          }
        ]
      }
    ]
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[name].[hash:6].css',
      chunkFilename: '[id].[hash:6].css'
    }),
    new OptimizeCssAssetsPlugin(),
    new HtmlWebpackPlugin({
      title: '这是一个测试项目',
      filename: 'main.html',
      template: './src/index.html',
      minify: {
          collapseWhitespace: true
      }
    }),
    new CleanWebpackPlugin({
      cleanOnceBeforeBuildPatterns: ['dist']
    }),
    new webpack.HotModuleReplacementPlugin()
  ],
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        cache: true
      })
    ]
  }
}
