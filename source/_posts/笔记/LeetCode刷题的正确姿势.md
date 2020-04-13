---
title: LeetCode 刷题的正确姿势
date: 2019-04-15
tags: [算法, 工具]
categories: 笔记
---

### 关于 LeetCode

[LeetCode](https://leetcode.com/problemset/all/)是美国一个在线编程网站，也是一个在线判题\(Online Judge\)平台。拥有很好的在线编程体验，大量的算法题库以及活跃的答案讨论区。

[LeetCode](https://leetcode.com/problemset/all/)上的题目分为三个难度，除了 Hard 模式外大多比较基础，基本上不考察复杂的算法，大多是对基础知识应用，因此是程序员准备面试，入门和练习算法的不二之选，另外它在去年推出了[中文版](https://leetcode-cn.com/problemset/all/)。

关于程序员练习算法的好处我就不赘述了，可以看看这篇文章： [LeetCode 编程训练](https://coolshell.cn/articles/12052.html)

### 如何更好地刷题？

各大 OJ 平台都会提供相应的在线编辑器和测试用例。用户可以即时做题，运行代码，得到结果。不过我认为对于长期使用者来说，应该使做算法的过程**本地化**，这么做有如下这些好处：

- 可以使用自己平时开发写代码的编辑器/IDE，更加习惯。
- 有语法提示和自动格式化，写代码更高效更规范。
- 本地断点调试很方便。
- 使用本地测试用例，可以针对性测试某个用例，同时避免在 OJ 平台的多次错误提交，降低你的 AC 成功率。
- 通过测试工具，对比不同解法的性能。
- 支持离线做题，可以将记录同步到 Git 进行版本管理。

因此我写了一个本地化 LeetCode 刷题的库：[https://github.com/shudery/leetCode](https://github.com/shudery/leetCode)，来获得上面的这些优势，让我可以更高效更方便地刷题，同时也记录了我的解题过程，我使用的编程语言为 JavaScript。

### 开始使用

将项目克隆到本地：

```text
git clone git@github.com:shudery/leetCode.git
```

你需要安装最新的 Nodejs 稳定版本，然后下载 npm 包依赖：

```javascript
npm install
```

PS：克隆下来的项目 problems 目录下有我的做题记录，可以重命名作为参考，也可以直接删除，记录你自己的解题记录

### 新建算法题目

通过一个简单的 shell 脚本，自动生成新题目的文件目录，根据模板生成相应文件，一个用来写算法的 **index.js** 和一个用来填写测试输入和输出的 **test.js**，并在 **README.md** 中插入一条相关的信息。

```text
# ./crepro.sh {题目序号}-{题目(-分隔单词)} {题目难度}
./crepro.sh 413-arithmetic-slices Medium
```

PS：目前仅在 **index.js** 插入一些基本信息，还需要手动 copy 一下题目内容和测试用例到本地，并导出函数，后续考虑通过自动化爬虫抓取。

### 测试算法题目

使用测试框架 **mocha** 和断言库 **chai** 来验证算法是否通过所有测试用例，用 **benchmark** 来测试算法运行耗时。

测试相应题目很简单，只需要带上题目号，在命令行中执行：

```javascript
// 测试题号001，传入n=001，返回测试结果
n=001 npm test
// 返回测试结果，包括算法性能
n=001 npm run perf
```

![测试结果](/images/leetcode.png)

思路很简单，将目录结构下的 **index.js** 中的函数取出，用 **test.js** 中的输入数组作为参数传入执行，与输出结果做对比，以此判断算法是否通过：

```javascript
const path = './problems';
const problems = fs.readdirSync(path);
const num = process.env.n;

if (num) {
  // 测试指定题目
  const reg = new RegExp(num);
  const problem = problems.find(val => reg.test(val));
  runTest(problem);
} else {
  // 测试所有题目
  problems.forEach(problem => runTest(problem));
}

/**
/**
 * 启动测试用例
 * @param {string} problem 题目目录
 */
function runTest(problem) {
  let fns = require('./problems/' + problem + '/index.js');
  const testCases = require('./problems/' + problem + '/test.js');
  //将单个函数转为数组格式
  if (Object.prototype.toString.call(fns) !== '[object Array]') {
    fns = [fns];
  }
  // 开始测试
  describe(`test-problem: ${problem}`, function() {
    // 将每个题目中导出的函数逐个执行测试用例
    fns.forEach(fn => {
      // 将多个测试用例逐个执行
      testCases.forEach((testCase, testIndex) => {
        // 如果该题目不需要测试则在module.exports =null即可
        fn &&
          it(`function:${fn.name}  testCase-${testIndex}`, function() {
            // 不能直接传入原始数组，不然testCase中的input被污染，影响下一个执行函数
            const arr = _.clone(testCase);
            expect(fn.apply(null, arr.input)).to.deep.equal(arr.output);
          });
      });
    });
  });
}
```

除了验证算法是否通过外，运行算法的耗时也很重要，在一些涉及到大量数据运算的题目中经常遇到算法正确性可以通过，但是由于超出运算时间而无法通过的情况，比如我在做[LRU-Cache](https://leetcode-cn.com/problems/lru-cache/) 这道题目的时候，就因为使用了 **forEach** 这个方法而导致超时错误，后面经过测试对比算法的运算耗时，发现 **forEach** 在数据量很大时，性能远远不如 for 循环，改用 for 循环后该问题得到解决。

性能测试使用 **benchmark** 库，由于其 API 为链式调用，直接通过 eval 拼接字符串的方式来执行，每个算法的导出为一个数组，数组中可以有多个解法函数，测试多个函数：

```javascript
const fns = require('./problems/' + problem + '/index.js');
const testCases = require('./problems/' + problem + '/test.js');
perfTest(problem, _.clone(testCases), fns);
/**
 * 性能测试函数
 * @param {*} problem 测试的题目目录
 * @param {*} testCases 测试用例
 * @param {*} fns 测试的函数
 */
function perfTest(problem, testCases, fns) {
  logger(('<--start fns speed test-->' + problem).underline + '\n');
  //非数组报错
  isArray(problem, fns);
  // 运算耗时排名
  const timeRank = [];
  // 执行字符串
  const evalStr = fns.reduce(
    (pre, cur, i) =>
      pre +
      `.add("${
        cur.name
      }", function() {fns[${i}].apply(null,testCases[0].input);})`,
    'suite'
  );
  eval(evalStr)
    .on('cycle', event => handleCycle(event, timeRank))
    .on('complete', function() {
      // 对运行时间排序
      sort(timeRank);
      const label = [];
      timeRank.forEach(result => {
        label.push(`${result.name} >>> ${result.time}`);
      });
      logger(this);
      logger(`Allrank :\n${label.join('\n')}`.blue);
      logger(`Fastest :\n${this.filter('fastest').map('name')}`.green);
      logger(`Slowest :\n${this.filter('slowest').map('name')}`.red);
    })
    // 这个选项与时间计算有关
    .run({ async: true });
}

function handleCycle(event, timeRank) {
  const info = String(event.target);
  logger(info.yellow);
  timeRank.push({
    name: info.split(' x ')[0],
    time: info.split(' x ')[1].split(' ops/sec')[0]
  });
}
```

不带题目号则测试所有题目：

```c
npm test
// 测试算法是否通过，并计算所有算法的性能，比较耗时
npm run perf
```

### 调试算法题目

在本地做题还有一个巨大的优势就是可以做断点调试。在 **VSCode** 之类的编辑器中打上断点，或者在代码中写 **debugger**，注意会执行一遍所有的算法测试用例，所以不要跨题目打多余的断点。执行如下命令：

```javascript
npm run debug
```

PS：注意通常 OJ 平台上的一些数据类型转化都是封装好，对用户不可见的，本地化时需要实现这些数据结构的转化算法。目前已经实现 **数组 --&gt; 链表** 的转换函数 **mapLinks**， **数组 --&gt; 二叉树** 的转换函数 **mapTree**，在需要做转换的测试用例中，将输入参数传入转换函数即可。

### TODO

- 算法题目内容的自动获取
- 算法空间性能的计算
- 支持函数调用式的测试用例格式
- 目前仅支持 JavaScript 的解法，可以兼容支持其他语言
