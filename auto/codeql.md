# CodeQL for Java

## 0x00 Introduction
 
>codeql是一门类似SQL的查询语言，通过对项目源码（C/C++、C#、golang、java、JavaScript、typescript、python）进行完整编译，并在此过程中把项目源码文件的所有相关信息（调用关系、语法语义、语法树）存在数据库中，然后编写代码查询该数据库来发现安全漏洞（硬编码/XSS等）。 CodeQL 是开发人员用来自动化安全检查的分析引擎，安全研究人员用来执行变体分析。

在 CodeQL 中，代码被视为数据。安全漏洞、错误和其他错误被建模为可以针对从代码中提取的数据库执行的查询。您可以运行由 GitHub 研究人员和社区贡献者编写的标准 CodeQL 查询，也可以编写自己的查询以用于自定义分析。查找潜在错误的查询直接在源文件中突出显示结果。

CodeQL 分析包括三个步骤：

1. 通过创建 CodeQL 数据库准备代码  
2. 针对数据库运行 CodeQL 查询
3. 解释查询结果


## 0x01 环境准备

- https://github.com/github/codeql
- https://github.com/github/codeql-cli-binaries/releases
- VSCode的CodeQL插件

安装codeql： 

1. 下载CodeQL客户端：
https://github.com/github/codeql-cli-binaries/releases 然后将其添加到环境变量
2. 下载CodeQL语句，稍后需要将这个目录添加到VSCODE的工作区 https://github.com/github/codeql
3. 生成数据库，CodeQL 分析依赖于从代码中提取关系数据，并使用它来构建CodeQL 数据库
```
codeql database create <database> --language=<language-identifier>
```

配置vscode插件：
在“拓展:商店”中找到CodeQL点击右下角设置图标，选择拓展设置，修改Executeable Path为下载的codeql程序路径。

## 0x02 教程

- [CodeQL文档](https://docs.github.com/en/code-security/codeql-cli)
- [深入理解CodeQL](https://github.com/ASTTeam/CodeQL)
- [CodeQL Java 全网最全的中文学习资料](https://github.com/SummerSec/learning-codeql)
-[CodeQL query help for Java and Kotlin](https://codeql.github.com/codeql-query-help/java/)


## 0x03 案例

- [CodeQL分析XXL-JOB默认accessToken命令执行漏洞](https://mp.weixin.qq.com/s/AMqymFxASgKCUYZQAB8Nzw)

https://github.com/xuxueli/xxl-job/blob/72963e4716a74eacdcbdd2e999c433debf3afaa3/xxl-job-admin/src/main/resources/application.properties#L55

## Ref
- https://pipeline8.com/?p=146