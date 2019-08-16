---
title: nuxt初步
date: 2019-08-16 11:44:20
tags: nuxt
---
# 介绍

## SSR与CSR的相爱相杀

首先看一下传统的web开发，传统的web开发是，客户端向服务端发送请求，服务端查询数据库，拼接HTML字符串（模板），通过一系列的数据处理之后，把整理好的HTML返回给客户端,浏览器相当于打开了一个页面。这种比如我们经常听说过的jsp,PHP,aspx也就是传统的MVC的开发。

SPA应用，到了Vue、React，单页面应用优秀的用户体验，逐渐成为了主流，页面整体式javaScript渲染出来的，称之为客户端渲染CSR。SPA渲染过程。由客户端访问URL发送请求到服务端，返回HTML结构（但是SPA的返回的HTML结构是非常的小的，只有一个基本的结构）。客户端接收到返回结果之后，在客户端开始渲染HTML，渲染时执行对应javaScript，最后渲染template，渲染完成之后，再次向服务端发送数据请求，注意这里时数据请求，服务端返回json格式数据。客户端接收数据，然后完成最终渲染。

SPA虽然给服务器减轻了压力，但是也是有缺点的：

- 首屏渲染时间比较长：必须等待JavaScript加载完毕，并且执行完毕，才能渲染出首屏。
- SEO不友好：爬虫只能拿到一个div元素，认为页面是空的，不利于SEO。

为了解决如上两个问题，出现了SSR解决方案，后端渲染出首屏的DOM结构返回，前端拿到内容带上首屏，后续的页面操作，再用单页面路由和渲染，称之为服务端渲染(SSR)。

SSR渲染流程是这样的，客户端发送URL请求到服务端，服务端读取对应的url的模板信息，在服务端做出html和数据的渲染，渲染完成之后返回html结构，客户端这时拿到的之后首屏页面的html结构。所以用户在浏览首屏的时候速度会很快，因为客户端不需要再次发送ajax请求。并不是做了SSR我们的页面就不属于SPA应用了，它仍然是一个独立的spa应用。

SSR是处于CSR与SPA应用之间的一个折中的方案，在渲染首屏的时候在服务端做出了渲染，注意仅仅是首屏，其他页面还是需要在客户端渲染的，在服务端接收到请求之后并且渲染出首屏页面，会携带着剩余的路由信息预留给客户端去渲染其他路由的页面。

# 渲染流程

从服务端到客户端的一个过程，主要查看官网。

注意：vue的生命周期created和beforeCreated在客户端和服务端同时执行。

# 布局思想

- layouts
- pages
- components

# 配置文件

