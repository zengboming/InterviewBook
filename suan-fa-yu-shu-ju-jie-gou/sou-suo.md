# 搜索

**平均查找长度（ASL）：**需和指定key进行比较的关键字的个数的期望值，称为查找算法在查找成功时的平均查找长度。

1. #### 顺序查找

   说明：顺序查找适合于存储结构为顺序存储或链接存储的线性表。

   基本思想：顺序查找也称为线形查找，属于无序查找算法。从数据结构线形表的一端开始，顺序扫描，依次将扫描到的结点关键字与给定值k相比较，若相等则表示查找成功；若扫描结束仍没有找到关键字等于k的结点，表示查找失败。

   复杂度分析：查找成功时的平均查找长度为：（假设每个数据元素的概率相等） ASL = 1/n \* \(1+2+3+…+n\) = \(n+1\)/2 ;

   时间复杂度为O\(n\)。

   ```
   public int SequenceSearch(int[] a, int value) {
   	int length = a.length;
   	for (int i = 0; i < length; i++) {
   		if (a[i] == value) {
   			return i;
   		}
   	}
   	return -1;
   }
   ```

2. #### 二分查找

   说明：元素必须是有序的，如果是无序的则要先进行排序操作。

   基本思想：也称为是折半查找，属于有序查找算法。用给定值k先与中间结点的关键字比较，中间结点把线形表分成两个子表，若相等则查找成功；若不相等，再根据k与该中间结点关键字的比较结果确定下一步查找哪个子表，这样递归进行，直到查找到或查找结束发现表中没有这样的结点。

   复杂度分析：最坏情况下，关键词比较次数为log2\(n+1\)，且期望时间复杂度为O\(log2n\)；

   ```
   public int binarySearch(int[] a, int left, int right, int value) {
   	if (left > right) {
   		return -1;
   	}
   	int temp = (right + left)/2;
   	if (a[temp] == value ) {
   		return temp;
   	} else if (a[temp] < value) {
   		return binarySearch(a, temp + 1, right, value);
   	} else {
   		return binarySearch(a, left, temp - 1, value);
   	}
   }
   ```

3. #### 插值查找
4. #### 斐波那契查找
5. #### 树表查找
6. #### 分块查找
7. #### 哈希查找



