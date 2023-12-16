# Webpack 和 Babel

## 基本配置

- 拆分配置和merge
- 启动本地服务
- 处理ES6
- 处理样式
- 处理图片
- 模块化

webpack.common.js

```js
// 公用webpack配置
const path = require('path')
const HtmlWebpackPlugin = require('html-webpack-plugin')
const { srcPath, distPath } = require('./paths')

module.exports = {
    entry: path.join(srcPath, 'index'),
    module: {
        rules: [
            {
                test: /\.js$/,
                loader: ['babel-loader'],
                include: srcPath,
                exclude: /node_modules/
            },
            // {
            //     test: /\.vue$/,
            //     loader: ['vue-loader'],
            //     include: srcPath
            // },
            // {
            //     test: /\.css$/,
            //     // loader 的执行顺序是：从后往前（知识点）
            //     loader: ['style-loader', 'css-loader']
            // },
            {
                test: /\.css$/,
                // loader 的执行顺序是：从后往前
                loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
            },
            {
                test: /\.less$/,
                // 增加 'less-loader' ，注意顺序
                loader: ['style-loader', 'css-loader', 'less-loader']
            }
        ]
    },
    plugins: [
        new HtmlWebpackPlugin({
            template: path.join(srcPath, 'index.html'),
            filename: 'index.html'
        })
    ]
}

```

webpack.dev.js

```js
const path = require('path')
const webpack = require('webpack')
const webpackCommonConf = require('./webpack.common.js')
const { smart } = require('webpack-merge')
const { srcPath, distPath } = require('./paths')

module.exports = smart(webpackCommonConf, {
    mode: 'development',
    module: {
        rules: [
            // 直接引入图片 url
            {
                test: /\.(png|jpg|jpeg|gif)$/,
                use: 'file-loader'
            }
        ]
    },
    plugins: [
        new webpack.DefinePlugin({
            // window.ENV = 'development'
            ENV: JSON.stringify('development')
        })
    ],
    devServer: {
        port: 8080,
        progress: true,  // 显示打包的进度条
        contentBase: distPath,  // 根目录
        open: true,  // 自动打开浏览器
        compress: true,  // 启动 gzip 压缩

        // 设置代理
        proxy: {
            // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
            '/api': 'http://localhost:3000',

            // 将本地 /api2/xxx 代理到 localhost:3000/xxx
            '/api2': {
                target: 'http://localhost:3000',
                pathRewrite: {
                    '/api2': ''
                }
            }
        }
    }
})
```

webpack.prod.js

```js
const path = require('path')
const webpack = require('webpack')
const { CleanWebpackPlugin } = require('clean-webpack-plugin')
const webpackCommonConf = require('./webpack.common.js')
const { smart } = require('webpack-merge')
const { srcPath, distPath } = require('./paths')

module.exports = smart(webpackCommonConf, {
    mode: 'production',
    output: {
        filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
        path: distPath,
        // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
    },
    module: {
        rules: [
            // 图片 - 考虑 base64 编码的情况
            {
                test: /\.(png|jpg|jpeg|gif)$/,
                use: {
                    loader: 'url-loader',
                    options: {
                        // 小于 5kb 的图片用 base64 格式产出
                        // 否则，依然延用 file-loader 的形式，产出 url 格式
                        limit: 5 * 1024,

                        // 打包到 img 目录下
                        outputPath: '/img1/',

                        // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
                        // publicPath: 'http://cdn.abc.com'
                    }
                }
            },
        ]
    },
    plugins: [
        new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
        new webpack.DefinePlugin({
            // window.ENV = 'production'
            ENV: JSON.stringify('production')
        })
    ]
})

```

## 高级配置

