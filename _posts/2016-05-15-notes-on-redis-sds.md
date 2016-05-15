---
layout: post
title: Redis 閱讀筆記：sds
---

Simple Dynamic String (sds) 是 Redis 中用於代替簡單的 `char *` 類型字符串的數據結構，有以下特點：

- 動態調整字符串 buffer 大小
- 記錄了實際使用長度和分配的 buffer 大小
- 總是 null-terminated
- binary safe，即字符串中間允許出現 `\0`
- 搭配了丰富的 API ，如 `sdsnew()`, `sdsempty()`, `sdsdup()` 等

sds 的實現位於 [sds.h][] 和 [sds.c][]。

[sds.h]: https://github.com/antirez/redis/blob/unstable/src/sds.h
[sds.c]: https://github.com/antirez/redis/blob/unstable/src/sds.c

### 數據結構

實現 sds 的數據結構如下：

- sds

  ```c
  typedef char *sds;
  ```

- sdshdr (sds header)

  ```c
  struct __attribute__ ((__packed__)) sdshdr5 {
      unsigned char flags; /* 3 lsb of type, and 5 msb of string length */
      char buf[];
  };

  struct __attribute__ ((__packed__)) sdshdr8 {
      uint8_t len; /* used */
      uint8_t alloc; /* excluding the header and null terminator */
      unsigned char flags; /* 3 lsb of type, 5 unused bits */
      char buf[];
  };

  /* follows sdshdr16, sdshdr32, sdshdr64 */
  ```

`sds` 僅僅是 `char *` 的別名，而真正存儲了字符串長度、分配空間大小等信息的是 `sdshdr`(sds header)。`sds` 實際會指向 `sdshdr` 的最後一個成員 `buf`。

根據字符串長度不同，`sdshdr` 分為 sdshdr5，sdshdr8，sdshdr16，sdshdr32，sdshdr64 等五種。當字符串的長度超出當前 header 所能表示的最大長度時，會自動昇級 header，直至 `sdshdr64`。由於 `sdshdr5` 只記錄了長度，而沒有成員記錄分配空間，所以分配的 `buf` 大小總是等於長度。從 header 的定義可以看出 Redis 非常注重節省空間。header 有如下特點：

- 對不同長度的 sds 使用不同的 header，其中 `len`，`alloc` 根據支持的最大長度分別使用 1 字節的 `unit8_t`，2 字節的 `unit16_t` 等等。因此不同的 header 分別使用的空間為 1 字節，3 字節，5 字節，7 字節，9 字節。
- [`__attribute__ ((__packed__))`][packed] 是 gcc 的拓展，表示以最少內存的方式生成這個結構體，避免對齊造成的空間浪費。
- header 結構體最後的 `char buf[]` 稱為 [flexible array member][fam]，有如下特點：
  - 結構體的最後一個成員
  - 不指定大小的數組（必須是 `[]` 而不能是指針）
  - 對此結構體的 `sizeof` 操作返回值是此成員在結構體中的 offset，可以認為是結構體除此成員外的大小
  - 在為結構體分配空間時需要預留此成員的空間，比如

    ```c
    struct sdshdr8 *sh = malloc(sizeof(struct sdshdr8) + (length for buf));
    ```

[packed]: https://gcc.gnu.org/onlinedocs/gcc-5.3.0/gcc/Type-Attributes.html
[fam]: https://en.wikipedia.org/wiki/Flexible_array_member

### API 筆記

#### SDS_HDR_VAR

```c
#define SDS_HDR_VAR(T,s) struct sdshdr##T *sh = (void*)((s)-(sizeof(struct sdshdr##T)));
```

計算 sds 的 header 位置，並聲明一個 `sdshdrT` 的指針 `sh` 指向 header。

#### SDS_HDR

```c
#define SDS_HDR(T,s) ((struct sdshdr##T *)((s)-(sizeof(struct sdshdr##T))))
```

將 sds 轉換為 header 的指針。與 `SDS_HDR_VAR` 的區別是 `SDS_HDR` 只計算 sds 的 header 指針，而 `SDS_HDR_VAR` 還會為此指針聲明一個變量 `sh`。

#### sdsnewlen

```c
sds sdsnewlen(const void *init, size_t initlen);
```

可以認為是 sds 的「構造函數」。它以 `init` 的內容創建長度為 `initlen` 的 sds，並會根據 `initlen` 選擇合適的 header。值得注意的是，在創建空字符串時，使用的 header 是 `sdshdr8` 而非 `sdshdr5`。因為創建空字符串常常是為了之後追加內容，而 `sdshdr5` 並不適用於此。

```c
/* Empty strings are usually created in order to append. Use type 8
 * since type 5 is not good at this. */
if (type == SDS_TYPE_5 && initlen == 0) type = SDS_TYPE_8;
```

#### sdsMakeRoomFor

```c
sds sdsMakeRoomFor(sds s, size_t addlen);
```

為 sds 分配額外的空間。在分配時采用預分配技術：

```c
#define SDS_MAX_PREALLOC (1024*1024)
...
sds sdsMakeRoomFor(sds s, size_t addlen) {
    ...
    len = sdslen(s);
    newlen = (len+addlen);
    if (newlen < SDS_MAX_PREALLOC)
        newlen *= 2;
    else
        newlen += SDS_MAX_PREALLOC;
    ...
}
```

#### sdsIncrLen

```c
void sdsIncrLen(sds s, int incr);
```

配合 `sdsMakeRoomFor()` 使用。調用 `sdsMakeRoomFor()` 並追加新內容後，使用此函數修正字符串長度**並在最後補上null terminator**。
