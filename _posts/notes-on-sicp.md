---
layout: post
title: SICP 筆記
---

在線閱讀：[Structure and Interpretation of Computer Programs](http://sarabander.github.io/sicp/)

這本書有別於大學時學習過的其它教材的一點在於，它喜歡講道理。然而很多道理其實是看的、寫的代碼多了纔會有所體會的，對大一新生而言或許猶如過眼雲煙。

## 第一章

- scheme 語法
- 感受到了 lisp 的簡洁和表逹力，怪不得會有人喜歡這個滿屏都是括號的語言
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
- Stream 的表逹能力很強，但隨著抽象層次的提昇，代碼也更加難懂了

## 第四章
