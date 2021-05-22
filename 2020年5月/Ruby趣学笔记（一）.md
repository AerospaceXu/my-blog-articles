# Ruby 趣学笔记（一）

本文写于 2020 年 5 月 6 日

- [Ruby 趣学笔记（一）](#ruby-趣学笔记一)
  - [变量](#变量)
    - [变量声明](#变量声明)
    - [变量类型](#变量类型)
  - [常量](#常量)
  - [输出](#输出)
  - [字符串](#字符串)
  - [字符串操作](#字符串操作)
  - [Array](#array)
    - [数组的遍历](#数组的遍历)
    - [数组的连接](#数组的连接)
    - [怎么判断该变量是否是数组](#怎么判断该变量是否是数组)
  - [函数](#函数)
    - [普通函数](#普通函数)
    - [传参的函数](#传参的函数)
    - [解包参数](#解包参数)
    - [部分参数解包](#部分参数解包)
    - [参数的默认值](#参数的默认值)
    - [传入一个散列](#传入一个散列)
  - [class](#class)
    - [class 下面有啥方法？](#class-下面有啥方法)
    - [如何判断这个方法是否存在呢？](#如何判断这个方法是否存在呢)

最近在 mac 上探索到了 homebrew 的使用方法，对 ruby 的兴趣直线上升，所以来学一学。

最近几年确实大家一直在唱衰 Ruby，整个社区的生态确实也不如 python 那么庞大，但是这都不妨碍 ruby 被称作“快乐编程”。

这几年越来越强调语言的性能，但是 Ruby 的作者松本行弘却认为，**人才是最重要的！**

自看到这句话的第一眼，我就深以为同！

其实这就是一个价值观的问题：人更值钱，那么就应该尽量节省人力；机器算力更值钱，那么就应该用人力去优化程序，来节省机器的算力。_但是国内的大环境大家也懂，人多爆了......_

自然而然，从上面的结论来看，Ruby 在国内不会流行。

作为多范式编程语言，Ruby 会比 Java 等更难协作；作为解释型语言，运行速度肯定比不上 C++ 超级慢……可很多人对于 Ruby 的评价仍然是：程序员的一把尖刀！

并且在 web 开发领域，还有一些大公司（GitHub）坚持在使用 Ruby，这足以说明 Ruby 的强大了！

## 变量

### 变量声明

Ruby 的变量声明不需要关键字，也不需要类型信息。

```rb
message = 'hello world'
_num = 0

$h = [1, 2, 3]
```

小写开头、`_` 开头的变量是局部变量；`$` 开头的单词是全局变量。

并且默认 Ruby 不存在闭包。

在函数内访问上级作用域的局部变量会报错。

### 变量类型

Ruby 不存在所谓的“基本类型”，比如 `int` / `float` / `char`...Ruby 只存在一个类型：对象。

一切皆对象的原则被严格恪守！我们可以不严谨的认为：**Ruby 的类型就是对象。**

什么意思呢？

```ruby
print(1.to_s)
```

`to_s` 天生的就是 `1` 的方法！**数字 `1` 其实就是 Number 对象的实例。**

Ruby 的数据类型有 Number、String、Ranges、Symbols，以及 true、false 和 nil，以及 Array、Hash。

其中比较有意思的是 true 和 false，他们的 class 并不是 `Boolean`，而是 `TrueClass` 和 `FalseClass`。

甚至连 class 都有 class——`Class`。

事实上，**所有变量的 class 的 class，都是 `Class` 类。**

## 常量

如同变量一样，Ruby 的常量声明也是通过约定。

所有首字母大写就代表常量，通常我们习惯将常量全大写（_也有例如 GOLANG 以及一些其他语言推荐使用大驼峰命名_）。

```rb
MAX_COUNT = 100
```

但 Ruby 毕竟还是个动态语言，即使是常量，我们也是可以强行修改的，只是会警告你而已。

```rb
MAX_COUNT = 100
MAX_COUNT = 101

p MAX_COUNT # 101
```

## 输出

Ruby 存在三种输出语句 `print` `puts` `p`。

其中，`p` 语句的输出可以让我们看到更多的信息，例如

```rb
p "hello" # "hello"
puts "hello" # hello
```

`p` 可以让我们看到是否是字符串，`puts` 则不可以。

所以一般程序中输出时会用 `p`，日志输出用 `print` 或 `puts`。

## 字符串

Ruby 可以用单引号或者双引号标注字符串，单引号中不会自动将转义字符转义、双引号会自动转义。

```rb
p "a\nb" # 会换行
p 'a\nb' # 不会换行，直接输出 \n
```

双引号中还可以进行类似 JS 的模板字符串的插值：`"hello #{userName}"`。

Ruby 也支持多行字符串。

```rb
str = <<fuck
1
2
3
3
fuck
```

在 `<<` 后跟上某一个字符串，直到遇见相同的字符串，多行字符串就会结束。

```rb
str = <<fuck
1
2
3
3fuck
```

但这种情况不会结束，所以结束符必须位于单行。

## 字符串操作

- `+`：字符连接
- `<<`：字符带入
- `*`：字符循环

```ruby
a = "hello";
b = "world";
puts a + " " + b;

# <<：字符带入
a << b
puts a
puts b

# 这个其实相当于 a = a + b

# *：字符循环
a = "A"
puts a * 4
```

## Array

```ruby
games = ['魔兽世界', '塞尔达传说', '金庸群侠传']
puts games
```

这个 games 的定义，如果你在 RubyMine 或者其他强大的 IDE 中编写，他就会告诉你，有更好的方式：`games = %w[魔兽世界 塞尔达传说 金庸群侠传]`

**这就是为什么 Ruby 不易协作**，一千个程序员有一千种写法与实现方法！

### 数组的遍历

这是永远的重点，编程中最为常用的方法。

Ruby 可以通过 `each` 来遍历数组，具体语法如下：

```ruby
games.each do |game|
  puts "我喜欢#{game}"
end
```

这个地方稍微提一句，我曾在某个地方看到过，写单引号的程序最后会比写双引号的小（文件大小）。但是这里这种模板字符串的写法，必须要双引号。

**带上数组下标的遍历**

```ruby
games.each_with_index do |game, i|
  puts "第#{i}个游戏是#{game}"
end
```

### 数组的连接

`games.join(',')`，这个方法其实很多语言都有，可以返回一个由逗号连接的、由数组每个元素顺序组成的字符串。

### 怎么判断该变量是否是数组

这个怎么判断呢？？？

很简单，还是运用 `respond_to?`。

怎么用？

刚刚的 `each` 方法就可以！

`games.respond_to?('each') && puts '这是一个数组！'`

## 函数

### 普通函数

```ruby
def sayHello
  puts "Hello World."
end

sayHello() # Hello World.
sayHello # Hello World.
```

这里可以看到，Ruby 的函数既可以带括号的调用，也可以直接调用。

### 传参的函数

```ruby
def sayHello(name)
  puts "Hello, #{name}."
end
```

### 解包参数

函数会自动解包传入的数组。

```rb
def foo(p1, p2, p3)
  p p1 + p2 + p3
end

foo [1, 2, 3]
```

注意，这种将数组解包的函数，如果传入的数组长度大于参数数量，那么会直接报错。

### 部分参数解包

传入的多余参数转化为数组。

```rb
def foo(base, *rest)
  rest.each do |item|
    base += item
  end

  return base
end

foo 1, 2, 3, 4, 5
```

`*rest` 参数可以不在末尾。

```rb
def foo(base, *rest, final)
  p base
  p rest
  p final
end

foo 1, 2, 3, 4, 5 # base 1, rest [2, 3, 4], final 5
foo 1, 2 # base 1, rest [], final 2
foo 1 # 报错
```

### 参数的默认值

```ruby
def sayHello(name = 'world')
  puts "Hello, #{name}."
end

sayHello "啦啦啦"
```

### 传入一个散列

该形式必须提供默认值。

```ruby
def sayHello(url: "", id: 0)
  puts "#{url}?id=#{id}."
end

sayHello url: "xxx", id: 5
```

这就类似于 JS 中的：

```js
function foo({ a, b } = { a: 1, b: 1 }) {}
```

这种形式还可以直接传入一个散列，但是必须保证 key 类型是 Symbol 类型，String 或者其他类型都会导致报错：

```rb
h = {
  :p1 => 100,
  :p2 => 200
}

h_err = {
  'p1' => 100,
  'p2' => 200
}
```

## class

让我们来定义一个手机类：

```ruby
class Phone
  def initialize(brand = '苹果', name = '刘好', number = 15_527_782_945)
    @brand = brand
    @name = name
    @number = number
  end

  def call
    puts "#{@name}使用#{@brand}手机，拨打了#{@number}的号码。"
  end
end

iPhone = Phone.new

iPhone2 = Phone.new('三星', '王自如', 1123321)
```

与函数一样，class 实例化时调用 new 方法也不需要括号。

Ruby 管 `@` 开头的变量叫做实例变量，`@@` 开头的叫做类变量。但其实类似于 Java 的成员变量和静态成员变量。

但不同的是，Ruby 自带封装性。

实例变量默认外部不可以访问，需要我们自定义一下 getter/setter。

- `attr_accessor :xxx` 为 getter/setter
- `attr_reader :xxx` 为 getter
- `attr_writer :xxx` 为 setter

```ruby
class Game
    attr_accessor :price
    attr_reader :name
    def initialize(name = "怪物猎人世界", price = 200)
        @name = name
        @price = price
    end
end
```

### class 下面有啥方法？

`Phone.instance_methods(false)`

这个方法，可以让我们知道该类下，有什么方法是我们自定义的。

对于上面写的 `Phone` 类，输出结果就会是：

```ruby
call
```

就这么简单。

如果参数为 `true` 呢，则会显示所有的方法，包括自带的方法。

### 如何判断这个方法是否存在呢？

```ruby
iphone = Phone.new
iphone.respond_to?("call")
```

`respond_to?` 这个方法可以判断该对象是否拥有某个方法，可以返回一个布尔值。

所以我们通常可以这么用：`iPhone.respond_to?("call") && iPhone.call`

（未完待续）