- 多入口

  ```js
  // webpack.common.js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: {
          index: path.join(srcPath, 'index.js'),
          other: path.join(srcPath, 'other.js')
      },
      module: {
          rules: [
              {
                  test: /\.js$/,
                  loader: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/
              },
              // {
              //     test: /\.css$/,
              //     // loader 的执行顺序是：从后往前
              //     loader: ['style-loader', 'css-loader']
              // },
              {
                  test: /\.css$/,
                  // loader 的执行顺序是：从后往前
                  loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
              },
              {
                  test: /\.less$/,
                  // 增加 'less-loader' ，注意顺序
                  loader: ['style-loader', 'css-loader', 'less-loader']
              }
          ]
      },
      plugins: [
          // new HtmlWebpackPlugin({
          //     template: path.join(srcPath, 'index.html'),
          //     filename: 'index.html'
          // })
  
          // 多入口 - 生成 index.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html',
              // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
              chunks: ['index']  // 只引用 index.js
          }),
          // 多入口 - 生成 other.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'other.html'),
              filename: 'other.html',
              chunks: ['other']  // 只引用 other.js
          })
      ]
  }
  // webpack.dev.js
  const path = require('path')
  const webpack = require('webpack')
  const webpackCommonConf = require('./webpack.common.js')
  const { smart } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'development',
      module: {
          rules: [
              // 直接引入图片 url
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: 'file-loader'
              }
          ]
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('development')
          })
      ],
      devServer: {
          port: 8080,
          progress: true,  // 显示打包的进度条
          contentBase: distPath,  // 根目录
          open: true,  // 自动打开浏览器
          compress: true,  // 启动 gzip 压缩
  
          // 设置代理
          proxy: {
              // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
              '/api': 'http://localhost:3000',
  
              // 将本地 /api2/xxx 代理到 localhost:3000/xxx
              '/api2': {
                  target: 'http://localhost:3000',
                  pathRewrite: {
                      '/api2': ''
                  }
              }
          }
      }
  })
  // webpack.prod.js
  const path = require('path')
  const webpack = require('webpack')
  const { CleanWebpackPlugin } = require('clean-webpack-plugin')
  const webpackCommonConf = require('./webpack.common.js')
  const { smart } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'production',
      output: {
          // filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
          filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
          path: distPath,
          // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
      },
      module: {
          rules: [
              // 图片 - 考虑 base64 编码的情况
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: {
                      loader: 'url-loader',
                      options: {
                          // 小于 5kb 的图片用 base64 格式产出
                          // 否则，依然延用 file-loader 的形式，产出 url 格式
                          limit: 5 * 1024,
  
                          // 打包到 img 目录下
                          outputPath: '/img1/',
  
                          // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
                          // publicPath: 'http://cdn.abc.com'
                      }
                  }
              },
          ]
      },
      plugins: [
          new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('production')
          })
      ]
  })
  ```

- 抽离CSS文件

  ```js
  // webpack.common.js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: {
          index: path.join(srcPath, 'index.js'),
          other: path.join(srcPath, 'other.js')
      },
      module: {
          rules: [
              {
                  test: /\.js$/,
                  loader: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/
              }
              // css 处理
          ]
      },
      plugins: [
          // new HtmlWebpackPlugin({
          //     template: path.join(srcPath, 'index.html'),
          //     filename: 'index.html'
          // })
  
          // 多入口 - 生成 index.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html',
              // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
              chunks: ['index']  // 只引用 index.js
          }),
          // 多入口 - 生成 other.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'other.html'),
              filename: 'other.html',
              chunks: ['other']  // 只引用 other.js
          })
      ]
  }
  // webpack.dev.js  开发环境下的打包无需抽离css
  const path = require('path')
  const webpack = require('webpack')
  const webpackCommonConf = require('./webpack.common.js')
  const { smart } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'development',
      module: {
          rules: [
              // 直接引入图片 url
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: 'file-loader'
              },
              // {
              //     test: /\.css$/,
              //     // loader 的执行顺序是：从后往前
              //     loader: ['style-loader', 'css-loader']
              // },
              {
                  test: /\.css$/,
                  // loader 的执行顺序是：从后往前
                  loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
              },
              {
                  test: /\.less$/,
                  // 增加 'less-loader' ，注意顺序
                  loader: ['style-loader', 'css-loader', 'less-loader']
              }
          ]
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('development')
          })
      ],
      devServer: {
          port: 8080,
          progress: true,  // 显示打包的进度条
          contentBase: distPath,  // 根目录
          open: true,  // 自动打开浏览器
          compress: true,  // 启动 gzip 压缩
  
          // 设置代理
          proxy: {
              // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
              '/api': 'http://localhost:3000',
  
              // 将本地 /api2/xxx 代理到 localhost:3000/xxx
              '/api2': {
                  target: 'http://localhost:3000',
                  pathRewrite: {
                      '/api2': ''
                  }
              }
          }
      }
  })
  
  // webpack.prod.js
  const path = require('path')
  const webpack = require('webpack')
  const { smart } = require('webpack-merge')
  const { CleanWebpackPlugin } = require('clean-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')
  const TerserJSPlugin = require('terser-webpack-plugin')
  const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
  const webpackCommonConf = require('./webpack.common.js')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'production',
      output: {
          // filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
          filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
          path: distPath,
          // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
      },
      module: {
          rules: [
              // 图片 - 考虑 base64 编码的情况
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: {
                      loader: 'url-loader',
                      options: {
                          // 小于 5kb 的图片用 base64 格式产出
                          // 否则，依然延用 file-loader 的形式，产出 url 格式
                          limit: 5 * 1024,
  
                          // 打包到 img 目录下
                          outputPath: '/img1/',
  
                          // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
                          // publicPath: 'http://cdn.abc.com'
                      }
                  }
              },
              // 抽离 css
              {
                  test: /\.css$/,
                  loader: [
                      MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                      'css-loader',
                      'postcss-loader'
                  ]
              },
              // 抽离 less --> css
              {
                  test: /\.less$/,
                  loader: [
                      MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                      'css-loader',
                      'less-loader',
                      'postcss-loader'
                  ]
              }
          ]
      },
      plugins: [
          new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('production')
          }),
  
          // 抽离 css 文件
          new MiniCssExtractPlugin({
              filename: 'css/main.[contentHash:8].css'
          })
      ],
  
      optimization: {
          // 压缩 css
          minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
      }
  })
  ```

