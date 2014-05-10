title: Grunt+Livereload 搭建本地前端开发环境
date: 2014-05-07
categories:
  - 前端工程化
tags:
  - grunt
  - javascript
---

## 要解决什么问题?
- 前端开发时，经常需要把静态文件映射成web服务，传统的做法是丢到apache，但太重太不友好了。
- 开发`angular`的时候，官方的chrome插件对`file:///`的支持不好，所以最好在web浏览器里面。
- 然后还有livereload -- 节省你的F5
<!-- more -->

## 主要思路
- 用nodejs+connectjs搭建静态web服务
- 用`grunt`来做脚本
- 用`livereload`来通知文件变更（不需要chrome livereload插件）
- yeoman angular自带的grunt脚本太旧太重了，所以自己写个。

再具体点：
1. `grunt-contrib-connect`负责启动web服务
2. `connect-livereload`负责给middleware，动态在html底部加一条livereload的js
3. `grunt-contrib-watch`监控文件变化并通知

## 具体步骤
### 安装依赖
1. 安装`nodejs`： http://nodejs.org
2. 安装`gruntjs` (http://gruntjs.com/) ：`npm install -g grunt-cli`
3. 初始化package.json： 在项目根目录下，命令行执行`npm init`，一路回车。
4. 安装依赖模块： 在项目根目录下，命令行执行`npm install --save-dev grunt matchdep grunt-contrib-connect grunt-contrib-watch connect-livereload grunt-open`

### 在根目录放置Gruntfile.js 
1. 注意首字母大写
2. 修改`src:'src/app/'`为你的源码目录
3. 若端口冲突则修改port

```
/**
 * 自动化脚本定义
 */
module.exports = function (grunt) {
  'use strict';

  //load all grunt tasks
  require('matchdep').filterDev('grunt-*').forEach(grunt.loadNpmTasks);

  //define tasks
  grunt.registerTask('server', ['connect:server', 'open:server', 'watch:server']);

  //env cfg
  var pkg = grunt.file.readJSON('package.json');
  var cfg = {
    src: 'src/app/',
    // Change 'localhost' to '0.0.0.0' to access the server from outside.
    serverHost: '0.0.0.0',
    serverPort: 9000,
    livereload: 35729
  };  

  //grunt config
  grunt.initConfig({
    //======== 配置相关 ========
    pkg: pkg,
    cfg: cfg,

    //======== 开发相关 ========
   //开启服务
    connect: {
      options: {
        port: cfg.serverPort,
        hostname: cfg.serverHost,
        middleware: function(connect, options) {
          return [
            require('connect-livereload')({
              port: cfg.livereload
            }),
            // Serve static files.
            connect.static(options.base),
            // Make empty directories browsable.
            // connect.directory(options.base),
          ];
        }
      },
      server: {
        options: {
          // keepalive: true,
          base: cfg.src,
        }
      }
    },
    
    //打开浏览器
    open: {
      server: {
        url: 'http://localhost:' + cfg.serverPort
      }
    },

    //监控文件变化
    watch: {
      options: {
        livereload: cfg.livereload,
      },
      server: {
        files: [cfg.src + '/**'],
        // tasks: [''],
      },
    }
  });
};
```

### 开始开发吧~
- 项目根目录下执行 `grunt server`
- 将会自动弹出浏览器，访问 localhost:9000 ，如果你端口冲突，在Gruntfile.js里面修改。
- 试着修改你的源码，保存，然后浏览器就自动刷新了。（别习惯性的按F5哈）
- 注意：访问的文件必须是HTML，并且有body标签，否则不会插入livereload.js