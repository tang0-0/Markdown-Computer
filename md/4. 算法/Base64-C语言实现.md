# Base64-C语言实现

## 概述

Base64是一种编码格式，用于将原始二进制转换成ASCII文本格式。

标准转换规则：将3个8位字节数据，拆分成4个6位数据，再高位补充两位0，组成新的4个8位字节数据。如果剩下的字符不足 3 个字节，则用 0 填充，输出字符使用 =，因此编码后输出的文本末尾可能会出现 1 或 2 个 =。

URL-Safe规则：标准的Base64并不适合直接放在URL里传输，因为URL编码器会把标准Base64中的“/”和“+”字符变为形如“%XX”的形式，故将`+`->`-`，`/`->`_`，至于`=`有的算法将其去掉有的替换成`.`。

![一文读懂Base64编码 | 全栈修仙之路](https://ts1.cn.mm.bing.net/th/id/R-C.a9dc21236c2f9eec0827834a736649e7?rik=8VH81wjtfCCrpA&riu=http%3a%2f%2fcdn.semlinker.com%2fbase64-encode-table.png&ehk=4vkidAdX1U7WTaq4NFCZt1EdCyugDzCq7aGKlSo78Cc%3d&risl=&pid=ImgRaw&r=0)

## 实现