- 抽离公共代码

  ```js
  // webpack.common.js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: {
          index: path.join(srcPath, 'index.js'),
          other: path.join(srcPath, 'other.js')
      },
      module: {
          rules: [
              {
                  test: /\.js$/,
                  loader: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/
              }
          ]
      },
      plugins: [
          // new HtmlWebpackPlugin({
          //     template: path.join(srcPath, 'index.html'),
          //     filename: 'index.html'
          // })
  
          // 多入口 - 生成 index.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html',
              // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
              chunks: ['index', 'vendor', 'common']  // 要考虑代码分割
          }),
          // 多入口 - 生成 other.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'other.html'),
              filename: 'other.html',
              chunks: ['other', 'common']  // 考虑代码分割
          })
      ]
  }
  // webpack.dev.js 开发环境无需抽离公共代码
  const path = require('path')
  const webpack = require('webpack')
  const webpackCommonConf = require('./webpack.common.js')
  const { smart } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'development',
      module: {
          rules: [
              // 直接引入图片 url
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: 'file-loader'
              },
              // {
              //     test: /\.css$/,
              //     // loader 的执行顺序是：从后往前
              //     loader: ['style-loader', 'css-loader']
              // },
              {
                  test: /\.css$/,
                  // loader 的执行顺序是：从后往前
                  loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
              },
              {
                  test: /\.less$/,
                  // 增加 'less-loader' ，注意顺序
                  loader: ['style-loader', 'css-loader', 'less-loader']
              }
          ]
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('development')
          })
      ],
      devServer: {
          port: 8080,
          progress: true,  // 显示打包的进度条
          contentBase: distPath,  // 根目录
          open: true,  // 自动打开浏览器
          compress: true,  // 启动 gzip 压缩
  
          // 设置代理
          proxy: {
              // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
              '/api': 'http://localhost:3000',
  
              // 将本地 /api2/xxx 代理到 localhost:3000/xxx
              '/api2': {
                  target: 'http://localhost:3000',
                  pathRewrite: {
                      '/api2': ''
                  }
              }
          }
      }
  })
  
  // webpack.prod.js
  const path = require('path')
  const webpack = require('webpack')
  const { smart } = require('webpack-merge')
  const { CleanWebpackPlugin } = require('clean-webpack-plugin')
  const MiniCssExtractPlugin = require('mini-css-extract-plugin')
  const TerserJSPlugin = require('terser-webpack-plugin')
  const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
  const webpackCommonConf = require('./webpack.common.js')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = smart(webpackCommonConf, {
      mode: 'production',
      output: {
          // filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
          filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
          path: distPath,
          // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
      },
      module: {
          rules: [
              // 图片 - 考虑 base64 编码的情况
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: {
                      loader: 'url-loader',
                      options: {
                          // 小于 5kb 的图片用 base64 格式产出
                          // 否则，依然延用 file-loader 的形式，产出 url 格式
                          limit: 5 * 1024,
  
                          // 打包到 img 目录下
                          outputPath: '/img1/',
  
                          // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
                          // publicPath: 'http://cdn.abc.com'
                      }
                  }
              },
              // 抽离 css
              {
                  test: /\.css$/,
                  loader: [
                      MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                      'css-loader',
                      'postcss-loader'
                  ]
              },
              // 抽离 less
              {
                  test: /\.less$/,
                  loader: [
                      MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                      'css-loader',
                      'less-loader',
                      'postcss-loader'
                  ]
              }
          ]
      },
      plugins: [
          new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('production')
          }),
  
          // 抽离 css 文件
          new MiniCssExtractPlugin({
              filename: 'css/main.[contentHash:8].css'
          })
      ],
  
      optimization: {
          // 压缩 css
          minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
  
          // 分割代码块
          splitChunks: {
              chunks: 'all',
              /**
               * initial 入口 chunk，对于异步导入的文件不处理
                  async 异步 chunk，只对异步导入的文件处理
                  all 全部 chunk
               */
  
              // 缓存分组
              cacheGroups: {
                  // 第三方模块
                  vendor: {
                      name: 'vendor', // chunk 名称
                      priority: 1, // 权限更高，优先抽离，重要！！！
                      test: /node_modules/,
                      minSize: 0,  // 大小限制
                      minChunks: 1  // 最少复用过几次
                  },
  
                  // 公共的模块
                  common: {
                      name: 'common', // chunk 名称
                      priority: 0, // 优先级
                      minSize: 0,  // 公共模块的大小限制
                      minChunks: 2  // 公共模块最少复用过几次
                  }
              }
          }
      }
  })
  ```

  

