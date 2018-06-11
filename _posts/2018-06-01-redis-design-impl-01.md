---
layout:     post
title:      Mastering redis - part1
subtitle:   形象思考与数学推理
date:       2018-06-03
author:     HL
header-img: img/redis/redis-bg.jpeg
catalog: true
tags:
    - redis
    - source code
---
# 简单动态字符串(Simple Dynamic String)
## SDS的基本数据结构
```c
/* Note: sdshdr5 is never used, we just access the flags byte directly.
 * However is here to document the layout of type 5 SDS strings. */
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
struct __attribute__ ((__packed__)) sdshdr16 {
    uint16_t len; /* used */
    uint16_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr32 {
    uint32_t len; /* used */
    uint32_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
struct __attribute__ ((__packed__)) sdshdr64 {
    uint64_t len; /* used */
    uint64_t alloc; /* excluding the header and null terminator */
    unsigned char flags; /* 3 lsb of type, 5 unused bits */
    char buf[];
};
```

## SDS 与 C语言的字符串的比较

|C 字符串 | SDS|
---------|----------
| O(N)获取字符串的长度 | O(1)获取字符串的长度 |
| API不安全，会造成缓冲区溢出 | API安全，不会导致缓冲区溢出 |
| N次修改字符串长度会导致N次内存重新分配 | N次修改字符串长度最多N次内存重新分配|
| 只能保存文本数据 | 可以保存文本数据或者二进制数据|
| 可以使用<string.h> 中的所有库函数 | 可以使用<string.h>中的部分库函数|

## SDS不完全API
 ```c

sds sdsnewlen(const void *init, size_t initlen);
/*创建一个包含C字符串的SDS */ 
sds sdsnew(const char *init);
/* 创建一个SDS空字符串 */ 
sds sdsempty(void);
/* 创建一个SDS字符串的副本 */
sds sdsdup(const sds s);
/* 释放资源  */
void sdsfree(sds s);
/* 使用空字符来扩容*/
sds sdsgrowzero(sds s, size_t len);
sds sdscatlen(sds s, const void *t, size_t len);
/* 拼接给定的C字符串 */   
sds sdscat(sds s, const char *t);
/* 拼接给定的SDS字符串*/  
sds sdscatsds(sds s, const sds t);
sds sdscpylen(sds s, const char *t, size_t len);
/* 拷贝覆盖*/  
sds sdscpy(sds s, const char *t);

sds sdscatvprintf(sds s, const char *fmt, va_list ap);
#ifdef __GNUC__
sds sdscatprintf(sds s, const char *fmt, ...)
    __attribute__((format(printf, 2, 3)));
#else
sds sdscatprintf(sds s, const char *fmt, ...);
#endif

sds sdscatfmt(sds s, char const *fmt, ...);
/*从SDS字符串的前后两端移除在给定C字符串中的字符  */
sds sdstrim(sds s, const char *cset);
/* 只保留range范围内的字符，不然范围内的会被清除或者覆盖  */
void sdsrange(sds s, ssize_t start, ssize_t end);
void sdsupdatelen(sds s);
/* 清空SDS保存的字符串的内容O(1),惰性释放*/  
void sdsclear(sds s);
/* 比较两个SDS字符串*/  
int sdscmp(const sds s1, const sds s2);
sds *sdssplitlen(const char *s, ssize_t len, const char *sep, int seplen, int *count);
void sdsfreesplitres(sds *tokens, int count);
void sdstolower(sds s);
void sdstoupper(sds s);
sds sdsfromlonglong(long long value);
sds sdscatrepr(sds s, const char *p, size_t len);
sds *sdssplitargs(const char *line, int *argc);
sds sdsmapchars(sds s, const char *from, const char *to, size_t setlen);
sds sdsjoin(char **argv, int argc, char *sep);
sds sdsjoinsds(sds *argv, int argc, const char *sep, size_t seplen);

/* Low level functions exposed to the user API */
sds sdsMakeRoomFor(sds s, size_t addlen);
void sdsIncrLen(sds s, ssize_t incr);
sds sdsRemoveFreeSpace(sds s);
size_t sdsAllocSize(sds s);
void *sdsAllocPtr(sds s);

/* Export the allocator used by SDS to the program using SDS.
 * Sometimes the program SDS is linked to, may use a different set of
 * allocators, but may want to allocate or free things that SDS will
 * respectively free or allocate. */
void *sds_malloc(size_t size);
void *sds_realloc(void *ptr, size_t size);
void sds_free(void *ptr);
 ```

## SDS的重点
* 通常而言Redis只会使用C的字符串作为字面值常量，大多数情况下使用自己的SDS来表示字符串。
* 比起C的字符串，SDS的优点：
    1. 常熟复杂度的字符串长度获取
    2. 避免缓冲区溢出
    3. 减少内存重新分类次数
    4. 二进制安全
    5. 兼容部分C字符串操作函数


# 链表