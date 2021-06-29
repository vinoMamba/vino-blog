# ruby

## 变量

#### 1. 没有关键字

直接使用`a = 1`,没有关键字`var`,`let`,`const`

#### 2. 局部变量

* 小写字母开头
* `_` 开头

#### 3. 全局变量

* `$`开头

## 常量

全大写,例如`RUBY_VERSION`

## 多重赋值

#### 1. 简化

```
a,b,c = 1,2,3

a,b,*c = 1,2,3,4,5    # a=1, b=2,c =[3,4,5]

a,*b,c = 1,2,3,4,5
```

#### 2. 交换

```ruby
a, b = b, a
```

#### 3. 数组

```ruby
arr = [1, 2]

a, b = arr
```

## 字符串

#### 1. 单引号(写什么就是什么,不会转义)

```ruby
puts '12\n34'

# 12\n34
```

#### 2. 双引号(会进行转义)

```ruby
puts '12\n34'

# 12
# 34
```

#### 3. 多行字符串(会进行转义)

```ruby
s = <<eos
    123
    345
    789
eos

# 123\n3456\n789
```

## 控制语句

#### 1. if

```ruby

if a > 3 then p "大" end

if a > 3 then p "大" else p '小' end 

if a > 3 ? 'big' : 'small'

```

ruby的`if`语句是有返回值得

```ruby
# 如果报错就返回
return if error

# 成功了才返回
return unless success
```

#### 2. case 语句

```ruby
case a 
    when 1 
    '1'
    
    when 2 
    '2'
    
    when 4,6,8 
    '偶数'
    else
    'others'
end
```

同样也有返回值

```ruby
x = case a
        when 1,3,5
            '奇数'
        when 2,4,6
            '偶数' 
    end
```

## 循环语句

#### 1.  `.times`

```ruby
7.times do 
    p 'one Time'
end

7.times { p 'one time'}
```

获取第几次

```ruby
7.times do |i|
    p "#{x} time"
end
```

#### 2. `.each`

```ruby
names = ['leBron','kobe','GiGi']

names.each do |name| 
    p name
end

names.each { |name| p name }
```

#### 3. `for ... in`

```ruby
for i in  1..7 do
    p i
end
```

## ruby中的数据类型

1. **ruby中只有对象**,可以用`.class`来查看类

2. 每个对象上面都有一个`object_id`,表示其唯一性

```ruby
a = 'string'

a.class     # String

a.object_id   # 208
```

#### 1. 数组

```ruby
list = [1,2,3,4,5]

list.class      # Array

list.methods    # 会打印所有的方法,这些方法前面都有一个冒号

```

#### 2. 散列

散列是由`Hash`构造出来的

```ruby
person = { name: 'vino', age: 12 }

person.keys     # [:name, :age]
```

上面的可不是JS里面的对象，里面的`name`,`age`也并非是字符串，而是`symbol`,他等价于:

```ruby
person = { :name => 'vino', :age => 12 }
```

`name`可以看做是下面这种形式的语法糖

如果希望使用字符串作为key,应该写成

```ruby
person = { 'name' => 'vino', 'age' => 12}
    
person.keys     # ['name','age']
```

**Tips: ruby中`.`后面接的是函数，函数甚至可以不用写`()`来执行,这一点区别于JS**

```ruby 
person.keys     # 这里的.keys不是属性，而是函数,相当于js里面的   person.keys()
```

遍历散列使用`.each`方法

```ruby
person.each do |key,value|
    p "key:#{key},value:#{value}"
end

#  key: name,value: vino
#  key: age, value: 12
#  symbol 会自动转换成字符串
```

#### 与JS的区别

1. 获取属性

```ruby
person[:name]
```

2. 给person加一个方法

```ruby
person[:sayHi] = lambda { p "hi" }
# 调用
person[:sayHi].call
```