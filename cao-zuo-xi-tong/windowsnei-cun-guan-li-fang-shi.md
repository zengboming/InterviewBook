# Windows内存管理

#### 分页存储管理

将程序的逻辑地址空间划分为固定大小的**页\(page\)**，而物理内存划分为同样大小的**页框\(page frame\)**。程序加载时，可将任意一页放人内存中任意一个页框，这些页框不必连续，从而实现了离散分配。该方法需要CPU的硬件支持，来实现逻辑地址和物理地址之间的映射。在页式存储管理方式中地址结构由两部构成，前一部分是页号，后一部分为页内地址w（位移量）。

逻辑地址道物理地址变化原理：CPU中的内存管理单元\(MMU\)按逻辑页号通过查进程页表得到物理页框号，将物理页框号与页内地址相加形成物理地址

#### 页式管理方式的优点 {#412-页式管理方式的优点}

1）没有外碎片，每个内碎片不超过页大比前面所讨论的几种管理方式的最大进步是，

2）一个程序不必连续存放。

3）便于改变程序占用空间的大小\(主要指随着程序运行，动态生成的数据增多，所要求的地址空间相应增长\)。

缺点：要求程序全部装入内存，没有足够的内存，程序就不能执行。

#### 分段存储

将用户程序地址空间分成若干个大小不等的段，每段可以定义一组相对完整的逻辑信息。存储分配时，以段为单位，段与段在内存中可以不相邻接，也实现了离散分配。 在为某个段分配物理内存时，可以采用首先适配法、下次适配法、最佳适配法等方法。 在回收某个段所占用的空间时，要注意将收回的空间与其相邻的空间合并。

在段式 管理系统中，整个进程的地址空间是二维的，即其逻辑地址由段号和段内地址两部分组成。为了完成进程逻辑地址到物理地址的映射，处理器会查找内存中的段表，由段号得到段的首地址，加上段内地址，得到实际的物理地址。这个过程也是由处理器的硬件直接完成的，操作系统只需在进程切换时，将进程段表的首地址装入处理器的特定寄存器当中。这个寄存器一般被称作段表地址寄存器。

#### 段页存储

它首先将程序按其逻辑结构划分为若干个大小不等的逻辑段，然后再将每个逻辑段划分为若干个大小相等的逻辑页。主存空间也划分为若干个同样大小的物理页。辅存和主存之间的信息调度以页为基本传送单位，每个程序段对应一个段表，每页对应一个页表。CPU访问时，段表指示每段对应的页表地址，每一段的页表确定页所在的主存空间的位置，最后与页表内地址拼接，确定CPU要访问单元的物理地址。

段页存储管理方式综合了段式管理和页式管理的优点，但需要经过两级查表才能完成地址转换，消耗时间多。

#### 分页和分段的主要区别

1. 页是信息的物理单位，分页是为实现离散分配方式，以消减内存的外零头，提高内存的利用率；段则是信息的逻辑单位，它含有一组其意义相对完整的信息，分段的目的是为了能更好地满足用户的需要。

2. 页的大小固定且由系统决定，由系统把逻辑地址划分为页号和页内地址两部分，是由机器硬件实现的，因而在系统中只能有一种大小的页面；而段的长度却不固定，决定于用户所编写的程序，通常由编译程序在对源程序进行编译时，根据信息的性质来划分。

3. 分页的作业地址空间是一维的，即单一的线性地址空间，程序员只需利用一个记忆符，即可表示一个地址；而分段的作业地址空间则是二维的，程序员在标识一个地址是，即需给出段名，又需给出段内地址。

4. 分页信息很难保护和共享、分段存储按逻辑存储所以容易实现对段的保存和共享。

#### 虚拟内存

它使得应用程序认为它拥有连续的可用的内存\(一个连续完整的地址空间\),而实际上，它通常是被分隔成多个物理内存碎片，还有部分暂时存储在外部磁盘存储器上，在需要时进行数据交换。

#### 虚拟地址

指的是由程序产生的由段选择符和段内偏移地址两个部分组成的地址。为什么叫它是虚拟的地址呢？因为这两部分组成的地址并没有直接访问物理内存，而是要通过分段地址的变换机构处理或映射后才会对应到相应的物理内存地址。

#### 逻辑地址

指由程序产生的与段相关的偏移地址部分。不过有些资料是直接把逻辑地址当成虚拟地址，两者并没有明确的界限。

#### 线性地址

指的是虚拟地址到物理地址变换之间的中间层，是处理器可寻指的内存空间（称为线性地址空间）中的地址。程序代码会产生逻辑地址，或者说是段中的偏移地址，加上相应段的基地址就生成了一个线性地址。如果启用了分页机制，那么线性地址可以再经过变换产生物理地址。若是没有采用分页机制，那么线性地址就是物理地址。

#### 物理地址

指的是现在CPU外部地址总线上的寻址物理内存的地址信号，是地址变换的最终结果
