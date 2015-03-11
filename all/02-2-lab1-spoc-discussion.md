# lab1 SPOC思考题

## 个人思考题

NOTICE
- 有"w2l2"标记的题是助教要提交到学堂在线上的。
- 有"w2l2"和"spoc"标记的题是要求拿清华学分的同学要在实体课上完成，并按时提交到学生对应的git repo上。
- 有"hard"标记的题有一定的难度，鼓励实现，在4月1日前完成即可。

---

请描述ucore OS配置和驱动外设时钟的准备工作包括哪些步骤？ (w2l2)
```
  + 采分点：说明了ucore OS在让外设时钟正常工作的主要准备工作
  - 答案没有涉及如下3点；（0分）
  - 描述了对IDT的初始化，包了针时钟中断的中断描述符的设置（1分）
  - 除第二点外，进一步描述了对8259中断控制器的初始过程（2分）
  - 除上述两点外，进一步描述了对8253时钟外设的初始化，或描述了对EFLAG操作使能中断（3分）
 ```
- [x]  

>答：在实地址模式初始化之后，加载IDT等，接着会初始化IDTR寄存器，IDTR的初始化可以在切换到保护模式之前也可以在刚刚切换到保护模式
还没有使能中断的时候初始化。IDTR的高32位是IDT的基址，后16位是IDT的大小。
 ucore OS对8259中断控制器的初始代码：
outb(IO_PIC1+1,0xFF);
outb(IO_PIC2+1,0cFF);//屏蔽所有的中断
outb(IO_PIC1,0x11);//ICW1写入0x11,表明电平触发，系统中有多片8259,在初始化程序中设置ICW4
outb(IO_PIC1 + 1, IRQ_OFFSET); //IRQ_OFFSET为32，所以IRQ0~IRQ７的中断类型码为32~39
outb(IO_PIC1 + 1, 1 << IRQ_SLAVE);//表明主片的IRQ2上连有从片
outb(IO_PIC1 + 1, 0x3);//其他的优先级方式，非缓冲方式，M/S不起作用，其他方式结束中断
outb(IO_PIC2, 0x11);    // ICW1写入0x11,表明电平触发，系统中有多片8259,在初始化程序中设置ICW4
outb(IO_PIC2 + 1, IRQ_OFFSET + 8);  // ICW2IRQ_OFFSET为40，所以IRQ0~IRQ７的中断类型码为40~47
outb(IO_PIC2 + 1, IRQ_SLAVE);       // ICW3表明从片连在主片的IR2
outb(IO_PIC2 + 1, 0x3);             // ICW4其他的优先级方式，非缓冲方式，M/S不起作用，其他方式结束中断
outb(IO_PIC1, 0x68);    // clear specific mask清除特定位的屏蔽
outb(IO_PIC1, 0x0a);    // read IRR by default
outb(IO_PIC2, 0x68);    // OCW3
outb(IO_PIC2, 0x0a);    // OCW3
ucore OS对8253的初始化：
outb(TIMER_MODE, TIMER_SEL0 | TIMER_RATEGEN | TIMER_16BIT);
outb(IO_TIMER1, TIMER_DIV(100) % 256);
outb(IO_TIMER1, TIMER_DIV(100) / 256);
选择通道０，工作在方式２，16位模式
设置分频

lab1中完成了对哪些外设的访问？ (w2l2)
 ```
  + 采分点：说明了ucore OS访问的外设
  - 答案没有涉及如下3点；（0分）
  - 说明了时钟（1分）
  - 除第二点外，进一步说明了串口（2分）
  - 除上述两点外，进一步说明了并口，或说明了CGA，或说明了键盘（3分）
 ```
- [x]  

>  
答：lab1采用系统时钟进行计数，访问了时钟，对串口初始化并访问串口，并且监听键盘的输入并回显在显示器上，所以也访问了CGA。

lab1中的cprintf函数最终通过哪些外设完成了对字符串的输出？ (w2l2)
 ```
  + 采分点：说明了cprintf函数用到的3个外设
  - 答案没有涉及如下3点；（0分）
  - 说明了串口（1分）
  - 除第二点外，进一步说明了并口（2分）
  - 除上述两点外，进一步说明了CGA（3分）
 ```
- [x]  

>  
答： 
lpt_putc(c);
cga_putc(c);
serial_putc(c);
cprintf根据输出的外设不同，使用不同的函数输出数据，包括串口，并口和CGA。
---

## 小组思考题

---

lab1中printfmt函数用到了可变参，请参考写一个小的linux应用程序，完成实现定义和调用一个可变参数的函数。(spoc)
- [x]  



如果让你来一个阶段一个阶段地从零开始完整实现lab1（不是现在的填空考方式），你的实现步骤是什么？（比如先实现一个可显示字符串的bootloader（描述一下要实现的关键步骤和需要注意的事项），再实现一个可加载ELF格式文件的bootloader（再描述一下进一步要实现的关键步骤和需要注意的事项）...） (spoc)
- [x]  

> 


如何能获取一个系统调用的调用次数信息？如何可以获取所有系统调用的调用次数信息？请简要说明可能的思路。(spoc)
- [x]  

> 


如何裁减lab1, 实现一个可显示字符串"THU LAB1"且依然能够正确加载ucore OS的bootloader？如果不能完成实现，请说明理由。
- [x]  

> 

---

## 开放思考题

---

如何修改lab1, 实现在出现除零错误异常时显示一个字符串的异常服务例程的lab1？
- [x]  

> 


在lab1/bin目录下，通过`objcopy -O binary kernel kernel.bin`可以把elf格式的ucore kernel转变成体积更小巧的binary格式的ucore kernel。为此，需要如何修改lab1的bootloader, 能够实现正确加载binar格式的ucore OS？ (hard)
- [x]  

>

GRUB是一个通用的bootloader，被用于加载多种操作系统。如果放弃lab1的bootloader，采用GRU来加载ucore OS，请问需要如何修改lab1, 能够实现此需求？ (hard)
- [x]  

>


如果没有中断，操作系统设计会有哪些问题或困难？在这种情况下，能否完成对外设驱动和对进程的切换等操作系统核心功能？
- [x]  

>  

---
