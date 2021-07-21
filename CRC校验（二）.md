## CRC校验（二）

参考：
1. <https://blog.csdn.net/liyuanbhu/article/details/7882789>
2. <https://www.cnblogs.com/esestt/archive/2007/08/09/848856.html>
3. <https://www.cnblogs.com/skullboyer/p/8342167.html>

验证算法的网站：<http://www.ip33.com/crc.html>

## 计算步骤
1. 选择一组生成多项式，转化成二进制表示；
2. 根据多项式位数N，在原数据后补(N-1)个0；
3. 进行模2运算，得到(N-1)位校验码；

## 算法对应原理的一些改动

1. 生成多项式首位可以去掉，以CRC16为例
G(X) = X^16^+X^15^+X^2^+X^0^
    = 1 1000 0000 0000 0101
2. 显然，这个位数有17位，超过了一般数据类型的位数，一个u16是存放不了的，但用一个u32来存放又太浪费了。而在运算中这个首位每次都是和`1`异或，重要的是第二位的异或，所以可以不存储首位，在算法中再做些处理就可以获得原来的效果了。那么可以只存储`1000 0000 0000 0101`
3. 上面的计算步骤是最基本的CRC除法，有个很明显的缺陷，就是原数据是0的情况下怎么计算？。因此真正应用的CRC 算法基本都在原始的CRC算法的基础上做了些小的改动。增加了两个概念，第一个是“余数初始值”[^1]，第二个是“结果异或值”[^2]。
4. 有些算法要求将输入和输出进行首尾反转，即`1011 0101`->`1010 1101`

**CRC算法参数模型解释：**
NAME：参数模型名称。
WIDTH：宽度，即CRC比特数。
POLY：生成项的简写，以16进制表示。例如：CRC-32即是0x04C11DB7，忽略了最高位的"1"，即完整的生成项是0x104C11DB7。
INIT：这是算法开始时寄存器（crc）的初始化预置值，十六进制表示。
REFIN：待测数据的每个字节是否按位反转，True或False。
REFOUT：在计算后之后，异或输出之前，整个数据是否按位反转，True或False。
XOROUT：计算结果与此参数异或后得到最终的CRC值

**CRC16常见几个标准的算法**

| CRC算法名称        | 多项式公式                                                   | 宽度 | 多项式   | 初始值   | 结果异或值 | 输入反转 | 输出反转 |
| :----------------- | :----------------------------------------------------------- | :--- | :------- | :------- | :--------- | :------- | :------- |
| CRC-4/ITU          | x4 + x + 1                                                   | 4    | 03       | 00       | 00         | true     | true     |
| CRC-5/EPC          | x5 + x3 + 1                                                  | 5    | 09       | 09       | 00         | false    | false    |
| CRC-5/ITU          | x5 + x4 + x2 + 1                                             | 5    | 15       | 00       | 00         | true     | true     |
| CRC-5/USB          | x5 + x2 + 1                                                  | 5    | 05       | 1F       | 1F         | true     | true     |
| CRC-6/ITU          | x6 + x + 1                                                   | 6    | 03       | 00       | 00         | true     | true     |
| CRC-7/MMC          | x7 + x3 + 1                                                  | 7    | 09       | 00       | 00         | false    | false    |
| CRC-8              | x8 + x2 + x + 1                                              | 8    | 07       | 00       | 00         | false    | false    |
| CRC-8/ITU          | x8 + x2 + x + 1                                              | 8    | 07       | 00       | 55         | false    | false    |
| CRC-8/ROHC         | x8 + x2 + x + 1                                              | 8    | 07       | FF       | 00         | true     | true     |
| CRC-8/MAXIM        | x8 + x5 + x4 + 1                                             | 8    | 31       | 00       | 00         | true     | true     |
| CRC-16/IBM         | x16 + x15 + x2 + 1                                           | 16   | 8005     | 0000     | 0000       | true     | true     |
| CRC-16/MAXIM       | x16 + x15 + x2 + 1                                           | 16   | 8005     | 0000     | FFFF       | true     | true     |
| CRC-16/USB         | x16 + x15 + x2 + 1                                           | 16   | 8005     | FFFF     | FFFF       | true     | true     |
| CRC-16/MODBUS      | x16 + x15 + x2 + 1                                           | 16   | 8005     | FFFF     | 0000       | true     | true     |
| CRC-16/CCITT       | x16 + x12 + x5 + 1                                           | 16   | 1021     | 0000     | 0000       | true     | true     |
| CRC-16/CCITT-FALSE | x16 + x12 + x5 + 1                                           | 16   | 1021     | FFFF     | 0000       | false    | false    |
| CRC-16/X25         | x16 + x12 + x5 + 1                                           | 16   | 1021     | FFFF     | FFFF       | true     | true     |
| CRC-16/XMODEM      | x16 + x12 + x5 + 1                                           | 16   | 1021     | 0000     | 0000       | false    | false    |
| CRC-16/DNP         | x16 + x13 + x12 + x11 + x10 + x8 + x6 + x5 + x2 + 1          | 16   | 3D65     | 0000     | FFFF       | true     | true     |
| CRC-32             | x32 + x26 + x23 + x22 + x16 + x12 + x11 + x10 + x8 + x7 + x5 + x4 + x2 + x + 1 | 32   | 04C11DB7 | FFFFFFFF | FFFFFFFF   | true     | true     |
| CRC-32/MPEG-2      | x32 + x26 + x23 + x22 + x16 + x12 + x11 + x10 + x8 + x7 + x5 + x4 + x2 + x + 1 | 32   | 04C11DB7 | FFFFFFFF | 00000000   | false    | false    |



