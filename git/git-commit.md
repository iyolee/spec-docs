# Git commit 规范

## 好处

规范的 Git commit message 带来的好处有：
- 使每次的提交的类型，原因都一目了然，易于后继维护者更快速接手项目
- 根据 Git Commit 的元数据生成 Changelog
- 加快 Code Review 的流程

## 技术方案
### Git commit 规范
目前规范使用较多的是 [Angular 团队的规范](https://github.com/angular/angular.js/blob/master/DEVELOPERS.md#-git-commit-guidelines)，它的格式如下：
``` sh
<type>(<scope>): <subject>
<BLANK LINE>
<body>
<BLANK LINE>
<footer>
```

大致分为三个部分：
- 标题行: 必填, 描述主要修改类型和内容
- 主题内容: 描述为什么修改, 做了什么样的修改, 以及开发的思路等
- 页脚注释: 放 Breaking Changes 或 Closed Issues

### 可落实的 Git commit 规范
Angular 团队的规范让每次提交都像在发一封邮件，如果是业务项目，提交可能会很频繁，过于繁琐的提交限制显然不合适，结合Angular 团队的规范，折中出一套可用于团队项目的规范。

#### commit message 格式
```
<type>(<scope>): <subject>
```
1. type — 提交 commit 的类型
    - feat: 新功能
    - fix: 修复问题
    - docs: 修改文档
    - style: 修改代码格式，不影响代码逻辑
    - refactor: 重构代码，理论上不影响现有功能
    - perf: 提升性能
    - test: 增加修改测试用例
    - chore: 修改工具相关（包括但不限于文档、代码生成等）
    - deps: 升级依赖
    - revert: 回滚到上一个版本
2. scope — 修改文件的范围，可选，包括但不限于 doc / plugins 等
3. subject — 用一句话清楚的描述这次提交做了什么

#### 规范工具的接入
规范工具的接入带来了提交机制的保障。若提交不符合要求格式，应该拒绝本次提交，并给出相应的提醒。

1. 引入对应的 npm 模块
```
npm i husky validate-commit-msg --save-dev
```
2. 配置 npm script
```
{
  "husky": {
    "hooks": {
      "commit-msg": "validate-commit-msg"
    }
  }
}
```
3. 使用：提交commit 时候触发检测
```
# 错误commit message提交
git commit -m "xxxxx"
# 错误commit message叫册
INVALID COMMIT MSG: does not match "<type>(<scope>): <subject>" !
xxxxx
```

#### 配置校验规则
可在`.vcmrc`文件中灵活配置规则。
```
  
{
  "types": ["feat", "fix", "docs", "style", "refactor", "perf", "test", "chore", "deps"],
  "scope": {
    "required": false,
    "allowed": ["*"],
    "validate": false,
    "multiple": false
  },
  "warnOnFail": false,
  "maxSubjectLength": 100,
  "subjectPattern": ".+",
  "subjectPatternErrorMsg": "subject does not match subject pattern!",
  "helpMessage": "",
  "autoFix": false
}
```

#### 自动生成 changelog 文档
接入自动生成 changelog 文档的工具`conventional-changelog-cli` :
``` sh
npm install -g conventional-changelog-cli
```

生成 CHANGELOG.md
``` sh
cd my-project

conventional-changelog -p angular -i CHANGELOG.md -s
```

或者配置 npm script :
```
"scripts": {
  "changelog": "rm -r CHANGELOG.md && conventional-changelog -p angular -i CHANGELOG.md -s"
}
```

按照上面配置，就可以生成一份`CHANGELOG.md`，具体可参考[Angular 的 CHANGELOG.md](https://github.com/angular/angular/blob/master/CHANGELOG.md)。