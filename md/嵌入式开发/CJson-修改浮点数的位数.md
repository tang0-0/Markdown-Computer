# CJson-修改浮点数的位数

## 现状

调用`cJSON_Print()`将组成的json转为字符串格式时，对于浮点数的位数是不固定的
`length = sprintf((char*)number_buffer, "%1.15g", d);`
源代码里用的是`%1.15g`，代表输出字符最少一位，最大15位，自动选择` %e `或` %f `中合适的表示法。

> 1. %g用于打印浮点型数据时，会去掉多余的零，至多保留六位有效数字（不同于%e的默认保留小数点后6位）
> 2. 当%g用于打印超过6位的浮点型数据时，因为精度问题，%f不得不输出一个不精确的超过六位的数字，%e也是同样，而%g此时会选择%e格式进行输出，并且按第一条要求，去掉多余的零，并且四舍五入到6位数字。这是《C Primer Plus》中所说的超过精度的时候的情况。

## 解决方案

1. 直接修改`print_number()`函数
   这样的更改会造成全局影响。

2. 使用`cJSON_CreateRaw()`
   
   ```c
    char num_string[26]={0};
    float number = 1.23456;
   
    /* 自己将浮点数转换成字符串 */
    snprintf(num_string,sizeof(num_string),"%1.3f",number);
    /* 将字符串传入，这样打印时就会打印成你自己处理的格式的字符串 */
    cJSON_CreateRaw(num_string);
   ```
