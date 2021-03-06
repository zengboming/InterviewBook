# 排序

内部排序算法：直接插入排序、希尔排序、简单选择排序、堆排序、冒泡排序、快速排序、归并排序、基数排序

![](/assets/import1.png)

时间复杂，空间复杂度，稳定性：![](/assets/739525-20160503202729044-614991035.jpg)

### 1.直接插入排序 {#1直接插入排序}

**思想**：每次将一个待排序的数据按照其关键字的大小插入到前面已经排序好的数据中的适当位置，直到全部数据排序完成。**时间复杂度**：O\(n2\) O\(n\) O\(n2\) （最坏 最好 平均）**空间复杂度**：O\(1\)**稳定性**： 稳定 每次都是在前面已排好序的序列中找到适当的位置，只有小的数字会往前插入，所以原来相同的两个数字在排序后相对位置不变。 代码：

```
public void InsertSort(int[] a) {
    int length = a.length;
    for (int i = 1; i < length; i++ ) {
        if (a[i] < a[i-1]) {
            int j = i-1;
            int temp = a[i];
            while (j >= 0 && temp < a[j]) {
                a[j+1] = a[j];
                j--;
            }
            a[j+1] = temp;
        }
    }
}
```

### 2.希尔排序 {#2希尔排序}

**思想**：希尔排序根据增量值对数据按下表进行分组，对每组使用直接插入排序算法排序；随着增量逐渐减少，每组包含的关键词越来越多，当增量减至1时，整体采用直接插入排序得到有序数组，算法终止。**时间复杂度**：O\(n2\) O\(n\) O\(n1.5\) （最坏，最好，平均）**空间复杂度**：O\(1\)**稳定性**：不稳定 因为是分组进行直接插入排序，原来相同的两个数字可能会被分到不同的组去，可能会使得后面的数字会排到前面，使得两个相同的数字排序前后位置发生变化。**不稳定举例**: 4 3 3 2 按2为增量分组，则第二个3会跑到前面**代码**：

```
public void ShellSort(int[] a) {
    int length = a.length;
    int n = length/2;
    while (n >= 1) {
        for (int i = n; i < length ; i++) {
            //插入排序
            if (a[i] < a[i-n]) {
                int temp = a[i];
                int j = i-n;
                while (j >= 0 && temp < a[j]) {
                    a[j+n] = a[j];
                    j -= n;
                }
                a[j+n] = temp;
            }
        }
        n /= 2;
    }
}
```

### 3.冒泡排序 {#3冒泡排序}

**思想**：对待排序元素的关键字从后往前进行多遍扫描，遇到相邻两个关键字次序与排序规则不符时，就将这两个元素进行交换。这样关键字较小的那个元素就像一个泡泡一样，从最后面冒到最前面来。**时间复杂度**：最坏：O\(n2\) 最好: O\(n\) 平均: O\(n2\)**空间复杂度**：O\(1\)**稳定性**：稳定，相邻的关键字两两比较，如果相等则不交换。所以排序前后的相等数字相对位置不变。**代码**：

```
public static void bubbleSort(int[] array) {
    for (int i = 0; i < array.length; i++) {
        for (int j = array.length -1; j >= i + 1; j--) {
            if (array[j -1 ] > array[j]) {
                int temp = array[j - 1];
                array[j - 1] = array[j];
                array[j] = temp;
            }
        }
    }
}

冒泡排序两种优化方案：1.增加一个交换标志位，如果没有交换，则结束循环。2.双向冒泡。
```

### 4.快速排序 {#4快速排序}

**思想**：该算法是分治算法，首先选择一个基准元素,根据基准元素将待排序列分成两部分,一部分比基准元素小,一部分大于等于基准元素,此时基准元素在其排好序后的正确位置,然后再用同样的方法递归地排序划分的两部分。基准元素的选择对快速排序的性能影响很大，所有一般会想打乱排序数组选择第一个元素或则随机地从后面选择一个元素替换第一个元素作为基准元素。**时间复杂度**：最坏:O\(n2\) 最好: O\(nlogn\) 平均: O\(nlogn\)**空间复杂度**：O\(nlogn\)用于方法栈**稳定性**：不稳定 快排会将大于等于基准元素的关键词放在基准元素右边，加入数组 1 2 2 3 4 5 选择第二个2 作为基准元素，那么排序后 第一个2跑到了后面，相对位置发生变化。**代码**：