- 懒加载

  - webpack默认支持

- 处理JSX

  ```js
  // npm install --save-dev @babel/preset-react
  // babel.config.json
  // 不带参数
  {
    "presets": ["@babel/preset-react"]
  }
  // 带参数
  {
    "presets": [
      [
        "@babel/preset-react",
        {
          "pragma": "dom", // default pragma is React.createElement (only in classic runtime)
          "pragmaFrag": "DomFrag", // default is React.Fragment (only in classic runtime)
          "throwIfNamespace": false, // defaults to true
          "runtime": "classic" // defaults to classic
          // "importSource": "custom-jsx-library" // defaults to react (only in automatic runtime)
        }
      ]
    ]
  }
  ```

- 处理Vue

  - npm i vue-loader

  ```js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: path.join(srcPath, 'index'),
      module: {
          rules: [
              {
                  test: /\.js$/,
                  loader: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/
              },
              // {
              //     test: /\.vue$/,
              //     loader: ['vue-loader'],
              //     include: srcPath
              // },
              // {
              //     test: /\.css$/,
              //     // loader 的执行顺序是：从后往前（知识点）
              //     loader: ['style-loader', 'css-loader']
              // },
              {
                  test: /\.css$/,
                  // loader 的执行顺序是：从后往前
                  loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
              },
              {
                  test: /\.less$/,
                  // 增加 'less-loader' ，注意顺序
                  loader: ['style-loader', 'css-loader', 'less-loader']
              }
          ]
      },
      plugins: [
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html'
          })
      ]
  }
  
  ```

## module chunk bundle的区别

- module 各个源码文件，webpack中一切皆模块
- chunk 多模块合并成的，如entry import() splitChunk
- bundle 最终的输出文件

## webpack性能优化

### 优化打包效率

- 优化babel-loader（可用于生产环境）

  ```js
  {
      test:/\.js$/,
      use:['babel-loader?cacheDirectory'], // 开启缓存
      include:path.resolve(__dirname,'src'), // 明确范围
      exclude:path.resolve(__dirname,'node_modules') // 排除范围
  }
  ```

- IgnorePlugin 避免引用无用模块（可用于生产环境）

  ```js
  plugins: [
      new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
      new webpack.DefinePlugin({
          // window.ENV = 'production'
          ENV: JSON.stringify('production')
      }),
  
      // 忽略 moment 下的 /locale 目录
      new webpack.IgnorePlugin(/\.\/locale/, /moment/)
  ]
  ```

- noParse 避免重复打包（可用于生产环境）

  ```js
  module.exports = {
      module: {
          // 忽略对 react.min.js 文件的递归解析处理
          noParse:[/react\.min\.js$/]
      }
  }
  ```

  - IgnorePlugin 直接不引入，代码中没有
  - noParse引入，但不打包

- happyPack 多进程打包（可用于生产环境）

- ParallelUglifyPlugin 多进程压缩JS（可用于生产环境）

  - 关于开启多进程

- 自动刷新（不可用于生产环境）

  - 整个网页全部刷新，速度较慢
  - 整个网页全部刷新，状态会丢失

