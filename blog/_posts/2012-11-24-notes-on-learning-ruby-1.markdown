---
layout: blog
title: 紅寶石學習筆記（一）
location: NKU, China
post: true

excerpt: A newbie to Ruby.
---

[Ruby 使用手冊](http://guides.ruby.tw/ruby/)
--------------------------------------------

### 簡單示範 ###

- 函數會回傳最後計算的結果，因此有時可以省略 `return` 敘述。
- 命令列引數存在 `ARGV` 陣列裡，並且 `ARGV[0]` 是程式的**第一個引數**而非程式的名稱。程式的名稱是全局變數 `$0`。
- Ruby 的世界裡好像是用兩個空格縮進的呢。

### 字串 ###

- 沒有 `char` 型別，單引號括起的即是字串本身，不處理逸出 (escape) 字元，也不使用 `#{}` 計算裡面的程式；雙引號括起的字串則會。

### 正規表示式 ###

- 匹配運算子：`=~`，一邊是字串，另一邊是正規表示式；如果匹配則會傳回匹配的開始位置，否則傳回 `nil`。
- 還有不匹配運算子：`!~`，回傳值是布林值。[^0]
- 這兩個運算子滿足交換律。[^0]

### 陣列 ###

- `ary[x, y]` 傳回包含從第 x 個項目開始連續 y 個項目的陣列，`x < 0` 的話是指陣列倒數第 `|x|` 個項目。
- `ary[x..y]` 傳回包含從第 x 個項目到第 y 個項目的陣列；如果 x 項目在 y 項目後面，就傳回 `[]`。
- 原來 "hash" 翻譯成「雜湊」，好神奇……另，「雜湊」又稱「關聯陣列 (associative array)」或「字典 (dictionary)」。

### 継續簡單示範 ###

- `print` 和 `puts` 的區別類似 Java 中的 `System.out.print` 和 `System.out.println`。
- 習慣上，在方法名稱後加上驚嘆號（`!`, "bang!"）暗示有某種副作用（如可能改變物件的內容），加上問號（`?`, "huh?"）暗示回傳值為布林值。

### 控制結構 ###

- 測試真值時，除了 `false` 和 `nil` 之外的東西都視為真。（**`0` 也是「真」！**）
- `case` 內部使用「關聯運算子（relationship operator）」`===`，可以一次檢查數個條件。
- `===` 左邊是一般物件時與 `==` 一樣，而左邊是範圍表示式或正規表示式時就會測試右邊是不是在左邊的範圍內或是否符合左邊的正規表示式。所以 `===` 不滿足交換律：[^0]

{% highlight ruby %}
    irb(main):001:0> /def/ === "abcdefg"
    => true
    irb(main):002:0> "abcdefg" === /def/
    => false
{% endhighlight %}

- `next` 相當於 C 中的 `continue`；比 C 多了個可以重新開始當前迴圈的 `redo`，這個蠻方便的。

### 迭代器 ###

- 貌似是從 Ruby 1.9 以後，`retry` 必須要和 `rescue` 一起用，所以 `retry` 的範例程式會產生 `SyntaxError`(`Invalid retry`)。改成下面這個樣子就沒問題了：

{% highlight ruby %}
    c = 0
    begin
      for i in 0..4
        print i
        raise if i == 2 and c == 0
      end
    rescue
      c = 1
      print "\n"
      retry
    end
    print "\n"
{% endhighlight %}

- 後面那個範例和 `yield` 在一起，要明白 `retry` 的是哪一部分：

{% highlight ruby %}
    def WHILE(cond)
        return if not cond
        yield
        raise
    end

    i = 0;

    begin
      WHILE(i < 3) { print i; i += 1 }
    rescue
      retry
    end
{% endhighlight %}

- 不過這種使用 `retry` 的方式很糟糕的感覺。
