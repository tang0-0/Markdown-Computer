# RK3568-UART通信

## 1. 功能特点

RK3568有10个UART控制器，对应设备树里的uart0-uart9。 Rockchip UART (Universal Asynchronous Receiver/Transmitter) 基于16550A串口标准，支持以下
功能：
- 包含2组64字节的 FIFO，用于接收和传输  
- 支持流控
- 支持速率 115.2Kbps, 460.8Kbps, 921.6Kbps, 1.5Mbps, 3Mbps, 4Mbps
- 支持5、6、7、8 bits数据位。
- 支持1、1.5、2 bits停止位。
- 支持奇校验和偶校验。
- 支持基于中断/DMA 模式

在Linux kernel 4.4和Linux kernel 4.19中，使用8250串口通用驱动，以下为主要驱动文件：
> drivers/tty/serial/8250/8250_core.c # 8250串口驱动核心 
drivers/tty/serial/8250/8250_dw.c # Synopsis DesignWare 8250串口驱动 
drivers/tty/serial/8250/8250_dma.c # 8250串口DMA驱动 
drivers/tty/serial/8250/8250_port.c # 8250串口端口操作 
drivers/tty/serial/8250/8250_early.c # 8250串口early console驱动

## 2. 使用方式

厂家已经适配好了UART控制器的驱动，只要配置好设备树，用户空间就可以使用tty的接口进行读写操作，设备节点为/dev/ttyS0-9。

UART控制器的配置在`rk3568.dtsi`中，默认是关闭的。

```
uart3: serial@fe670000 {
		compatible = "rockchip,rk3568-uart", "snps,dw-apb-uart";
		reg = <0x0 0xfe670000 0x0 0x100>;
		interrupts = <GIC_SPI 119 IRQ_TYPE_LEVEL_HIGH>;
		clocks = <&cru SCLK_UART3>, <&cru PCLK_UART3>;
		clock-names = "baudclk", "apb_pclk";
		reg-shift = <2>;
		reg-io-width = <4>;
		dmas = <&dmac0 6>, <&dmac0 7>;
		pinctrl-names = "default";
		pinctrl-0 = <&uart3m0_xfer>;
		status = "disabled";
	};
```

引脚的配置在`rk3568-pinctrl.dtsi`中。

```
uart3 {
		/omit-if-no-ref/
		uart3m0_xfer: uart3m0-xfer {
			rockchip,pins =
				/* uart3_rxm0 */
				<1 RK_PA0 2 &pcfg_pull_up>,
				/* uart3_txm0 */
				<1 RK_PA1 2 &pcfg_pull_up>;
		};

		/omit-if-no-ref/
		uart3m0_ctsn: uart3m0-ctsn {
			rockchip,pins =
				/* uart3m0_ctsn */
				<1 RK_PA3 2 &pcfg_pull_none>;
		};

		/omit-if-no-ref/
		uart3m0_rtsn: uart3m0-rtsn {
			rockchip,pins =
				/* uart3m0_rtsn */
				<1 RK_PA2 2 &pcfg_pull_none>;
		};

		/omit-if-no-ref/
		uart3m1_xfer: uart3m1-xfer {
			rockchip,pins =
				/* uart3_rxm1 */
				<3 RK_PC0 4 &pcfg_pull_up>,
				/* uart3_txm1 */
				<3 RK_PB7 4 &pcfg_pull_up>;
		};
	};
```

使用的时候在`rk3568-evb1-ddr4-v10.dtsi`中开启uart，配好引脚就可以了。

```
&uart3 {
	status = "okay";
	pinctrl-names = "default";
	pinctrl-0 = <&uart3m1_xfer>;
};
```
