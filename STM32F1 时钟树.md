# STM32F1 时钟树

## 时钟源
1. HSI:高速内部时钟，RC振荡器，频率为8MHz
2. HSE:高速外部时钟，可接石英/陶瓷谐振器，或者接外部时钟源，频率范围为4MHz~16MHz
3. LSI:低速内部时钟，RC振荡器，频率为40kHz
4. LSE:低速外部时钟，接频率为32.768kHz的石英晶体

(LSI可以用于驱动独立看门狗和通过程序选择驱动RTC。RTC用于从停机/待机模式下自动唤醒系统。LSE也可用来通过程序选择驱动RTC(RTCCLK)。)
(内部即指芯片已经集成，外部即指需要设计PCB时接入)

## 锁相环
概念：这个东西可以将输入频率进行放大然后输出，比如说输入1MHz，设置倍频为3倍，最后就能输出3MHz的信号。
PLL:锁相环倍频输出，其时钟输入源可选择为HSI/2、HSE或者HSE/2。倍频可选择为2~16倍，但是其输出频率最大不得超过72MHz。

## 时钟树图像化
![image-20200921172948107](C:\Users\ta\AppData\Roaming\Typora\typora-user-images\image-20200921172948107.png)

## 基于标准库的设置代码
```C
void RCC_Configuration(void)
{
    RCC_DeInit();                                      		//重设RCC寄存器为缺省值
    RCC_HSICmd(ENABLE);                                   	//使能HSI内部高速晶振
    while(RCC_GetFlagStatus(RCC_FLAG_HSIRDY) == RESET);    	//等待使能成功
    
    //配置AHB时钟频率与系统时钟频率一致    
    RCC_HCLKConfig(RCC_SYSCLK_Div1);  
 
    //配置APB1低速时钟频率为AHB时钟频率的1/2                     	
    RCC_PCLK1Config(RCC_HCLK_Div2);  
 
    //配置APB2高速时钟频率与AHB时钟频率一致                      
    RCC_PCLK2Config(RCC_HCLK_Div1);  
                     	
    //配置ADC时钟频率为APB2时钟频率的1/4
    RCC_ADCCLKConfig(RCC_PCLK2_Div4);                      
 
    //将内部晶振时钟2分频后作为PLL时钟源，倍频系数为10（即系统时钟为40MHz）
    RCC_PLLConfig(RCC_PLLSource_HSI_Div2,RCC_PLLMul_10);   
 
    //使能PLL（如果PLL被用于系统时钟，那么它不能被失能）                
    RCC_PLLCmd(ENABLE);     
 
    //等待指定的RCC标志位设置成功，等待PLL初始化成功
    while(RCC_GetFlagStatus(RCC_FLAG_PLLRDY) == RESET);  
 	
    RCC_SYSCLKConfig(RCC_SYSCLKSource_PLLCLK);              //以PLL时钟作为系统时钟源
 
    //0x00:HSI作为系统时钟
    //0x04:HSE作为系统时钟
    //0x08:PLL作为系统时钟
    while(RCC_GetSYSCLKSource() != 0x08);          			//等待PLL时钟成功作为系统时钟源
}

```