- 热更新（不可用于生产环境）

  - 新代码生效，无需重新刷新页面，速度快，状态不会丢失

  ```js
  // webpack.common.js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: {
          index: path.join(srcPath, 'index.js'),
          other: path.join(srcPath, 'other.js')
      },
      module: {
          rules: [
              // babel-loader
          ]
      },
      plugins: [
          // new HtmlWebpackPlugin({
          //     template: path.join(srcPath, 'index.html'),
          //     filename: 'index.html'
          // })
  
          // 多入口 - 生成 index.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html',
              // chunks 表示该页面要引用哪些 chunk （即上面的 index 和 other），默认全部引用
              chunks: ['index', 'vendor', 'common']  // 要考虑代码分割
          }),
          // 多入口 - 生成 other.html
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'other.html'),
              filename: 'other.html',
              chunks: ['other', 'vendor', 'common']  // 考虑代码分割
          })
      ]
  }
  // webpack.dev.js
  const path = require('path')
  const webpack = require('webpack')
  const webpackCommonConf = require('./webpack.common.js')
  const { smart } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  const HotModuleReplacementPlugin = require('webpack/lib/HotModuleReplacementPlugin');
  
  module.exports = smart(webpackCommonConf, {
      mode: 'development',
      entry: {
          // index: path.join(srcPath, 'index.js'),
          index: [
              'webpack-dev-server/client?http://localhost:8080/',
              'webpack/hot/dev-server',
              path.join(srcPath, 'index.js')
          ],
          other: path.join(srcPath, 'other.js')
      },
      module: {
          rules: [
              {
                  test: /\.js$/,
                  loader: ['babel-loader?cacheDirectory'],
                  include: srcPath,
                  // exclude: /node_modules/
              },
              // 直接引入图片 url
              {
                  test: /\.(png|jpg|jpeg|gif)$/,
                  use: 'file-loader'
              },
              // {
              //     test: /\.css$/,
              //     // loader 的执行顺序是：从后往前
              //     loader: ['style-loader', 'css-loader']
              // },
              {
                  test: /\.css$/,
                  // loader 的执行顺序是：从后往前
                  loader: ['style-loader', 'css-loader', 'postcss-loader'] // 加了 postcss
              },
              {
                  test: /\.less$/,
                  // 增加 'less-loader' ，注意顺序
                  loader: ['style-loader', 'css-loader', 'less-loader']
              }
          ]
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('development')
          }),
          new HotModuleReplacementPlugin()
      ],
      devServer: {
          port: 8080,
          progress: true,  // 显示打包的进度条
          contentBase: distPath,  // 根目录
          open: true,  // 自动打开浏览器
          compress: true,  // 启动 gzip 压缩
  
          hot: true,
  
          // 设置代理
          proxy: {
              // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
              '/api': 'http://localhost:3000',
  
              // 将本地 /api2/xxx 代理到 localhost:3000/xxx
              '/api2': {
                  target: 'http://localhost:3000',
                  pathRewrite: {
                      '/api2': ''
                  }
              }
          }
      },
      // watch: true, // 开启监听，默认为 false
      // watchOptions: {
      //     ignored: /node_modules/, // 忽略哪些
      //     // 监听到变化发生后会等300ms再去执行动作，防止文件更新太快导致重新编译频率太高
      //     // 默认为 300ms
      //     aggregateTimeout: 300,
      //     // 判断文件是否发生变化是通过不停的去询问系统指定文件有没有变化实现的
      //     // 默认每隔1000毫秒询问一次
      //     poll: 1000
      // }
  })
  
  // webpack.prod.js
      const path = require('path')
      const webpack = require('webpack')
      const { smart } = require('webpack-merge')
      const { CleanWebpackPlugin } = require('clean-webpack-plugin')
      const MiniCssExtractPlugin = require('mini-css-extract-plugin')
      const TerserJSPlugin = require('terser-webpack-plugin')
      const OptimizeCSSAssetsPlugin = require('optimize-css-assets-webpack-plugin')
      const HappyPack = require('happypack')
      const ParallelUglifyPlugin = require('webpack-parallel-uglify-plugin')
      const webpackCommonConf = require('./webpack.common.js')
      const { srcPath, distPath } = require('./paths')
  
      module.exports = smart(webpackCommonConf, {
          mode: 'production',
          output: {
              // filename: 'bundle.[contentHash:8].js',  // 打包代码时，加上 hash 戳
              filename: '[name].[contentHash:8].js', // name 即多入口时 entry 的 key
              path: distPath,
              // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
          },
          module: {
              rules: [
                  // js
                  {
                      test: /\.js$/,
                      // 把对 .js 文件的处理转交给 id 为 babel 的 HappyPack 实例
                      use: ['happypack/loader?id=babel'],
                      include: srcPath,
                      // exclude: /node_modules/
                  },
                  // 图片 - 考虑 base64 编码的情况
                  {
                      test: /\.(png|jpg|jpeg|gif)$/,
                      use: {
                          loader: 'url-loader',
                          options: {
                              // 小于 5kb 的图片用 base64 格式产出
                              // 否则，依然延用 file-loader 的形式，产出 url 格式
                              limit: 5 * 1024,
  
                              // 打包到 img 目录下
                              outputPath: '/img1/',
  
                              // 设置图片的 cdn 地址（也可以统一在外面的 output 中设置，那将作用于所有静态资源）
                              // publicPath: 'http://cdn.abc.com'
                          }
                      }
                  },
                  // 抽离 css
                  {
                      test: /\.css$/,
                      loader: [
                          MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                          'css-loader',
                          'postcss-loader'
                      ]
                  },
                  // 抽离 less
                  {
                      test: /\.less$/,
                      loader: [
                          MiniCssExtractPlugin.loader,  // 注意，这里不再用 style-loader
                          'css-loader',
                          'less-loader',
                          'postcss-loader'
                      ]
                  }
              ]
          },
          plugins: [
              new CleanWebpackPlugin(), // 会默认清空 output.path 文件夹
              new webpack.DefinePlugin({
                  // window.ENV = 'production'
                  ENV: JSON.stringify('production')
              }),
  
              // 抽离 css 文件
              new MiniCssExtractPlugin({
                  filename: 'css/main.[contentHash:8].css'
              }),
  
              // 忽略 moment 下的 /locale 目录
              new webpack.IgnorePlugin(/\.\/locale/, /moment/),
  
              // happyPack 开启多进程打包
              new HappyPack({
                  // 用唯一的标识符 id 来代表当前的 HappyPack 是用来处理一类特定的文件
                  id: 'babel',
                  // 如何处理 .js 文件，用法和 Loader 配置中一样
                  loaders: ['babel-loader?cacheDirectory']
              }),
  
              // 使用 ParallelUglifyPlugin 并行压缩输出的 JS 代码
              new ParallelUglifyPlugin({
                  // 传递给 UglifyJS 的参数
                  // （还是使用 UglifyJS 压缩，只不过帮助开启了多进程）
                  uglifyJS: {
                      output: {
                          beautify: false, // 最紧凑的输出
                          comments: false, // 删除所有的注释
                      },
                      compress: {
                          // 删除所有的 `console` 语句，可以兼容ie浏览器
                          drop_console: true,
                          // 内嵌定义了但是只用到一次的变量
                          collapse_vars: true,
                          // 提取出出现多次但是没有定义成变量去引用的静态值
                          reduce_vars: true,
                      }
                  }
              })
          ],
  
          optimization: {
              // 压缩 css
              minimizer: [new TerserJSPlugin({}), new OptimizeCSSAssetsPlugin({})],
  
              // 分割代码块
              splitChunks: {
                  chunks: 'all',
                  /**
                   * initial 入口chunk，对于异步导入的文件不处理
                      async 异步chunk，只对异步导入的文件处理
                      all 全部chunk
                  */
  
                  // 缓存分组
                  cacheGroups: {
                      // 第三方模块
                      vendor: {
                          name: 'vendor', // chunk 名称
                          priority: 1, // 权限更高，优先抽离，重要！！！
                          test: /node_modules/,
                          minSize: 0,  // 大小限制
                          minChunks: 1  // 最少复用过几次
                      },
  
                      // 公共的模块
                      common: {
                          name: 'common', // chunk 名称
                          priority: 0, // 优先级
                          minSize: 0,  // 公共模块的大小限制
                          minChunks: 2  // 公共模块最少复用过几次
                      }
                  }
              }
          }
      })
  
  ```

  

