# 从 rails 窥探 web 全栈开发（零）

本文将讲述在学习之前几个必须要知道的概念，这些词汇在 rails 中都会出现。

本文前置条件：安装好 Ruby。

- [从 rails 窥探 web 全栈开发（零）](#从-rails-窥探-web-全栈开发零)
  - [先换源](#先换源)
  - [Ruby](#ruby)
  - [RVM](#rvm)
  - [Rails](#rails)
  - [RubyGems](#rubygems)
  - [Gem](#gem)
  - [Gemfile](#gemfile)
  - [Bundle](#bundle)
  - [Rake](#rake)
  - [Rakefile](#rakefile)

## 先换源

```bash
gem sources -l
gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/
bundle config mirror.https://rubygems.org https://gems.ruby-china.com
```

使用 `--add` 新增源，然后用第一条命令查看有哪些已有源，用 `--remove` 把除了刚刚添加的国内源，其他的都删掉。

## Ruby

不必多说，一门「编程语言」。

## RVM

一个「程序」。

RVM 帮你管理多个版本的 Ruby，让你能随时切换 Ruby 的环境。

_（可以要，可以不要，并不是必须的）_

## Rails

一个 Ruby 的 web 「框架」。

## RubyGems

一个「程序」。

一般说的 gem 就是指它，是 Ruby 的包管理工具。

使用 `gem install rails` 在电脑上就可以安装 rails。

如果不理解什么是包管理器，可以看这篇[文章](https://www.cnblogs.com/xhyccc/p/13260541.html)。

## Gem

指封装起来的一个 Ruby 「应用或代码库」。

## Gemfile

一个「文件」。

其中定义你的应用依赖哪些第三方包，bundle 会根据该配置去寻找这些包。

## Bundle

一个「程序」。

帮你批处理运行 gem，在配置文件 Gemfile 里说明你的应用依赖哪些第三方包，然后运行 `bundle install` bundle 就会自动帮你下载安装多个包——并同时会下载这些包依赖的包。

## Rake

Rake 是一门构建「语言」。

和 make 类似，但 Rake 是用 Ruby 写的，它支持自己的 DSL 用来处理和维护 Ruby 程序。

例如 Rails 可以用 rake 扩展来完成多种任务，如数据库初始化、更新等等。

## Rakefile

一个「文件」。

Rakefile 中写的是 Ruby 代码，Rake 的命令执行就是由 Rakefile 文件定义的。

（未完待续）
