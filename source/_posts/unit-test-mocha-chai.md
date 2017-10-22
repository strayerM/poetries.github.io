---
title: Mocha + Chai + Istanbul单元测试
date: 2017-10-22 15:35:43
tags: 
  - JavaScript
  - 测试
categories: Front-End
---

一、简介
---

- `Istanbul`是`JavaScript`程序的代码覆盖率工具
- `Mocha`是一种测试框架，也就是运行测试的工具。用`descibe`和`it`方法，来定义`test suit`，为不同的测试分组。
- `mocha`本身并不提供`assert`断言，所以要提供更加有表现力的断言，可以搭配`chai`使用，当然也可以使用`nodejs`提供的`assert`模块

二、配置测试环境
---

**安装相应的依赖包**

```javascript
npm install --save-dev mocha chai  istanbul
```

- 安装完成之后，在`package.json`文件的`scripts`下，添加执行测试和测试覆盖率检查的命令

```javascript
{
  
  "scripts":{
    "cover": "istanbul cover _mocha -- -R spec --timeout 5000 --recursive",
    "cover:check": "istanbul check-coverage",
  }
  
}
```
- 注意，`window`下必须要这样才可以执行`cover`

```javascript
"cover": "istanbul cover C:\Users\Administrator\Desktop\test\node_modules\mocha\bin\_mocha --reporter test/mocha.js"
```


- 运行`npm run cover`和`npm run cover:check`，就可以生成测试报告，前者生成测试报告，后者则是检查测试覆盖率是否达到要求

![](https://pic2.zhimg.com/v2-6ca2e32f24fdf4a0e37eeda89ae6a7d9_b.png)


**配置Istanbul**

> `istanbul`相关的执行参数，可以在命令行下执行时传递参数来制定，也可以在`yaml`格式的`.istanbul.yml`文件中配置。简单贴出一些重要的配置项

```javascript
instrumentation:
  root: .   # 执行的根目录
  extensions:
    - .js   # 检查覆盖率的文件扩张名
  excludes: ['**/benchmark/**']

  ... ...

reporting:
  print: summary
  reports: [lcov, text, html, text-summary] # 生成报告的格式
  dir: ./coverage   # 生成报告保存的目录
  watermarks:       # 在不同覆盖率下会显示使用不同颜色
    statements: [80, 95]
    ... ...
check:
  global:
    statements: 100
    branches: 100
    lines: 100
    functions: 100
```

> 最后的`check`是项目要通过覆盖率检查需要达到的测试覆盖率，测试覆盖率包括四个维度，分别是语句覆盖率、分支覆盖率、行覆盖率和函数覆盖率。如果达不到设定的指标，在执行的时候会报错，项目的测试就无法通过自动化的持续集成

三、编写测试代码
---

> 利用`chai`提供的`expect`断言，我们可以用`BDD`的方式，写出更加符合代码预期行为的测试用例

```javascript
const {should, expect, assert} = require('chai');

describe('#math', () => {
  describe('add', () => {
    it('should return 5 when 2 + 3', () => {
      expect(add(2, 3), 5);
    });

    it('should return 5 when 2 + 3', () => {
      expect(add(2, -3), -1);
    });
  });

  describe('mul', () => {
    it('should return 6 when 2 * 3', () => {
      expect(mul(2, 3), 6);
    });
  });

  describe('cover', () => {
    it('should return 1 when cover(2, 1)', () => {
      expect(cover(2, 1)).to.equal(1);
    });

    it('should return 3 when cover(1, 2)', () => {
      expect(cover(1, 2)).to.equal(3);
    });

    it('should return 4 when cover(2, 2)', () => {
      expect(cover(2, 2)).to.equal(4);
    });
  });
});
```

四、持续集成
---

> 完成所有代码之后，我们可以将代码发布到`github`，然后使用持续集成工具`travis`检查代码，将生成的测试报告上传到`coverall`上，这样就可以在项目中显示项目状态和测试覆盖率的badges

- 持续集成是一种软件开发流程
- 频繁将代码集成到主干
- 每次集成都通过自动化的构建来验证
- 尽早发现错误
- 防止防止大幅偏离主干

- 通常的`nodejs`项目`.travis.yml`配置如下

```javascript
language: node_js
node_js:
  - "6"
  - "8"
brancher:
  only:
    - "dev"
    - "master"
install:
  - "npm install"
  - "npm install -g codecov"
script:
  - "npm run cover"
  - "codecov"

```

五、小结

- 测试代码预览 https://github.com/poetries/test