```js

const config = {
  server: {
    host: 'localhost',
    port: 16700 // default: 3000
  },
  mode: 'universal',
  // 源码目录,分层使之有客户端和服务端
  srcDir: 'client/',
  // 打包目录
  buildDir: 'nuxt-dist',
  env: {
    baseUrl: process.env.NODE_ENV === 'production' ? 'linker.sz.datastory.com.cn/m' : 'linker.sz.datastory.com.cn'
  },
  router: {
    base: process.env.NODE_ENV === 'production' ? '/m/' : '',
    middleware: 'permission'
  },
  /*
  ** Headers of the page
  */
  head: {
    title: '数说领客',
    meta: [{ charset: 'utf-8' },
      { name: 'viewport', content: 'width=device-width, initial-scale=1,minimum-scale=1, maximum-scale=1' },
      { hid: 'description', name: 'description', content: process.env.npm_package_description || '' }],
    link: [{ rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }],
    // eslint-disable-next-line array-bracket-newline
    script: [
      // flexible
      { src: '//g.tbcdn.cn/mtb/lib-flexible/0.3.4/??flexible_css.js,flexible.js', type: 'text/javascript', charset: 'utf-8' },
      // 接入微信js
      { src: '//res.wx.qq.com/open/js/jweixin-1.4.0.js', type: 'text/javascript', charset: 'utf-8' },
      { src: '//cdnjs.cloudflare.com/ajax/libs/gsap/1.20.3/TweenMax.min.js', type: 'text/javascript', charset: 'utf-8' }
    // eslint-disable-next-line array-bracket-newline
    ]
  },
  /*
  ** Customize the progress-bar color
  */
  loading: { color: '#fff' },
  /*
  ** Global CSS
  */
  css: ['~/assets/styles/app.less'],
  /*
  ** Plugins to load before mounting the App
  */
   plugins: [{ src: '~plugins/vant', ssr: false },
    { src: '~/plugins/proxy.ts' },
    { src: '~/plugins/dayjs.ts', ssr: true },
    { src: '~/plugins/ramda.ts', ssr: true },
    { src: '~/plugins/throttle-debounce.ts', ssr: true },
    { src: '~/plugins/echarts.ts', ssr: false }],
  /*
  ** Nuxt.js modules
  */
  // eslint-disable-next-line array-bracket-newline
  modules: [
    // Doc: https://axios.nuxtjs.org/usage
    '@nuxtjs/axios',
    '@nuxtjs/pwa',
    '@nuxtjs/eslint-module',
    '@nuxtjs/style-resources'],
  styleResources: {
    less: ['./assets/styles/variable.less']
  },

  /*
  ** Axios module configuration
  ** See https://axios.nuxtjs.org/options
  */
  axios: {
    proxy: true
  },
  /**
   * proxy中间件
  */
  proxy: {
    '/mock': {
      target: 'https://easy-mock.com/mock/5cc7f9cba978170c5029aaeb/linker',
      changeOrigin: true,
      pathRewrite: {
        '^/mock': ''
      }
    },
    '/api': {
      target: 'http://linker.sz.datastory.com.cn'
      // pathRewrite: {
      //   '^/api': '/'
      // }
    }
  },
  /*
  ** Build configuration
  */
  build: {
    analyze: true,
    /*
    ** You can extend webpack config here
    */
    extend(config, ctx) {
      if (ctx.isClient) {
        config.output.filename = ctx.isDev ? '[name].js' : '[name].[chunkhash].js'
        config.output.chunkFilename = ctx.isDev ? '[name].js' : '[name].[chunkhash].js'
      }
    },
    optimization: {
      splitChunks: {
        chunks: 'all',
        automaticNameDelimiter: '.',
        maxAsyncRequests: 7,
        cacheGroups: {
          ramda: {
            test: /node_modules[\\/]ramda/,
            chunks: 'all',
            priority: 20,
            name: true
          },
          echarts: {
            test: /node_modules[\\/]echarts/,
            chunks: 'all',
            priority: 20,
            name: true
          }
        }
      },
      runtimeChunk: 'single'
    },
    splitChunks: {
      layouts: false,
      pages: true,
      commons: true
    },
    filenames: {
      app: ({ isDev, isClient, isServer }) => isDev ? '[name].js' : '[name].[chunkhash].js',
      chunk: ({ isDev, isClient, isServer }) => isDev ? '[name].js' : '[name].[chunkhash].js',
      css: ({ isDev }) => isDev ? '[name].css' : '[contenthash].css',
      img: ({ isDev }) => isDev ? '[path][name].[ext]' : 'img/[hash:7].[ext]',
      font: ({ isDev }) => isDev ? '[path][name].[ext]' : 'fonts/[hash:7].[ext]',
      video: ({ isDev }) => isDev ? '[path][name].[ext]' : 'videos/[hash:7].[ext]'
    },
    babel: {
      // eslint-disable-next-line array-bracket-newline
      plugins: [
        // 按需导入
        ['component', {
          libraryName: 'vant',
          libraryDirectory: 'es',
          style: false
        }, 'vant'],
        // nuxt-property-decorator
        ['@babel/plugin-transform-runtime'],
        // nuxt-property-decorator
        ['@babel/plugin-proposal-decorators', { legacy: true }],
        //
        ['@babel/plugin-proposal-class-properties', { loose: true }]

      // eslint-disable-next-line array-bracket-newline
      ]
    },
    postcss: [require('postcss-pxtorem')({
      rootValue: 37.5, // 设计稿是750px
      propList: ['*'],
      selectorBlackList: [] // 排除转换van
    })],
    publicPath: '/dist/client/'

  }
}
module.exports = config

```

