---
layout: post
title: SICP 閱讀筆記
---

在線閱讀：[Structure and Interpretation of Computer Programs](http://sarabander.github.io/sicp/)

這本書有別於大學時學習過的其它教材的一點在於，它喜歡講道理。然而很多道理其實是看的、寫的代碼多了纔會有所體會的，對入門而言或許猶如過眼雲煙。

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