- DllPlugin 动态链接库插件（不可用于生产环境）

  - webpack已内置DllPlugin支持
  - DllPlugin 打包出dll 文件
  - DllReferencePlugin 使用 dll文件

  ```js
  // webpack.common.js
  const path = require('path')
  const HtmlWebpackPlugin = require('html-webpack-plugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
      entry: path.join(srcPath, 'index'),
      module: {
          rules: [
              {
                  test: /\.js$/,
                  use: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/
              },
          ]
      },
      plugins: [
          new HtmlWebpackPlugin({
              template: path.join(srcPath, 'index.html'),
              filename: 'index.html'
          })
      ]
  }
  
  // webpack.dev.js
  const path = require('path')
  const webpack = require('webpack')
  const { merge } = require('webpack-merge')
  const webpackCommonConf = require('./webpack.common.js')
  const { srcPath, distPath } = require('./paths')
  
  // 第一，引入 DllReferencePlugin
  const DllReferencePlugin = require('webpack/lib/DllReferencePlugin');
  
  module.exports = merge(webpackCommonConf, {
      mode: 'development',
      module: {
          rules: [
              {
                  test: /\.js$/,
                  use: ['babel-loader'],
                  include: srcPath,
                  exclude: /node_modules/ // 第二，不要再转换 node_modules 的代码
              },
          ]
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('development')
          }),
          // 第三，告诉 Webpack 使用了哪些动态链接库
          new DllReferencePlugin({
              // 描述 react 动态链接库的文件内容
              manifest: require(path.join(distPath, 'react.manifest.json')),
          }),
      ],
      devServer: {
          port: 8080,
          progress: true,  // 显示打包的进度条
          contentBase: distPath,  // 根目录
          open: true,  // 自动打开浏览器
          compress: true,  // 启动 gzip 压缩
  
          // 设置代理
          proxy: {
              // 将本地 /api/xxx 代理到 localhost:3000/api/xxx
              '/api': 'http://localhost:3000',
  
              // 将本地 /api2/xxx 代理到 localhost:3000/xxx
              '/api2': {
                  target: 'http://localhost:3000',
                  pathRewrite: {
                      '/api2': ''
                  }
              }
          }
      }
  })
  // webpack.dll.js
  const path = require('path')
  const DllPlugin = require('webpack/lib/DllPlugin')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = {
    mode: 'development',
    // JS 执行入口文件
    entry: {
      // 把 React 相关模块的放到一个单独的动态链接库
      react: ['react', 'react-dom']
    },
    output: {
      // 输出的动态链接库的文件名称，[name] 代表当前动态链接库的名称，
      // 也就是 entry 中配置的 react 和 polyfill
      filename: '[name].dll.js',
      // 输出的文件都放到 dist 目录下
      path: distPath,
      // 存放动态链接库的全局变量名称，例如对应 react 来说就是 _dll_react
      // 之所以在前面加上 _dll_ 是为了防止全局变量冲突
      library: '_dll_[name]',
    },
    plugins: [
      // 接入 DllPlugin
      new DllPlugin({
        // 动态链接库的全局变量名称，需要和 output.library 中保持一致
        // 该字段的值也就是输出的 manifest.json 文件 中 name 字段的值
        // 例如 react.manifest.json 中就有 "name": "_dll_react"
        name: '_dll_[name]',
        // 描述动态链接库的 manifest.json 文件输出时的文件名称
        path: path.join(distPath, '[name].manifest.json'),
      }),
    ],
  }
  // webpack.prod.js
  const path = require('path')
  const webpack = require('webpack')
  const webpackCommonConf = require('./webpack.common.js')
  const { merge } = require('webpack-merge')
  const { srcPath, distPath } = require('./paths')
  
  module.exports = merge(webpackCommonConf, {
      mode: 'production',
      output: {
          filename: 'bundle.[contenthash:8].js',  // 打包代码时，加上 hash 戳
          path: distPath,
          // publicPath: 'http://cdn.abc.com'  // 修改所有静态文件 url 的前缀（如 cdn 域名），这里暂时用不到
      },
      plugins: [
          new webpack.DefinePlugin({
              // window.ENV = 'production'
              ENV: JSON.stringify('production')
          })
      ]
  })
  
  ```

  package.json

  ```json
  {
    "name": "08-webpack-dll-demo",
    "version": "1.0.0",
    "description": "",
    "main": "index.js",
    "scripts": {
      "test": "echo \"Error: no test specified\" && exit 1",
      "dev": "webpack serve --config build/webpack.dev.js",
      "dll": "webpack --config build/webpack.dll.js"
    },
    "keywords": [],
    "author": "",
    "license": "ISC",
    "dependencies": {
      "react": "^16.12.0",
      "react-dom": "^16.12.0"
    },
    "devDependencies": {
      "@babel/core": "^7.7.5",
      "@babel/preset-env": "^7.7.5",
      "@babel/preset-react": "^7.7.4",
      "babel-loader": "^8.0.6",
      "html-webpack-plugin": "^4.5.0",
      "webpack": "^5.10.3",
      "webpack-cli": "^4.2.0",
      "webpack-dev-server": "^3.11.0",
      "webpack-merge": "^5.7.1"
    }
  }
  ```

  