# 部署

## Nginx

> 假设域名为a.com,该域名映射到pc端网站（vue的spa）。a.com/m映射到服务端（nuxt启动的服务，移动端网站）

- nginx配置如下

```bash
upstream gateway_svr {
    server sznode1:16800;
}


upstream h5_nuxt_ssr {
    #h5移动端渲染
    server 127.0.0.1:16700;
    keepalive 64;
}


map $sent_http_content_type $expires {
    "text/html"                 epoch;
    "text/html; charset=utf-8"  epoch;
    default                     off;
}

server {
    listen       80;
    server_name   linker.sz.datastory.com.cn;

   client_max_body_size 10M;

    # 0代表是pc端，1是移动端微信
   set $wechatMobile_request  0; 

   if ($http_user_agent ~* MicroMessenger){
            set $wechatMobile_request 1;
    }

    # pc端静态资源
    location ~* ^/static/.* {
        if ($request_uri ~ "[&\?]max_age=([0-9]+)") {
            add_header "Cache-Control" "max-age=$1";
        }
        root /home/linker-frontend/dist;
    }
    # h5移动端
    location ~* ^/dist/.* {
        root /home/linker-h5-frontend-root/nuxt-dist;
    }
    

    

    location  /mock/ {
        root html;
        proxy_connect_timeout 300s;
        proxy_send_timeout   600s;
        proxy_read_timeout   600s;
        send_timeout 600s;
        index  index.html index.htm index.php  index.jsp index.do default.do default.jsp default.html default.php;
        proxy_next_upstream error http_503 http_504;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1; 
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_pass         https://easy-mock.com/mock/5cc7f9cba978170c5029aaeb/linker/;
    }

    location  /api/ {
        root html;
        proxy_connect_timeout 300s;
        proxy_send_timeout   600s;
        proxy_read_timeout   600s;
        send_timeout 600s;
        index  index.html index.htm index.php  index.jsp index.do default.do default.jsp default.html default.php;
        proxy_next_upstream error http_503 http_504;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_http_version 1.1; 
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header Host $host;
        proxy_pass	http://gateway_svr/;
    }
   
    location /m/ {
        expires $expires;

        proxy_redirect                      off;
        proxy_set_header Host               $host;
        proxy_set_header X-Real-IP          $remote_addr;
        proxy_set_header X-Forwarded-For    $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto  $scheme;
        proxy_read_timeout          1m;
        proxy_connect_timeout       1m;
        proxy_pass                          http://h5_nuxt_ssr; 
    }
    
    

    location / {
        root /home/linker-frontend/dist;
        index index.html index.htm index.php index.jsp index.do default.do   default.jsp default.html default.php;
        try_files $uri $uri/ /index.html;
        
    }

}

```
- 同时nuxt配置router的base为`/m`

## 守护进程

pm2可使进程常驻

```js
module.exports = {
  apps: [{
    name: 'h5-nuxt-ssr',
    cwd: './',//当前工作路径
    script: 'npm',// 实际启动脚本
    args: ['start', '--max-memory-restart 500M'],/参数
    autorestart: true,
    watch: ['nuxt-dist', 'client', 'server', 'nuxt.config.ts'],// 监控变化的目录，一旦变化，自动重启
    watch_delay: 1000,
    ignore_watch: ['node_modules'],// 从监控目录中排除
    watch_options: {
      followSymlinks: false,
      usePolling: true
    },
    error_file: './logs/app-err.log',//错误日志
    out_file: './logs/app-out.log'//输出日志
  }]
}

```
最好配置脚本在package.json里

- "pm2-start": "yarn build && pm2 start pm2.config.js"
- "pm2-delete": "pm2 delete h5-nuxt-ssr",
- "pm2-monit": "pm2 monit h5-nuxt-ssr"

### 开机自动启动

> 我们希望直接通过服务器重启之后能自动启动

- 通过pm2 save保存当前进程状态。
- pm2 startup