---
layout: post
title: 紅寶石學習筆記（一）
---

《[Ruby 使用手冊](http://guides.ruby.tw/ruby/)》筆記

### 簡單示範

- 函數會回傳最後一步計算的結果，因此最後的 `return` 敘述可以省略。
- 命令列引數存於 `ARGV` 陣列，並且 `ARGV[0]` 是程式的**第一個引數**而非程式的名稱。程式名稱存於全局變數 `$0`。
- Ruby 世界用兩個空格縮進。

### 字串

- 沒有 `char` 型別。
- 單引號括起的字串不處理逸出 (escape) 字元，也不使用 `#{}` 計算裡面的程式；雙引號括起的字串則會。

### 正規表示式

- 匹配運算子 `=~` 用於匹配字串和正規表示式，如果匹配則傳回匹配的子字串的開始位置，否則傳回 `nil`。
- 不匹配運算子 `!~` 的回傳值是布林值。

### 陣列

- `ary[x, y]` 傳回包含從第 x 個項目開始連續 y 個項目的陣列，`x < 0` 的話是指陣列倒數第 `|x|` 個項目。
- `ary[x..y]` 傳回包含從第 x 個項目到第 y 個項目的陣列；而如果 x 項目在 y 項目之後，會傳回 `[]`。
- 「雜湊 (hash)」又稱「關聯陣列 (associative array)」或「字典 (dictionary)」。

### 継續簡單示範

- `print` 和 `puts` 的區別類似 Java 中的 `System.out.print` 和 `System.out.println`。
- 習慣上，在方法名稱後加上驚嘆號（`!`, "bang!"）暗示該方法有某種副作用（如可能改變物件的內容），加上問號（`?`, "huh?"）暗示回傳值為布林值。

### 控制結構

- 測試真值時，除了 `false` 和 `nil` 之外的東西都視為真。（**`0` 也為 `true`！**）
- `case` 內部使用「關聯運算子（relationship operator）」`===`，可以一次檢查數個條件。
- `next` 相當於 C 中的 `continue`，进入下一個迭代。
- `redo` 重新開始當前迭代。
- `===` **左邊**是一般物件時與 `==` 一樣，而**左邊**是範圍表示式或正規表示式時會測試**右邊**是否在左邊的範圍內或是否與左邊的正規表示式匹配。

```ruby
irb(main):001:0> /def/ === "abcdefg" # "abcdefg" 與 /def/ 匹配
=> true
irb(main):002:0> "abcdefg" === /def/ # 此時 `===` 相當於 `==`，两邊型別不同，結果為 `false`
=> false
```

### 迭代器

- 從 Ruby 1.9 開始不再支持在迴圈或迭代中使用 `retry`<sup>[[1](#fn1)]</sup>，所以 `retry` 的範例程式無法運行，會產生 `SyntaxError`(`Invalid retry`)。可以改成下面這樣：

```ruby
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
```

<span id="fn1" />[1]: <http://svn.ruby-lang.org/repos/ruby/tags/v1_9_1_0/NEWS>
