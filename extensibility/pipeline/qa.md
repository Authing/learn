# 常见问题

## Pipeline 函数支持  async/await 语法吗？

支持。

## Pipeline 函数中可以使用 Authing SDK 吗？

可以，且无需导入和初始化。详情请见[可用的  Node Modules](available-node-modules.md)。

## 我能用其他语言编写 Pipeline 函数吗？

暂时不能，当前仅支持 Node 语言。

## 编写 Pipeline 函数有哪些注意事项？

* 请不要重命名 pipe 函数。
* 推荐不要硬编码，使用[环境变量](env.md)来存放常量值。

## 刷新用户池  secret 对 Pipeline 函数有何影响？

由于 Authing Pipeline 函数完全运行在云端，所以刷新用户池 secret 会同时更新用户池内所有的 Pipeline 函数。这意味着在一小段时间之内 Pipeline 函数中将无法正常使用 authing-js-sdk。