### 优化产出代码

- 小图片base64编码

- bundle加hash

- 懒加载

- 提取公共代码

- IgnorePlugin

- 使用CDN加速

- 使用production

  - 自动开启压缩代码
  - Vue React 等会自动删掉调试代码（如开发环境的warning）
  - 启动Tree-Shaking（没有用的会被删除掉）
    - ES6 Module才会使Tree-Shaking生效，Commonjs则不行
    - ES6 Module静态引入，编译时引入
    - Commonjs 动态引入，执行时引入
    - 只有ES6 Module才能静态分析，实现Tree-Shaking

- Scope Hosting

  - 代码提及更小

  - 创建函数作用域更少

  - 代码可读性更好

    ```js
    const ModuleConcatenationPlugin = require('webpack/lib/optimize/ModuleConcatenationPlugin')
    
    module.exports = {
        resolve: {
            mainFields:['hsnext:main','browser','main']
        },
        plugins:[
            // 开启Scope Hosting
            new ModuleConcatenationPlugin()
        ]
    }
    ```


## babel

环境搭建&基本配置

- 环境搭建
- .babelrc配置
- presets和plugins

babel-polyfill与babel-runtime

- Babel 7.4之后被弃用了，推荐直接使用core-js regenerator

- 配置按需引入

  ```js
  {
      "presets":[
          [
              "@babel/preset-env",
              {
                  "useBuiltIns":"usage",
                  "corejs":3
              }
          ]
      ],
      "plugins":[
          [
              "@babel/plugin-transform-runtime",
              {
                  "absoluteRuntime":false,
                  "corejs":3,
                  "helpers":true,
                  "regenerator":true,
                  "useESModules":false
              }
          ]
      ]
  }
  ```

