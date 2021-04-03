# 4.1 启动顺序

> x86启动顺序 - 第一条指令

- CS = F000H, EIP = 0000FFF0H

- 实际地址是：

  Base + EIP = FFFF0000H+0000FFF0H = FFFFFFF0H (H表示HEX)

  这是BIOS的EPROM(Erasable Programmable Read Only Memory) 所在地

- 当CS被新值加载，则地址转换规则将开始起作用

- 通常第一条指令是一条长跳转指令(这样CS和EIP都会更新)到BIOS代码中执行

>  x86启动顺序 - 处于实模式的段

![WeChat40e25ad459ed37b21e23f66fc4a217e3.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3lqshn9qj324s0vc7wh.jpg)

- 段选择子 ( Segment Selector ) : CS, DS, SS,...
- 偏移量 (Offset ): EIP

>  x86启动顺序 - 从BIOS到Bootloader

- BIOS加载存储设备(如软盘、硬盘、光盘、USB盘)上的第一个扇区(主引导扇区, Mater Boot Record,or MBR)的512字节到内存的0x7c00 ...
- 然后转跳到 @0x7c00的第一条指令开始执行
- bootloader做的事情：
  - 实模式(16位)切换到保护模式(32位)， 1MB寻找到4GB寻址
  - 使能保护模式( protection mode ) & 段机制(segment-level protection)
  - 从硬盘上读取kernel in ELF格式的ucore kernel (跟在MBR后面的扇区)并放到内存中的固定位置
  - 跳转到ucore OS的入口点(entry point)执行，这时控制权到了ucore OS中

> x86启动顺序 - 段机制

![WeChateb18d327e08ec29fa2a0a16d47a8fe90.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3m4w7w6pj31tk0xcnpd.jpg)

![WeChata5e73cdb6e9574bf9f654265e5ef92af.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3m87zecej31i00v04qp.jpg)

存在一个数据结构全局描述符表GDT，存储段信息

> x86启动顺序 - 使能保护模式

- 使能保护模式

  bootloader/OS要设置CR0的bit 0

- 段机制

  在保护模式下是自动使能的

>  x86启动顺序 - 加载ELF格式的ucore OS kernel

![WeChat08a515e8ffc2fa0bb4d2662dd53c6e03.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3mhy7s7ej31ic13gb2b.jpg)

![WeChatf1f675afda91551b0ba4804b0d980a9f.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3mj6flzzj31q00skhdu.jpg)



# 4.2 C函数调用的实现

> 需要注意的事项

- 参数(parameters) & 函数返回值(return values) 可通过寄存器或位于内存中的栈来传递
- 不需要保存/恢复(save/restore)所有寄存器

# 4.3 GCC内联汇编

> 什么是内联汇编( inline assembly)

- 这是GCC对C语言的扩展
- 可直接在C语句中插入汇编指令

> 作用

- 调用C语言不支持的指令
- 用编译在C语言中手动优化

> 如何工作

- 用给定的模版和约束来生成汇编指令
- 在C函数内形成汇编源码

![WeChat89f33063ac612954e46ae51be27f82be.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp3xfgrmvzj31b80ng4qp.jpg)

# 4.4 x86中断处理过程 

> 中断源

- 中断 Interrupts

  外部中断External (hardware generated) interrupts

  串口、硬盘、网卡、时钟...

  软件产生的中断Software generated interrupts

  The INT n 指令，通常用于系统调用

- 异常 Exceptions

  程序错误

  软件产生的异常 Software generated exceptions

  INTO, INT 3 and BOUND

  机器检查出的异常S

> 确定中断服务例程 (ISR)

- 每个中断或异常与一个中断服务例程(Interrupt Service Routine, 简称ISR)关联，其关联关系存储在中断描述符表(Interrupt Descriptor Table, 简称IDT)
- IDT的起始地址和大小保存在中断描述符表寄存器IDTR中

![WeChata6c6d10701836b2a70b785027209ec03.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp4lpy6yjwj31os11chdt.jpg)

![WeChat724949f74f5542ac6ba6127711d49380.png](http://ww1.sinaimg.cn/large/008aPpVGgy1gp4lrk4944j31uc1gonpe.jpg)

> 系统调用

- 用户程序通过系统调用访问OS内核服务
- 实现原理
  - 需要指定中断号
  - 使用Trap，也称为Software generated interrupt
  - 或使用特殊指令(SYSENTER/SYSEXIT)

