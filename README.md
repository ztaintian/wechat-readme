1、uni-app打包太大问题

    vue.config.js添加代码
    var path = require('path')
    const UglifyJsPlugin = require('uglifyjs-webpack-plugin');
    const BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin;
    function resolve (dir) {
      return path.join(__dirname, './', dir)
    }
    let Isproduct = process.env.NODE_ENV=='production'
    let opt = {};
    if(Isproduct){
      opt = {
        splitChunks: {
          chunks: "all",          
          minSize: 30000,         
          minChunks: 1,           
          maxAsyncRequests: 5,    
          maxInitialRequests: 3, 
          automaticNameDelimiter: '~', 
          name: true,                  
          cacheGroups: { 
            vendors: {  
              test: /[\\/]node_modules[\\/]/,
              priority: -10, 
              filename: 'vendor.js',
            },
            commons: {
              name: "all",
              test: resolve("./src/resource"), 
              minChunks: 2, 
              priority: 5,
              reuseExistingChunk: true
            },
            default: {
              minChunks: 2, 
              priority: 5,
              reuseExistingChunk: true, 
              filename: 'common.js'
            }
          }
        }
      }
    }
    module.exports = {
      chainWebpack: config => {
        // 打包分析
        // if (Isproduct) {
        //  config.plugin('webpack-report')
        //   .use(BundleAnalyzerPlugin, [{
        //    analyzerMode: 'static',
        //   }]);
        // }
      },
      configureWebpack: {
        //关闭 webpack 的性能提示
        performance: {
          hints:false
        },

        //警告 webpack 的性能提示
        performance: {
          hints:'warning',
          //入口起点的最大体积
          maxEntrypointSize: 50000000,
          //生成文件的最大体积
          maxAssetSize: 30000000,
          //只给出 js 文件的性能提示
          assetFilter: function(assetFilename) {
            return assetFilename.endsWith('.js');
          }
        },
        optimization: opt
      },
      css: {
        extract: true,
        sourceMap: false,
        loaderOptions: {},
        modules: false
      }
    }

  2、修改build下的app.js如下

    require('./common/runtime.js')
    require('./common.js')
    require('./common/main.js')
    require('./vendor.js')