```
public void FastSort(int[] a, int low, int high) {
        if (low >= high) {
            return;
        }
        int begin = low;
        int end = high;
        int pos = a[begin];
        while (begin < end) {
            while (begin < end && pos <= a[end]) {
                end--;
            }
            int temp = a[end];
            a[end] = a[begin];
            a[begin] = temp;

            while(begin < end && pos >= a[begin]) {
                begin++;
            }
            temp = a[begin];
            a[begin] = a[end];
            a[end] = temp;
        }
        //begin为分割点
        FastSort(a, low, begin -1 );
        FastSort(a, begin + 1, high);
    }
```

三向快速排序算法

```
/**
 * 三向切分快速排序, 适用于存在大量重复元素的数组
 *
 * @param array
 */
public static void quick2waySort(int[] array) {
    quick2waySort(array, 0, array.length - 1);
}

private static void quick2waySort(int[] array, int lo, int hi) {
    if (hi <= lo) {
        return;
    }
    int lt = lo, gt = hi, i = lo + 1;
    int val = array[lo];
    while (i <= gt) {
        if (array[i] < val) {
            exch(array, i++, lt++);
        } else if (array[i] > val) {
            exch(array, i, gt--);
        } else {
            i++;
        }
    }
    // lt 到 gt 之间的都是等于val 的. 如果存在大量重复元素的数组使用该算法可以极大提升算法效率,
    quick2waySort(array, lo, lt - 1);
    quick2waySort(array, gt + 1, hi);
}
```

### 5.直接选择排序 {#5直接选择排序}

**思想**：首先在未排序序列中找到最小（大）元素，存放到排序序列的起始位置，然后每次从剩余未排序元素中继续寻找最小（大）元素放到已排序序列的末尾。以此类推，直到所有元素均排序完毕**时间复杂度**：最坏:O\(n2\) 最好: O\(n2\) 平均: O\(n2\)**空间复杂度**：O\(1\)**稳定性**：不稳定 例如数组 2 2 1 3 第一次选择的时候把第一个2与1交换使得两个2的相对次序发生了改变。**代码**：

```
public void SimpleSelectSort(int[] a) {
    int length = a.length;
    for (int i = 0; i < length ; i++) {
        int pos = i;
        for (int j = i + 1; j < length; j++) {
            if (a[j] < a[pos]) {
                pos = j;
            }
        }
        if (pos != i) {
            int temp = a[pos];
            a[pos] = a[i];
            a[i] = temp;
        }
    }
}
```

#### 二元选择排序

简单选择排序，每趟循环只能确定一个元素排序后的定位。我们可以考虑改进为每趟循环确定两个元素（当前趟最大和最小记录）的位置,从而减少排序所需的循环次数。改进后对n个数据进行排序，最多只需进行\[n/2\]趟循环即可。

```
//二元选择排序：每次确定两个数（最大和最小）
public void DoubleSelectSort(int[] a) {
    int length = a.length;
    for (int i = 0; i < length/2 ; i++) {
        int min = i;
        int max = i;
        for (int j = i + 1; j <= length - i - 1; j++) {
            if (a[j] < a[min]) {
                min = j;
                continue;
            }
            if (a[j] > a[max]) {
                max = j;
            }
        }
        if (min != i) {
            int temp = a[min];
            a[min] = a[i];
            a[i] = temp;
        }
        if (max != i) {
            int temp = a[max];
            a[max] = a[length - i - 1];
            a[length - i - 1] = temp;
        } else { //max == i 的情况，此时 a[i] 已经改变了
            int temp = a[min];
            a[min] = a[length - i - 1];
            a[length - i - 1] = temp;
        }
    }
}
```

