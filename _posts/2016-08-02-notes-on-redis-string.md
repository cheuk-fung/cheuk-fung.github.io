---
layout: post
title: Redis 閱讀筆記：String 類型
---

- String 是 Redis 的基本類型，與其相關的命令，如 `SET`、`GET` 等在 _t\_string.c_ 裡實現
- String 最大長度為 512MB
- 與 sds 的區別在於：
  - String 是一個 robj (Redis Object)
  - sds 是 String 的內部表示 (`robj->ptr`) **之一**
- 根據 value 的特點，Redis 會使用不同的數據類型存儲，以節約空間：
  - 不轉換共享對象，即引用計數大於 1 的對象
  - 如果 value 能轉換為 long，則會使用 long 存儲
    - 如果 `value >= 0 && value < 10000`，會共享預先生成的對象（這點和 Java 的 Integer、Byte 等的做法類似）
  - 如果 value 長度在 44 以下，會將 robj 和表示 value 的 sds 緊湊存儲在一起以提高 cache 命中率

    ```c
    robj *o = zmalloc(sizeof(robj)+sizeof(struct sdshdr8)+len+1);
    struct sdshdr8 *sh = (void*)(o+1);
    ```

  - 如果 sds 的空余空間大於實際長度的 10%，會釋放多余的空間
- `INCR`, `INCRBY`, `DECR`, `DECRBY` 等命令的 value 會存儲為 long，所以取值範圍受到 long 的限制
- `INCRBYFLOAT` 命令使用 long double 計算，但是**存儲為 sds**。在日誌中，這個命令會被改寫為 `SET key result`，以保證重現日誌（有可能是在 slave 上）時不會因為浮點計算的差異而導致結果變化。我猜這也是為什麼結果需要用 sds 存儲。