## 算法实现步骤

这里以CRC16_MODBUS的实现为例：

1. 设置CRC寄存器，并给其赋值为“余数初始值”；
2. 将8-bit数据进行输入倒转；
3. 将8-bit数据与CRC寄存器高八位异或并保存在CRC寄存器中；
4. 检查最高位是否为1，若为1则左移一位，再与0x8005相异或，否则只左移一位；
5. 重复第4步直到8次移位全部完成；
6. 重复将所有输入数据操作完成以上步骤；
7. 将寄存器值与0x000异或(可以省略，和0异或就是本身)；
8. 将输出数据进行输出倒转，即得到CRC16_MODBUS校验码。

```C
typedef unsigned short u16;
typedef unsigned char u8;
//将8位二进制首尾反转，1010 1011 -> 1101 0101
u8 FanzhuanU8(u8 data)
{
    u8 temp;
    u8 result=0x00;
    for (int i=0;i<4;i++)
    {
        temp = (0x01<<i) & data;
        temp = temp << (7-i*2);
        result = result | temp;
        temp = (0x80>>i) & data;
        temp = temp >> (7-i*2);
        result = result | temp;
    }
    return result;
}
//将16位二进制首尾反转
u16 FanzhuanU16(u16 data)
{
    u16 temp;
    u16 result=0x0000;
    for (int i=0;i<8;i++)
    {
        temp = (0x0001<<i) & data;
        temp = temp << (15-i*2);
        result = result | temp;
        temp = (0x8000>>i) & data;
        temp = temp >> (15-i*2);
        result = result | temp;
    }
    return result;

}
//正序CRC16_Modbus算法
u16 crc16_check1(u8 *puchMsg, u16 usDataLen)
{
    u16 i,j,crc_reg,check;
    crc_reg = 0xFFFF;
    for(i=0;i<usDataLen;i++)
    {
        crc_reg ^= FanzhuanU8(puchMsg[i])<<8;
        for(j=0;j<8;j++)
        {
            check = crc_reg & 0x8000;
            crc_reg <<= 1;
            if(check==0x8000)
            {
                crc_reg ^= 0x8005;
            }
        }
    }
    return FanzhuanU16(crc_reg);

}
//逆序CRC16_Modbus算法
u16 crc16_check2(u8 *puchMsg, u16 usDataLen)
{
    u16 i,j,crc_reg,check;
    crc_reg = 0xFFFF;
    for(i=0;i<usDataLen;i++)
    {
        crc_reg = crc_reg ^ puchMsg[i];
        for(j=0;j<8;j++)
        {
            check = crc_reg & 0x0001;
            crc_reg >>= 1;
            if(check==0x0001)
            {
                crc_reg ^= 0xA001;
            }
        }
    }
    return crc_reg;
}
```







[^1]:所谓的“余数初始值”就是在计算CRC值的开始，给CRC寄存器一个初始值。
[^2]:结果异或值是在其余计算完成后将CRC寄存器的值在与这个值进行一下异或操作作为最后的校验值。