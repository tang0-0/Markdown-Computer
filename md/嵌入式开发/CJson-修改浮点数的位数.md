# CJson-修改浮点数的位数

## 现状
调用`cJSON_Print`将组成的json转为字符串格式时，对于浮点数的位数是不固定的
‘length = sprintf((char*)number_buffer, "%1.15g", d);’
源代码里用的是%1.15g，代表输出字符最少一位，最大15位，自动选择 %e 或 %f 中合适的表示法

