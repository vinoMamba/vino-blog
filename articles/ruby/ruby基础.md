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






