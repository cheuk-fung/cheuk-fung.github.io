---
layout: blog
title: 紅寶石學習筆記（四）
location: Shenzhen, China
post: true

excerpt: "||= 運算子，for 的視野，heredoc"
---

Misc
----

### ||= 運算子 ###
由於 `+=`, `-=` 等運算子的語義，`a ||= b` 可能會被理解成 `a = a || b`。
但實際上，Ruby 將其實現為 `a || a = b`，
這樣當 `a` 為真（即不為 `nil` 或 `false`）的時候就不會執行賦值操作。

[_What Ruby’s ||= (Double Pipe / Or Equals) Really Does_](http://www.rubyinside.com/what-rubys-double-pipe-or-equals-really-does-5488.html)
這篇文章的一個測試非常好：

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

### for 的視野 ###
出乎意料，`for` 並沒有開啟一個新的視野（与 `each` 不同），
所以在 `for` 的區塊中定義的變數會被外部看到。
因此，[_Ruby 風格指南_](https://github.com/styleguide/ruby) 中建議使用迭代器 (iterator) 取代之。

類似的 `if`, `while`, `case` 等也沒有開啟新的視野。

### heredoc ###
可以使用 heredoc 為字串賦值：

{% highlight ruby %}
hello = <<END
hello, world
END
{% endhighlight %}

但是這樣就不好看了：
{% highlight ruby %}
if something
  hello = <<END
hello, world
END
end
{% endhighlight %}

這樣好看一點（`END` 前多了一個 `-`）：
{% highlight ruby %}
if something
  hello = <<-END
hello, world
  END
end
{% endhighlight %}

更好看一點？有點 trick... ：
{% highlight ruby %}
if something
  hello = <<-END.gsub(/^ {4}/, '')
    hello, world
  END
end
{% endhighlight %}
