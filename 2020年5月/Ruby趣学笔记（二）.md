# Ruby 趣学笔记（二）

本文写于 2020 年 5 月 7 日

## 类的继承

之前忘记写了，Ruby 的继承写法是：

```ruby
class IPhone < Phone
    def initialize(id, title, price)
        super
    end
end
```

即可！

## 类型转换

Ruby 的类型转换非常方便，只需要 `.to_i` 就可以转换为 int 类型；`.to_f` 转换为 float 类型……

## 哈希变量

```ruby
rank = {
  "徐航宇" => 2800,
  "Faker" => 2000,
  "Uzi" => 2000
}

puts rank
puts rank["Faker"]

# 类似JSON使用
player = {
    name: "Uzi",
    age: 25,
    rank: 2000
}

puts player
puts player[:name] + ", " + player[:age].to_s + ", " + player[:rank].to_s
```

作为 JS 开发者，我当然是超级喜欢第二种写法的！

## 模块

模块的概念类似于类的概念，可以这么理解，但是**有出入**。

```ruby
module Base
  Version = 'v0.1.1'

  def sayVersion
    return Version
  end

  def add(a, b)
    return a + b
  end

  def self.showVersion
    return Version
  end

  module_function :v
end
```

## 控制流

Ruby 的控制流和其他的区别不大，首先看看 if：

```ruby
tall = 180

if tall >= 180
    puts "高"
elsif tall <= 170
    puts "矮子"
else
    puts "不高不矮"
end
```

Ruby 还有一个叫做 unless 的语句，类似于 if：

```ruby
PUBG_SteamPrice = 40

unless PUBG_SteamPrice < 50
  #大于等于50的时候
  puts "《绝地求生》这个游戏虽然好玩，但是价格太贵，还是学习吧。"
else
  #小于50的时候
  puts "《绝地求生》降价了，剁手买！"
end
```

这个语句很有意思，我们经常写 JS 或者其他语言的时候，会经常遇到这种情况：

```javascript
if (!isDown) {
  // do something
}
```

这个时候用 unless 就很舒服：

```ruby
unless isDown
  puts 'do something'
end
```

Ruby 的 switch 语句和其他的语言不太一样：

**注意！实际生产情况，用表编程就可以了，这个例子仅用于展示语法。**

```ruby
case week_day
  when 0,7
    puts "星期日"
  when 1
    puts "星期一"
  when 2
    puts "星期二"
  when 3
    puts "星期三"
  when 4
    puts "星期四"
  when 5
    puts "星期五"
  when 6
    puts "星期六"
  else
    raise Exception.new("没这天")
end
```

## 数组的遍历

这个和其他的区别不大，就是方法特别多，看看语法就好：

```ruby
gamelist = ["塞尔达传说", "超级马里奥", "开心剪纸"]
for game in gamelist do
    puts game
end

# 循环1-5
for num in 1..5 do
    puts num
end

# 循环1-4
for num in 1...5 do
    puts num
end

# while循环
index = 0
while index < 5 do
    puts "while.index=" + index.to_s
    index+=1
end

# do while循环
index = 0
until index == 5 do
    puts "until.index=" + index.to_s
    index+=1
end

# each循环
gamelist = ["塞尔达传说", "超级马里奥", "开心剪纸"]

gamelist.each { |game|
    puts game
}

gamelist.each do |game|
    puts game
end

gamelist.each_with_index do |game,i|
    puts i.to_s + "." + game
end

# times循环
5.times do |i|
    puts "第 #{i+1} 次times循环"
end

# step循环
1.step(10,3) do |i|
    puts "#{i}"
end

# upto
2.upto(5) do |i|
    puts "updo=" + i.to_s
end

# downto
5.downto(2) do |i|
    puts "downto=" + i.to_s
end
```

## 错误捕捉

```ruby
begin
  # 有可能发生错误的处理
  puts ">处理开始"
  # raise "my raise error!"
  # 10 / 0
rescue => e
  # 错误发生时
  puts "X错误发生!"
  puts e
else
  # 正常处理时
  puts "O正常处理"
ensure
  # 最后处理，无论是否发生处理(final)
  puts "_最后的扫尾处理"
end
```

（完）
