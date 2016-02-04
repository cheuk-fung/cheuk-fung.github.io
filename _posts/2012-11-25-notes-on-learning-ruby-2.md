---
layout: post
title: 紅寶石學習筆記（二）
---

《[Ruby 使用手冊](http://guides.ruby.tw/ruby/)》筆記

### 物件導向思維

- "Object oriented" 譯成「物件導向」似乎比「面象對象」更為達意。
- 在 Ruby 裡任何東西都是物件。

### 方法

- 呼叫或定義方法時，如果不會造成歧義，可以省略引數的括號。

### 存取控制

- Ruby 是**純物件導向語言**。
- `Object` 類別是所有類別的父類別，不在任何一個類別中定義的「函數」其實是它的**私有**方法。雖然所有類別都從 `Object` 類別繼承了這些方法，但由於它們是私有方法，所以只能以函數的方式呼叫。
- <del><code>private:method</code> 冒號後面不能加空格。</del>  
`:method` 是**字串符号** (Symbol)，它是一個可識別的名字而不是字串物串。這裡更好的寫法是：`private :method`。

### 單件方法

- 只給予單一物件的方法，應該是支援多態的一種方式吧。

### 模組

- Ruby 只支援單一繼承，但可以利用模組實現「受限制的多重繼承」，稱之為「混入 (mixin)」。這樣既滿足了對多重繼承的基本需求，又使得繼承關係不至於過份複雜。

### 程序物件

- `Proc.new` 和 `lambda` 的區別：

```ruby
def foo
  x = Proc.new { return "I am foo." }
  puts x.call
  puts "foo ends."
end

def bar
  x = lambda { return "I am bar." }
  puts x.call
  puts "bar ends."
end

foo # => "I am foo."
bar # => nil
```

   輸出是：

```
I am bar.
bar ends.
```
