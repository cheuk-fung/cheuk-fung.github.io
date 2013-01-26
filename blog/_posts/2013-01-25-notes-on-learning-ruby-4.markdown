---
layout: blog
title: 紅寶石學習筆記（四）
location: Shenzhen, China
post: true

excerpt: A newbie to Ruby.
---

Misc
----

### ||= 運算子 ###
由於 `+=`, `-=` 等運算子的語義，`a ||= b` 可能會被理解成 `a = a || b`。但實際上，Rubb 將其實現為 `a || a = b`，這樣當 `a` 為真（即不為 `nil` 或 `false`）的時候就不會執行賦值操作。

_[What Ruby’s ||= (Double Pipe / Or Equals) Really Does](http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html)_ 這篇文章的一個測試非常好：

{% highlight ruby %}
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
{% endhighlight %}

輸出為：

    Setting hash key x with 10
    Setting hash key y with 10
    Setting hash key y with 10
    Setting hash key z with 10

還有一個 `&&=` 運算子，類似地，語義是 `a && a = b`。