- 涉及的问题
  - 污染全局环境，需要使用babel-runtime来解决



webpack与babel面试题

构建流程概述

前端代码为何要进行构建和打包？

- 代码层面
  - 体积更小（Tree-Shaking、压缩、合并），加载更快
  - 编译 高级语言或语法（TS，ES6+，模块化，scss）
  - 兼容性和错误检查（Polyfill、postcss、eslint）
- 研发层面
  - 统一高效的开发环境
  - 统一的构建流程和产出标准
  - 集成公司构建规范

module chunk bundle 分别什么意思，有何区别

- module 各个源码文件
- chunk 多模块 合并成的，如entry import() splitChunk
- bundle 最终输出的文件

loader和plugin区别

- loader 模块转换器，如less——> css
- plugin 扩展插件，如HtmlWebpackPlugin

常见的loader和plugin

- https://www.webpackjs.com/loaders
- https://www.webpackjs.com/plugins

babel和webpack的区别

- babel JS新语法的编译工具，不关心模块化
- webpack 打包构建工具，是多个loader plugin 的集合

如何产出一个lib

- 参考webpack.dll.js

- output.library

  ```js
  output:{
      //lib文件名
      filename:'lodash.js',
      // 输出lib到dist目录下
      path:distPath,
      // lib的全局变量名
      library:'lodash'
  }
  ```

  

webpack如何实现懒加载

- import()
- 结合Vue React 异步组件
- 结合Vue-router react-router加载异步组件

为何Proxy不能被Polyfill

- 如Class可以用function模拟
- 如Promise可以用callback来模拟
- 但Proxy的功能用Object.defineProperty无法模拟

webpack常见性能优化

1. webpack优化构建速度（可用于生产环境）
   - 优化babel-loader
   - IgnorePlugin
   - noParse
   - happyPack多进程
   - ParallelUglyfyPluginduo进程
2. webpack优化构建速度（不可用于生产环境）
   - 自动刷新
   - 热更新
   - DllPlugin
3. webpack优化产出代码
   - 小图片base64编码
   - bundle加hash
   - 懒加载
   - 提取公共代码
   - 使用CDN加速
   - IgnorePlugin
   - 使用production
   - Scope Hoisting

babel-runtime和babel-polyfill的区别

- babel-polyfill会污染全局
- babel-runtime不会污染全局
- 产出第三方 lib要用babel-runtime