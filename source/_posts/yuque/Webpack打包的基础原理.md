
---
title: Webpack打包的基础原理
date: 2019-01-10 10:37:32 +0800
tags: []
categories: 
---
最近想学习一下webpack打包的基本原理，从官方文档找到了一个推荐链接：[简单模块打包工具的细节说明](https://github.com/ronami/minipack)

是一个精简的打包工具示例：minipack，里面的源码解释非常详细，介绍了一个打包工具库主要做的工作。我快速地阅读了一下源码，并做了一下翻译和总结。

简单来说打包就是从一个入口文件开始，根据模块引入的语法（例如ES6的import，commonJS的require）找到所有的依赖模块，然后通过函数作用域隔离开并打包成一个单一文件。

当然webpack也支持多入口打包，已经输出多个chunks打包文件，这里只是实现最简单的打包器功能，只需分三步。

### 1. 找出单个模块的依赖文件

在minipack中，没有选择直接对文件内容进行字符串解析，而是使用了babylon这个包来做语法解析。得到的抽象语法树AST，处理文件中的ImportDeclaration语句，即可得到文件的依赖信息，

处理后同时给该文件打上标记id，每个文件模块经过处理之后会返回一个如下结构的对象：

```javascript
// Return all information about this module.
  return {
    id, // 模块标识
    filename, // 模块文件的地址
    dependencies, // 模块的依赖文件列表
    code // 模块的代码内容
  };
```

### 2. 获取全部模块的依赖关系

接下来需要将所有文件的依赖关系联系起来，输入一个入口模块的地址，已该模块作为起点，循环搜索各个文件的依赖，直到依赖链的底层，直接贴上我对源码的注解：

```javascript
// 获取全部模块的依赖关系
function createGraph(entry) {
  // 结构化模块信息
  const mainAsset = createAsset(entry);

  // 存储模块信息到队列
  const queue = [mainAsset];

  // 使用迭代器来处理模块队列，当队列为空循环终止
  for (const asset of queue) {

    // 保存依赖关系
    asset.mapping = {};
    const dirname = path.dirname(asset.filename);
    // 处理依赖子模块
    asset.dependencies.forEach(relativePath => {
      // 相对路径转为绝对路径
      const absolutePath = path.join(dirname, relativePath);

      // 获取依赖子模块的信息
      const child = createAsset(absolutePath);

      // 将依赖关系通过path:id的形式保存
      asset.mapping[relativePath] = child.id;

      // 存在依赖的子模块，即asset.dependencies不为空时，子模块入队列继续迭代
      queue.push(child);
    });
  }
  return queue;
}
```

### 3. 整合为单个文件

有了所有模块的依赖关系后，我们就可以将它们整合在一份文件中，通过js的function作用域去将各个模块隔离开来，仅暴露module.exports出来。

将依赖信息构造成一个以id的索引的数组，数组包括两个元素：一个是以require函数，module和exports对象为入参的函数，一个是子依赖项的信息。重点在于实现require方法。

在require方法中，传入模块id，根据模块id获取隔离作用域的执行函数fn以及依赖信息mapping，预处理一下require函数，目的是将模块中require函数入参的相对地址转为id，然后传入执行fn，最后返回module.exports对象给上级调用。

```javascript
function bundle(graph) {
  let modules = '';
  // 将依赖图信息列表转为一个字符串
  graph.forEach(mod => {
    modules += `${mod.id}: [
      function (require, module, exports) {
        ${mod.code}
      },
      ${JSON.stringify(mod.mapping)},
    ],`;
  });

  // 实现一个自执行函数，注入modules信息
  const result = `
    (function(modules) {
      function require(id) {
        const [fn, mapping] = modules[id];

        function localRequire(name) {
          return require(mapping[name]);
        }

        const module = { exports : {} };

        fn(localRequire, module, module.exports);

        return module.exports;
      }

      require(0);
    })({${modules}})
  `;
  // 返回单个文件输出
  return result;
}
```

这个方法显然是有一些瑕疵的，比如所有的模块执行require时都会执行一遍require模块的代码，可能会导致重复引入。不过通过对require方法的简单实现，已经足够让我们理解webpack这类打包工具的本质：就是通过函数来划分作用域，通过module以及module.exports来共享数据。