### 6.堆排序 {#6堆排序}

**思想**：堆排序是利用堆的性质进行的一种选择排序，先将排序元素构建一个最大堆,每次堆中取出最大的元素并调整堆。将该取出的最大元素放到已排好序的序列前面。这种方法相对选择排序，时间复杂度更低，效率更高。**时间复杂度**：最坏:O\(nlog2n\) 最好: O\(nlog2n\) 平均: O\(nlog2n\)**空间复杂度**：O\(1\)**稳定性**：不稳定 例如 5 10 15 10。 如果堆顶5先输出，则第三层的10\(最后一个10\)的跑到堆顶，然后堆稳定，继续输出堆顶，则刚才那个10跑到前面了，所以两个10排序前后的次序发生改变。**代码**：

```
public void HeapSort(int[] a) {
	int length = a.length;
	//构建初始堆
	//最后一个非叶子结点是(length-1)/2
	for (int i = (length - 1) / 2; i >= 0; i--) {
		AdjustHeap(a, i, length);
	}
	//从最后一个序列开始调整
	for (int i = length - 1 ; i > 0; i--){
		//将堆顶最大元素和堆中最后一个元素
		int temp = a[0];
		a[0] = a[i];
		a[i] = temp;
			
		AdjustHeap(a, 0, i);
	}
}
	
//key为待调整的元素的位置
private void AdjustHeap(int[] a, int key, int length) {
	//左孩子 
	int child = 2 * key + 1; 
	while (child < length) {
		//找左右孩子中最大的
		if (child + 1 < length && a[child] < a[child + 1]) {
			child++;
		 }
		//如果较大的孩子大于父节点，交换，重新设置key，即下一个需要调整的位置
		if (a[child] > a[key]) {
			int temp = a[key];
			a[key] = a[child];
			a[child] = temp;
			key = child;
			child = 2 * key + 1;
		} else {
			break;
		}	 
	}
}
```

## 7.归并排序 {#7归并排序}

**思想**：归并排序采用了分治算法，首先递归将原始数组划分为若干子数组，对每个子数组进行排序。然后将排好序的子数组递归合并成一个有序的数组。**时间复杂度**：最坏:O\(nlog2n\) 最好: O\(nlog2n\) 平均: O\(nlog2n\)**空间复杂度**：O\(n\)**稳定性**：稳定**代码**：

```
public void MergeSort(int[] a, int left, int right) {
    if (left < right) {
        int middle = (left + right)/2;
        MergeSort(a, left, middle);
        MergeSort(a, middle + 1, right);
        merge(left, middle, right, a);
    }
}

private void merge(int left, int middle, int right, int[] a) {
    int[] b = new int[right - left + 1]; 
    int k = 0;
    int low = left;
    int rig = middle + 1;
    while (low <= middle && rig <= right) {
        if (a[low] < a[rig]) {
            b[k++] = a[low++];
        } else {
            b[k++] = a[rig++];
        }
    }

    //将剩余的加入数组
    while (low <= middle) {
        b[k++] = a[low++];
    }

    while (rig <= right) {
        b[k++] = a[rig++];
    }

    //将临时数组b的数据存回a
    k = 0;
    while (left <= right) {
        a[left++] = b[k++];
    }
}
```

## 8.基数排序算法 {#8基数排序算法}

**思想**：基数排序是通过“分配”和“收集”过程来实现排序，首先根据数字的个位的数将数字放入0-9号桶中，然后将所有桶中所盛数据按照桶号由小到大，桶中由顶至底依次重新收集串起来，得到新的元素序列。然后递归对十位、百位这些高位采用同样的方式分配收集，直到没各位都完成分配收集得到一个有序的元素序列。**时间复杂度**：最坏:O\(d\(r+n\)\) 最好:O\(d\(r+n\)\) 平均: O\(d\(r+n\)\)**空间复杂度**：O\(dr+n\) n个记录，d个关键码，关键码的取值范围为r**稳定性**：稳定 基数排序基于分别排序，分别收集，所以其是稳定的排序算法。**代码**：

