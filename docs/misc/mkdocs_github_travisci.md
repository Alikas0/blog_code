
## 说明
> GitHub Pages

[Github Pages](https://pages.github.com/)是一个免费的静态站点，特点:免费托管、自带主题、支持自制页面和Jekyll

> MkDocs

[MkDocs](http://markdown-docs-zh.readthedocs.io/zh_CN/latest/)是一个用于创建项目文档的 **快速, 简单 , 完美华丽** 的静态站点生成器. 文档源码使用 Markdown 来撰写, 用一个 YAML 文件作为配置文档

中文文档:https://markdown-docs-zh.readthedocs.io/zh_CN/latest/

>Travis CI

[Travis CI](https://travis-ci.org/)，是一个专门为开源项目打造的持续集成环境。

如果你有一个放在github上的开源项目，Travis CI简直就是一个完美的CI选择。

## MkDocs

### 安装

需要 Python 和 Python package manager pip 来安装 MkDocs . 可以通过以下命令查看是否安装了上述依赖:

```text
$ python --version
Python 3.7.3
$ pip --version
pip 19.0.3 from D:\MkDocs\venv\lib\site-packages\pip-19.0.3-py3.7.egg\pip (python 3.7)
```
使用pip安装`mkdocs`:

```text
$ pip install mkdocs
```
`mkdocs`已经安装到你的系统. 运行 `mkdocs help` 以检查是否正确安装.

```text
$ mkdocs -h
Usage: mkdocs [OPTIONS] COMMAND [ARGS]...
```

### 开始

输入以下命令以开始一个新项目

```text
$ mkdocs new my-project
$ cd my-project
```