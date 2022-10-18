---
title: UUID
date: 2022-06-17 08:23:10
tags: 插件
categories: js
---
> UUID 是 通用唯一识别码（Universally Unique Identifier）的缩写，是一种软件建构的标准，亦为[开放软件基金会](https://baike.baidu.com/item/开放软件基金会/1223731)组织在分布式计算环境领域的一部分。其目的，是让[分布式系统](https://baike.baidu.com/item/分布式系统/4905336)中的所有元素，都能有唯一的辨识信息，而不需要通过中央控制端来做辨识信息的指定。如此一来，每个人都可以创建不与其它人冲突的UUID。在这样的情况下，就不需考虑[数据库](https://baike.baidu.com/item/数据库/103728)创建时的名称重复问题。最广泛应用的UUID，是[微软公司](https://baike.baidu.com/item/微软公司/732128)的全局唯一标识符（GUID），而其他重要的应用，则有Linux ext2/ext3文件系统、LUKS加密分区、GNOME、KDE、Mac OS X等等。另外我们也可以在e2fsprogs包中的UUID库找到实现。

**1. Install**

```
npm install uuid
```

**2. Create a UUID** (ES6 module syntax)

```
import { v4 as uuidv4 } from 'uuid';
uuidv4(); // ⇨ '9b1deb4d-3b7d-4bad-9bdd-2b0d7b3dcb6d'
```

... or using CommonJS syntax:

```
const { v4: uuidv4 } = require('uuid');
uuidv4(); // ⇨ '1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed'
```

## API Summary

<!--more-->

|                                                              |                                              |                   |
| ------------------------------------------------------------ | -------------------------------------------- | ----------------- |
| [`uuid.NIL`](https://www.npmjs.com/package/uuid#uuidnil)     | The nil UUID string (all zeros)              | New in `uuid@8.3` |
| [`uuid.parse()`](https://www.npmjs.com/package/uuid#uuidparsestr) | Convert UUID string to array of bytes        | New in `uuid@8.3` |
| [`uuid.stringify()`](https://www.npmjs.com/package/uuid#uuidstringifyarr-offset) | Convert array of bytes to UUID string        | New in `uuid@8.3` |
| [`uuid.v1()`](https://www.npmjs.com/package/uuid#uuidv1options-buffer-offset) | Create a version 1 (timestamp) UUID          |                   |
| [`uuid.v3()`](https://www.npmjs.com/package/uuid#uuidv3name-namespace-buffer-offset) | Create a version 3 (namespace w/ MD5) UUID   |                   |
| [`uuid.v4()`](https://www.npmjs.com/package/uuid#uuidv4options-buffer-offset) | Create a version 4 (random) UUID             |                   |
| [`uuid.v5()`](https://www.npmjs.com/package/uuid#uuidv5name-namespace-buffer-offset) | Create a version 5 (namespace w/ SHA-1) UUID |                   |
| [`uuid.validate()`](https://www.npmjs.com/package/uuid#uuidvalidatestr) | Test a string to see if it is a valid UUID   | New in `uuid@8.3` |
| [`uuid.version()`](https://www.npmjs.com/package/uuid#uuidversionstr) | Detect RFC version of a UUID                 | New in `uuid@8.3` |