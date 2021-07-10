#  FreeRTOS系统的开发一

大家好，这是本人2021年暑期开始对于cubemx以及cubeIDE使用的记录，其中存在一些bug以及一些遗留的问题等待解决。由于本人也是小菜鸟一枚，很多程序的编写过程中也存在的一些不足之处欢迎大家对我进行指点。


## 项目内容

项目主要是通过cubemx生成的FreeRTOS的一个CMSISv2版本的模板，其中我添加了两个任务为task1、task2。

## Task1

		for(;;)
		  {
			  HAL_GPIO_TogglePin(GPIOD, GPIO_PIN_2);
			  ss++；
			  osDelay(500);
		  }
我在void Task01(void *argument)函数中直接加了一个os的500ms延时函数以及一个HAL库的IO取反函数。通过TASK01任务可以显示当前系统的运行状态。
## Task2

		printf("hello!\r\n");
		osDelay(500);
我在任务2放置的任务就是项目中比较复杂的一部分了，其中也存在三个bug等待后期的研究下面我也会说到。这边我是对于HAL库的串口进行再次修改，使用Printf来进行串口打印，也是这个原因导致我琢磨了一个多小时。
## Bug1 无法包含stdio.h

	#include <stdio.h>
		#ifdef __GNUC__
	#define PUTCHAR_PROTOTYPE int __io_putchar(int ch)
		#else
	#define PUTCHAR_PROTOTYPE int fputc(int ch, FILE *f)
	#endif

	PUTCHAR_PROTOTYPE
	{
		//同样USART1改为你的串口
		HAL_UART_Transmit(&huart1, (uint8_t*)&ch,1,HAL_MAX_DELAY);
	    return ch;
	}
本人的笔记本电脑中，始终无法包含stdio.h文件，会有个”No include files were found that matched that nam"提示。目前尚未解决。
## Bug2 串口打印
使用下面函数时没有输出，放入循环中却能够输出。

			printf("hello!");
			
经过cddn、google等一顿操作后，经过其他up的指点后发现有以下两个解决办法：

1.  在 printf 后面再加上一句 **fflush(stdout)**
2.  简单的在每句 printf 里都加上 \**n**

## Bug3？ 无法打印的任务

Task2任务，我最开始的写法：

		for(;;)
		  {
			 if(ss==2)
			 {
				 ss=0;
				 printf("hello!\r\n");
			 }
		  }

但是意外的发现，串口也无法打印。关于这个问题是否是个bug尚无法确认，我打算后期找个大佬问问。

## 留言：
后面也会慢慢更新RTOS的程序，但是也可能比较缓慢，有点拖延症。
关于文件中有个Debug以及Release这两个文件夹是由于编译中选择了两次不同导致的，因此目前HEX文件生成目录在Release文件夹中，Debug文件中为早期的编译垃圾。


# BY：小十一
