---
layout: post
title: 紅寶石學習筆記（四）
---

### ||= 運算子

由於 `+=`, `-=` 等運算子的語義，`a ||= b` 可能會被理解成 `a = a || b`。但實際上，Ruby 將其定義為 `a || (a = b)`，這樣當 `a` 為真（即不為 `nil` 或 `false`）時就不會執行賦值操作。

[_What Ruby’s ||= (Double Pipe / Or Equals) Really Does_](http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html)
這篇文章的一個測試非常好：

```ruby
h = {}

def h.[]=(k, v)
  puts "Setting hash key #{k} with #{v.inspect}"
  super
end

# 1. The standard ||= approach
h[:x] ||= 10
h[:x] ||= 20

# 2. The a = a || b approach
h[:y] = h[:y] || 10
h[:y] = h[:y] || 20

# 3. The a || a = b approach
h[:z] || h[:z] = 10
h[:z] || h[:z] = 20
```

輸出為：

```
Setting hash key x with 10
Setting hash key y with 10
Setting hash key y with 10
Setting hash key z with 10
```

還有一個 `&&=` 運算子，與 `||=` 類似，語義是 `a && a = b`。

### for 的視野

出乎意料，`for` 並沒有開啟一個新的視野，所以在 `for` 的區塊中定義的變數在區塊之外仍然有效。因此，[_Ruby 風格指南_](https://github.com/styleguide/ruby) 中建議使用迭代器 (iterator) 取代之。

類似地， `if`, `while`, `case` 等也沒有開啟新的視野。
