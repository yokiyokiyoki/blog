---
title: node的基本调试
date: 2019-08-22 17:08:26
tags: node
---

# vscode调试

## 断点

无须多言

## 模式

> 两种模式

- 一种是通过node的launch，也就是直接启动项目的入口。这种方式最为直接，但是实际上并不是很方便，因为每一次调试都需要重新启动项目。
- 另一种是通过attach的方式，使用node-inspector进行调试。

```json
{
  "version": "0.2.0",
  "configurations": [
    {
      "type": "node",
      "request": "launch",
      "name": "Launch Program",
      "program": "${workspaceFolder}/build/app.js",
      "sourceMaps": true,
      "outFiles": [ // 该选项指定了map文件的寻找路径
        "build"
      ]
    },
    {
      "type": "node",
      "request": "attach",
      "name": "附加",
      "port": 5858,
      "timeout": 10000,
      "restart": true,
      "address": "localhost",
      "sourceMaps": true,
      "outFiles": [
        "build"
      ]
    }
  ]
}
```
## 参考资料

[NodeJS的代码调试和性能调优](https://www.barretlee.com/blog/2015/10/07/debug-nodejs-in-command-line/)