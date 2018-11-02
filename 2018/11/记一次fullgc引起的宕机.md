**背景**
----
用户使用导入名单功能，上传了一个几十m的Excel，导致频繁Full gc，导致系统宕机了。

![内存分析](https://lemontree863.github.io/2018/11/memory_analy.jpg)

![内存分析2](https://lemontree863.github.io/2018/11/memory2.jpeg)

通过mat可以看到，因为创建了几万个对象导致内存耗尽，系统直接将应用kill了。

**解决方案**
---
1. 限制用户上传excel的行数，根据实际需求调研确定。
2. 使用完workbook后，切记关闭资源。
3. 使用sax的方式解析excel，降低内存消耗。(打开一个40M的excel，dom需要耗9g左右内存，而sax只耗200M内存)

