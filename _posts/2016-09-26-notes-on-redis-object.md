---
layout: post
title: Redis 閱讀筆記：Redis Object
---

Redis 實現了一套簡單的類型系統，其對象的數據結構和相關函數聲明於頭文件 _server.h_，實現於 _object.c_。

## 數據結構

```c
typedef struct redisObject {
    unsigned type:4;
    unsigned encoding:4;
    unsigned lru:LRU_BITS; /* lru time (relative to server.lruclock) */
    int refcount;
    void *ptr;
} robj;
```

- `type`: 對象的類型。有如下幾種：

  ```c
  #define OBJ_STRING 0
  #define OBJ_LIST 1
  #define OBJ_SET 2
  #define OBJ_ZSET 3
  #define OBJ_HASH 4
  #define OBJ_MODULE 5
  ```
  前幾種是 Redis 中的基本類型，而最後的 `OBJ_MODULE` 表示這個對象是由擴展模塊創建和管理的。

- `encoding`: 類型的「編碼」，實際上可看做是類型的不同實現。比如 `OBJ_STRING` 類型有 `OBJ_ENCODING_RAW`, `OBJ_ENCODING_INT` 和 `OBJ_ENCODING_EMBSTR` 幾種編碼方式。
- `lru`: LRU 時間戳
- `refcount`: 引用記數，用於 GC
- `ptr`: 對象的實際數據。所以這個 `redisObject`/`robj` 的聲明本質是**對象頭**的聲明。

## 函數筆記

- 對象的默認編碼為 `OBJ_ENCODING_RAW`
- 可以通過 `makeObjectShared(robj *o)` 標記一個對象為共享對象，使其引用記數恒定，不會被 GC。小的整型對象即是通過這種方式共享的。
- 可用 `OBJECT` 命令查看對象的 refcount, encoding 和 idletime 等信息
