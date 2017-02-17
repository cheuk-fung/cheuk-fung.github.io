---
layout: post
title: SICP 閱讀筆記
---

在線閱讀：[_Structure and Interpretation of Computer Programs_](http://sarabander.github.io/sicp/)

## 第一章

- Scheme 語法
- 感受到了 Lisp 的簡洁和表逹力，怪不得會有人喜歡這個滿屏都是括號的語言
- 計算機語言作為從數學到計算機的抽象表達
- 乍一看還以為是在看數學書…

## 第二章

- 基本就是在 DIY 類型系統
- data-directed programming 本質上是方法派發的方式，類似 OOP 裡虛函數的動態派發。它的核心在於方法，「虛函數表」由方法維護，而不是對象。缺點是新的類型必須主動在各個方法的「虚函數表」裡注冊。

## 第三章

- 從**對象**和**流**兩種角度去看**模塊化**
- local state 有助於模塊化，但是 assignment 破壞了 referentially transparent

  > A language that supports the concept that "equals can be substituted for equals" in an expression without changing the value of the expression is said to be **referentially transparent**.

- Programming without any use of assignments is accordingly known as **functional programming**.
- In the presence of assignment, a variable can no longer be considered to be merely a name for a value. Rather, a variable must somehow designate a "place" in which values can be stored.
- 於是 scheme 不是**純函數式**語言
- stream 的表逹能力很強，但隨著抽象層次的提昇，代碼也更加難懂了

## 第四章

- > Metalinguistic abstraction—establishing new languages—...
- 構建程序可以看作是在構建解決特定問題的語言（DSL）

  > In fact, we can regard almost any program as the evaluator for some language. ... Seen from this perspective, the technology for coping with large-scale computer systems merges with the technology for building new computer languages, and computer science itself becomes no more (and no less) than the discipline of constructing appropriate descriptive languages.

- 用 Scheme 寫了個 Scheme 解釋器，在此過程中，實踐了不少編程技術和設計模式。比如 `eval` 一开始運用了 [Template Method]({% post_url 2016-03-28-notes-on-gof-3 %}#template-method-)，後來將 `analyze` 分離後，可視為 [Facade]({% post_url 2016-03-26-notes-on-gof-2 %}#facade-)。
- Nondeterministic Scheme 很有趣，它將深度優先搜索集成到了語言中，用戶只需要提供可能的分支和回溯的條件就好了。

  > the execution procedures in the `amb` evaluator take three arguments: the environment, and two procedures called _continuation procedures_. ... Constructing and calling appropriate continuations is the mechanism by which the nondeterministic evaluator implements backtracking.

- Logic Programming 中實現的 query interpreter 也很有趣。設定好適當的約束，即可以解 `1 + 2 = x`，也可以解 `1 + x = 3`。
- 這章不只是寫了個 Scheme 解釋器，還將應用程序當成 DSL 來實現。DSL 這點 Ruby 學習得非常好。

## 第五章

- 建立了一個基於寄存器的虛擬機器及支持的原子操作，並實現一個虛擬機器的模擬器，能夠執行類似於彙編語言的機器語言。
- 講解了簡單的內存管理和垃圾回收機制
- 又實現了一個 Scheme 解釋器，與第四章中的實現不同，這回需要將 Scheme 「翻譯」成虛擬機器的機器語言。
- 實現了一個 Scheme 編譯器。有趣的是，這個編譯器可以和上述的解釋器合起來使用，程序的一部分編譯一部分解釋。
- 解釋器將機器語言「昇級」成了程序；編譯器反之，將程序「降級」成了機器語言。

  > An interpreter raises the machine to the level of the user program; a compiler lowers the user program to the level of the machine language.

- 尾調用指函數在末尾位置調用自己或另一個函數。執行尾調用時可以不在調用棧中新增一個棧幀，只需要更新它，並且最後一次調用的返回值可以直接返回給首次調用的位置。Scheme 可以將循環寫成遞歸而不損失性能正是利用了尾調用。
- 為什麼 C 和 Pascal 的編譯器不實現尾調用優化？

  > The difficulty with tail recursion in these languages is that their implementations use the stack to store procedure arguments and local variables as well as return addresses.

_SICP_ 从 Scheme 開始講解了計算機科學的基础概念，逐步構建了一個五臓俱全的虛擬機器。面面俱到又非點到為止。草草讀過一遍，並沒有做幾道習題，亦收穫不少。
