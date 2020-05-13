# Ruby 趣学笔记（一）

本文写于 2020 年 5 月 6 日

最近在 mac 上探索到了 homebrew 的使用方法，对 ruby 的兴趣直线上升，所以来学一学。

最近几年确实大家一直在唱衰 Ruby，整个社区的生态确实也不如 python 那么庞大，但是这都不妨碍 ruby 被称作“快乐编程”。

这几年越来越强调语言的性能，但是 Ruby 的作者松本行弘却认为，**人才是最重要的！**

自看到第一眼后，我就深以为同！

这其实就是一个价值观的问题，人更值钱，那么就应该尽量节省人力；机器算力更值钱，那么就应该用人力去优化程序，来节省机器的算力。

但是国内的大环境大家也懂，人多爆了。

那么自然而然，从上面的结论来看，Ruby 在国内就不会流行——

Ruby 作为多范式编程语言，真的很难协作；作为解释型语言，运行速度也是超级慢……

可很多人对于 Ruby 的评价仍然是：程序员的一把尖刀！

并且在 web 开发领域，很多大公司坚持在使用 Ruby，这足以说明 Ruby 的强大了！

## 函数

```ruby
def sayHelo
    puts "Helo World."
end

sayHelo
```

就这么简单，看起来还蛮像 python 的。但是我们继续深入，就会发现，ruby 的语言真的**令人舒适**。

**传参的函数**

```ruby
def sayHello(name)
  puts "Hello, #{name}."
end
```

我们还可以提供一个默认值，就像这样：

```ruby
def sayHello(name = 'world')
  puts "Hello, #{name}."
end
```

这样，如果我们不传入参数，那么就会自动将 world 赋给 name 变量。

## class

Ruby 是一个纯 OOP 语言，那么自然开头就要讲 class 啦！

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

iphone = Phone.new

iphone2 = Phone.new('三星', '王自如', 1123321)
```

大家从这里就能看到，Ruby 的语言是多么的像英语。

在这个类中我们可以看到有个`@variable`，这个有什么用呢？其实就是一种变量。

- 局部变量：局部变量是在方法中定义的变量，在方法外是不可用的，并且习惯以以小写字母或`_`开始（只是习惯）；
- 实例变量：实例变量可以跨任何特定的实例或对象中的方法使用，这意味着，实例变量可以从对象到对象的改变，要想定义它，需要之前放置符号`@`（这是语法，不是习惯！）；
- 类变量：类变量可以跨不同的对象使用，它属于类，且是类的一个属性，要想定义它，需要在变量名之前放置符号`@@`；
- 全局变量：类变量不能跨类使用。如果想要有一个可以跨类使用的变量，您需要定义全局变量，它是以美元符号`$`开始的。

那么如何改变实例变量呢？

使用`attr_accessor`: 定义可存取对象的属性。

```ruby
class Game
    attr_accessor :price #, :name
    def initialize(name = "怪物猎人世界", price = 200)
        @name = name
        @price = price
    end
end
```

此时 price 变量就是可以改变的，name 就不可以改变。

可以利用`respond_to?`来判断一下（_这个方法一会儿会说_）：

```ruby
mygame.respond_to?("name") # false
mygame.respond_to?("price") # true
```

### class 下面有啥方法？

`Phone.instance_methods(false)`

这个方法，可以让我们知道该类下，有什么方法是我们自定义的。

对于上面写的`Phone`类，输出结果就会是：

```ruby
call
```

就这么简单。

如果中间写的是`true`呢，则会显示所有的方法，比如一些自带的方法。

### 如何判断这个方法是否存在呢？

```ruby
iphone = Phone.new
iphone.respond_to?("call")
```

`respond_to?`这个方法可以判断该对象是否拥有某个方法，可以返回一个布尔值。

所以我们通常可以这么用：`iphone.respond_to?("call") && iphone.call`

## 变量类型

什么？？？变量类型？

Ruby 不存在所谓的“基本类型”，比如`int`/`float`/`char`/...

一切皆对象的原则被严格恪守！

什么意思呢？

```ruby
puts 1.to_s
```

`to_s`天生的就是 1 的方法！

对于数据类型来说，Ruby 拥有：Number、String、Ranges、Symbols，以及 true、false 和 nil 这几个特殊值，同时还有两种重要的数据结构——Array 和 Hash。

### Array

别的类型其实没太多好说的，都大同小异。

我们从 Array 类型的方法，来体验一下 Ruby 的灵活性！

```ruby
games = ['魔兽世界', '塞尔达传说', '金庸群侠传']
puts games
```

这个 games 的定义，如果你在 RubyMine 或者其他强大的 IDE 中编写，他就会告诉你，有更好的方式：`games = %w[魔兽世界 塞尔达传说 金庸群侠传]`

**这就是为什么 Ruby 不易协作**，一千个程序员有一千种写法与实现方法！

#### 数组的遍历

这是永远的重点，正常编程中最为常用的方法。

Ruby 可以通过`each`来遍历数组，具体语法如下：

```ruby
games.each do |game|
  puts "我喜欢#{game}"
end
```

这个地方稍微提一句，我曾在某个地方看到过，写单引号的程序最后会比写双引号的小（文件大小）。

但是这里这种模板字符串的写法，必须要双引号。

**带上数组下标的遍历**

```ruby
games.each_with_index do |game, i|
  puts "第#{i}个游戏是#{game}"
end
```

#### 数组的连接

`games.join(',')`，这个方法其实很多语言都有，可以返回一个由逗号连接的、由数组每个元素顺序组成的字符串。

#### 怎么判断该变量是否是数组

这个怎么判断呢？？？

很简单，还是运用`respond_to?`。

怎么用？

刚刚的`each`方法就可以！

`games.respond_to?('each') && puts '这是一个数组！'`

## 字符串操作

刚刚的一些示例代码，应该可以看出来一些套路。

接下来总结一下：

- `+`：字符连接
- `<<`：字符带入
- `*`：字符循环

```ruby
a = "helo";
b = "world";
puts a + " " + b;

# <<：字符带入
a << b
puts a
puts b

# 这个其实相当于 a = a + b

# *：字符循环
a="A"
puts a*4
```

（未完待续）
