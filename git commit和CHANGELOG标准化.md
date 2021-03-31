#  git commit和CHANGELOG的版本发布标准化

> 代码规范提交可以很好的保存代码修改日志，规范提交日志对于定位问题或代码回退具有极大意义。
>

[conventional-changelog](https://github.com/conventional-changelog)是一款可以根据项目的`commit message`的元信息自动生成 `changelogs` 和 `release notes`的系列工具，本文将讲解`commitlint`、`commitizen`、`conventional-changelog-cli`和`standard-version`工具的基础用法，在讲解工具的具体使用方法前，首先引出`commit message`的规范。

## Commit message的正确格式

commit message的格式如下，包括了Header、Body、Footer：

`<type>(<scope>): <subject>// 空一行<body>// 空一行<footer>`

其中`body`和`footer`可以省略

#### Header

Header部分只有一行，包括三个字段：type（必需）、scope（可选）和subject（必需）。

##### type(类型)

- feat: 新功能
- fix: 修复bug
- docs: 文档变动
- style: 代码格式更新，不影响功能
- refactor: 重构
- test: 测试文件更新
- chore: 非src文件夹的变动
- revert: commit回退
- build: 构建系统或包依赖更新
- ci: 脚本文件更新

##### scope(作用域)

scope用于说明 commit 影响的范围，比如数据层、控制层、视图层等等，视项目不同而不同。

##### subject(简短描述)

subject是 commit 目的的简短描述，不超过50个字符。



## commitlint

[commitlint](https://conventional-changelog.github.io/commitlint/)是一款能够约束commit message规范的工具

##### 在安装commitlint

```bash
// 安装@commitlint/config-conventional @commitlint/cli
$ npm install --save-dev @commitlint/config-conventional @commitlint/cli
```

##### 配置规则

新建`.commitlintrc.js`文件，写入以下配置（配置可以根据实际需求修改）：

```javascript
module.exports = {
  extends: [
    "@commitlint/config-conventional"
  ],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'refactor', 'docs', 'chore', 'style', 'revert'
     ]],
    'type-case': [0],
    'type-empty': [0],
    'scope-empty': [0],
    'scope-case': [0],
    'subject-full-stop': [0, 'never'],
    'subject-case': [0, 'never'],
    'header-max-length': [0, 'always', 72]
  }
};
```

文件名可以不一定是`.commitlintrc.js`，也可以为`commitlint.config.js`、`.commitlintrc.json`或`.commitlintrc.yml`

##### 安装husky

```bash
$ npm install --save-dev husky
```

##### 配置`package.json`文件

```json
{
  "husky": {
    "hooks": {
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  }
}
```

##### 测试

当我们提交错误的格式后，会显示如下错误：

![](https://pythonerkk.oss-cn-shenzhen.aliyuncs.com/iShot2021-01-27%2015.43.42.jpg)

## commitizen

[commitizen](https://github.com/commitizen/cz-cli) 是一款标准化 git commit 信息的工具，默认使用Angular规范来标准化commit信息。本文将采用`cz-conventional-changelog`适配器进行标准化，该工具可以用来代替`commitlint`

##### 全局安装

```bash
$ npm install -g commitizen
$ npm install -g cz-conventional-changelog
```

##### 配置`.czrc`文件

全局安装完成后，我们需要在项目根目录下添加 `.czrc` 配置文件，文件内容如下：

```bash
// path 用来指定适配器
{ "path": "cz-conventional-changelog" }
```

##### 配置`package.json`文件

```json
"config": {
    "commitizen": {
      "path": "cz-conventional-changelog"
    }
  }
```

安装完成后，请使用`git cz`命令代替`git commit`进行代码提交，流程如下：

1. 选择commit类型(feat、fix、docs、style、refactor、perf、test、build、ci、chore、revert)
2. 输入影响范围`scope`
3. 填写标题`subject`
4. 填写描述`body`(如果要编写多行 请使用`\n `换行 回车直接结束描述)
5. 填写breakchange 信息，没有直接回车
6. 填写issue相关信息，没有直接回车

![](https://pythonerkk.oss-cn-shenzhen.aliyuncs.com/iShot2021-01-27%2014.25.43.jpg)

![](https://pythonerkk.oss-cn-shenzhen.aliyuncs.com/iShot2021-01-27%2014.28.01.jpg)

选择完对应的commit类型后，输入作用域scope和描述后即可，如上图所示，commit日志最终格式化为：`chore(package.json): 增加git cz配置`

## conventional-changelog-cli

[conventional-changelog-cli](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-cli) 默认推荐的 commit 标准是来自`angular`项目，本文默认使用`angular`的规范。若项目的所有commit日志都遵守某一规范，就能够输出正确的版本变动文件。

conventional-changelog 更多的选项配置可以[点击进入](https://github.com/conventional-changelog/conventional-changelog/tree/master/packages/conventional-changelog-core)

##### 安装

```bash
$ npm install -g conventional-changelog-cli
```

##### 基本使用

```bash
$ conventional-changelog -p angular -i CHANGELOG.md -s
```

以上命令的`-p`指令指定了使用angular的commit message标准，`-i`指定了输出的文件名

**效果**

<img src="https://pythonerkk.oss-cn-shenzhen.aliyuncs.com/iShot2021-01-26%2017.27.16.jpg" alt="image-20210126172555406" style="zoom:50%;" />

## standard-version

[standard-version](https://github.com/conventional-changelog/standard-version) 是一款能够自动更新版本号，git打标签、更新changelog并commit的工具，其流程如下：

1. 根据`package.json`文件中的`version`更新版本号
2. 自动更新`CHANGELOG.md`文件
3. git打标签
4. git commit

##### 全局安装

```bash
$ npm install -g standard-version
```

安装完成后，可以通过`standard-version`命令直接执行，默认会自动根据`package.json`的`version`，主版本，次版本，修订版规则生成版本号，例如版本号为`0.0.1`，那么自动生成`0.0.2`

执行结果如下图所示：

![](https://pythonerkk.oss-cn-shenzhen.aliyuncs.com/iShot2021-01-27%2015.07.29.jpg)

##### 手动指定版本号

除了在`package.json`中配置版本号，还可以手动指定版本

```bash
$ standard-version -r 1.0.0
# 1.0.0
$ standard-version -r 1.0.0-test
# 1.0.0-test
```

