# 磁盘调度

磁盘访问延迟 = 队列时间 + 控制器时间 + 寻道时间 + 旋转时间 + 传输时间

磁盘调度的目的是减小延迟，其中前两项可以忽略，寻道时间是主要矛盾。

#### 磁盘调度算法

1. .**先来先服务（FCFS）**，按访问请求到达的先后顺序服务。简单，公平，但是效率不高，相临两次请求可能会造成最内到最外柱面寻道，使磁头反复移动，增加了服务时间，对机器不利。

2. **最短寻道时间优先\(SSTF\)**，优先选择距当前磁头最近的访问请求进行服务，主要考虑寻道优先。改善了磁盘平均服务时间，但是造成某些访问请求长期等待得不到服务。

3. **扫描算法（SCAN）**，当设备无访问请求时，磁头不动；当有访问请求时，磁头按一个方向移动，在移动过程中对遇到的访问请求进行服务，然后判断该方向上是否还有访问请求，如果有则继续。

4. **循环扫描算法（CSCAN），**循环扫描调度算法是在扫描算法的基础上改进的。磁臂改为单项移动，由外向里。当前位置开始沿磁臂的移动方向去选择离当前磁臂最近的哪个柱面的访问者。如果沿磁臂的方向无请求访问时，再回到最外，访问柱面号最小的作业请求。

## 文件系统 {#文件系统}

#### 常见文件系统

* Windows: FAT, FAT16, FAT32, NTFS
* Linux: ext2/3/4, btrfs, ZFS
* Mac OS X: HFS+

#### RAID 技术

磁盘阵列（Redundant Arrays of Independent Disks，RAID），独立冗余磁盘阵列之。原理是利用数组方式来作磁盘组，配合数据分散排列的设计，提升数据的安全性。

#### 分区表

* MBR：支持最大卷为2 TB（Terabytes）并且每个磁盘最多有4个主分区（或3个主分区，1个扩展分区和无限制的逻辑驱动器）
* GPT：支持最大卷为18EB（Exabytes）并且每磁盘的分区数没有上限，只受到操作系统限制（由于分区表本身需要占用一定空间，最初规划硬盘分区时，留给分区表的空间决定了最多可以有多少个分区，IA-64版Windows限制最多有128个分区，这也是EFI标准规定的分区表的最小尺寸。另外，GPT分区磁盘有备份分区表来提高分区数据结构的完整性。



