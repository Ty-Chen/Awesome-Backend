## 排序算法

### 一、分类与性能总结

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/1.webp)

### 二、详细算法介绍

#### 1 直接插入排序

##### 1.1 算法思想

直接插入排序的核心思想就是：**将数组中的所有元素依次跟前面已经排好的元素相比较，如果选择的元素比已排序的元素小，则交换，直到全部元素都比较过。**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/2.webp)


 因此，从上面的描述中我们可以发现，直接插入排序可以用两个循环完成：



1. 第一层循环：遍历待比较的所有数组元素
2. 第二层循环：将本轮选择的元素(selected)与已经排好序的元素(ordered)相比较。如果：selected > ordered，那么将二者交换。

##### 1.2 代码实现



```python
# 1. 直接插入排序
def insert_sort(L):
    # 遍历数组中的所有元素，其中0号索引元素默认已排序，因此从1开始
    for i in range(1,len(L)):
        # range(x-1,-1,-1):从x-1倒序循环到0
        for j in range(i-1,-1,-1):
            # 将该元素与已排序好的前序数组依次比较，如果该元素小，则交换
            if L[j]>L[j+1]:
                L[j], L[j+1] = L[j+1], L[j]
    return L
```

#### 2 希尔排序

##### 2.1 算法思想

希尔排序的核心思想就是：**将待排序数组按照步长gap进行分组，然后将每组的元素利用直接插入排序的方法进行排序；每次将gap折半减小，循环上述操作；当gap=1时，利用直接插入，完成排序。**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/3.webp)


 因此，从上面的描述中我们可以发现，希尔排序可以用三个循环完成：



1. 第一层循环：将gap依次折半，对序列进行分组，直到gap=1
2. 第二、三层循环：也即直接插入排序所需要的两次循环。具体描述见上。

##### 2.2 代码实现



```python
# 2. 希尔排序
def shell_sort(L):
    # 初始化gap值，此处利用序列长度的一半为其赋值
    gap = len(L) // 2
    # 第一层循环：依次改变gap值对列表进行分组
    while(gap >= 1):
        # 下面：利用直接插入排序的思想对分组数据进行排序，注意分组后下标与gap有关
        for i in range(gap,len(L)):
            for j in range(i-gap,-1,-gap):
                if L[j]>L[j+1]:
                    L[j], L[j + 1] = L[j + 1], L[j]
        # while循环条件折半
        gap = gap // 2
    return L
```

#### 3 简单选择排序

##### 3.1 算法思想

简单选择排序的核心思想就是：**比较+交换**。
 **1. 从待排序序列中，找到关键字最小的元素；**
 **2. 如果最小元素不是待排序序列的第一个元素，将其和第一个元素互换；**
 **3. 从余下的 N - 1 个元素中，找出关键字最小的元素，重复(1)、(2)步，直到排序结束。**
 因此，从上面的描述中我们可以发现，简单选择排序可以用两个循环完成：

1. 第一层循环：依次遍历序列当中的每一个元素
2. 第二层循环：将遍历得到的当前元素依次与余下的元素进行比较，符合最小元素的条件，则交换。

##### 3.2 代码实现



```python
# 3. 简单选择排序
def select_sort(L):
    # 依次遍历序列中的每一个元素
    for i in range(0,len(L)):
        # 将当前位置的元素定义此轮循环当中的最小值
        min = L[i]
        # 将该元素与剩下的元素依次比较寻找最小元素
        for j in range(i+1,len(L)):
            if L[j] < min:
                L[j], min = min, L[j]
        # 将比较后得到的真正的最小值赋值给当前位置
        L[i] = min
    return L
```

#### 4 堆排序

##### 4.1 算法思想

**堆**：本质是一种数组对象。特别重要的一点性质：**任意的叶子节点小于（或大于）它所有的父节点**。对此，又分为大顶堆和小顶堆，大顶堆要求节点的元素都要大于其孩子，小顶堆要求节点元素都小于其左右孩子，两者对左右孩子的大小关系不做任何要求。

利用堆排序，就是基于大顶堆或者小顶堆的一种排序方法。下面，我们通过大顶堆来实现。

堆排序的核心思想就是：
 **1.首先将序列构建称为大顶堆；**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/4.webp)

这样满足了大顶堆那条性质：位于根节点的元素一定是当前序列的最大值


**2. 取出当前大顶堆的根节点，将其与序列末尾元素进行交换；**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/5.webp)

此时序列末尾的元素为已排序的最大值；由于交换了元素，当前位于根节点的堆并不一定满足大顶堆的性质


**3. 对交换后的n-1个序列元素进行调整，使其满足大顶堆的性质**
**4. 重复2.3步骤，直至堆中只有1个元素为止**



##### 4.2 代码实现



```python
# 4. 堆排序
def heap_adjust(L, start, end):
# start为当前需要调整最大堆的位置，end为调整边界
    root = start
    # 执行循环操作：两个任务：1 寻找最大值的下标；2.最大值与父节点交换
    while True:
        child = root * 2 + 1
        if child > end:
            break
        # 取较大的子节点
        if child + 1 <= end and L[child+1] > L[child]:
            child += 1
        # 较大的子节点成为父节点
        if L[child] > L[root]:
            L[child], L[root] = L[root], L[child]
            root = child
        else:
            break

def heap_sort(L):
    # 先建立大顶堆，保证最大值位于根节点；并且父节点的值大于叶子结点，从最后一个父节点开始逆序向前循环
    for start in range(len(L)//2-1, -1, -1):
        heap_adjust(L, start, len(L)-1)
    # 执行循环：1.每次取出堆顶元素置于序列的最后    2.调整堆，使其继续满足大顶堆的性质
    for end in range(len(L)-1, 0, -1):
        L[0], L[end] = L[end], L[0]
        heap_adjust(L, 0, end-1)
    return L
```

#### 5 冒泡排序

##### 5.1 算法思想

冒泡排序的思路比较简单，其核心思想为：
 **1. 将序列当中的左右元素，依次比较，保证右边的元素始终大于左边的元素；**
 **（ 第一轮结束后，序列最后一个元素一定是当前序列的最大值；）**
 **2. 对序列当中剩下的n-1个元素再次执行步骤1。**
 **3. 对于长度为n的序列，一共需要执行n-1轮比较**
 **（利用while循环可以减少执行次数）**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/6.webp)



##### 5.2 代码实现



```python
# 5. 冒泡排序
def bubble_sort(L):
    # 序列长度为length，需要执行length-1轮交换
    for i in range(1,len(L)):
        # 对于每一轮交换，都将序列当中的左右元素进行比较
        # 每轮交换当中，由于序列最后的元素一定是最大的，因此每轮循环到序列未排序的位置即可
        for j in range(0, len(L)-i):
            if L[j]>L[j+1]:
                L[j], L[j+1] = L[j+1], L[j]
    return L
```

#### 6 快速排序

##### 6.1 算法思想

快速排序的核心思想为：**挖坑填数+分治法**
 **1. 从序列当中选择一个基准数(pivot)**
 **（ 在这里我们选择序列当中第一个数最为基准数）**
 **2. 将序列当中的所有数依次遍历，比基准数大的位于其右侧，比基准数小的位于其左侧。**
 **3. 重复步骤1.2，直到所有子集当中只有一个元素为止。**

![img](https://github.com/Ty-Chen/Awesome-Backend/blob/master/7.webp)


 上述过程可用伪代码表示如下：
**1. i =L; j = R; 将基准数挖出形成第一个坑a[i]。**
**2. j--由后向前找比它小的数，找到后挖出此数填前一个坑a[i]中。**
**3. i++由前向后找比它大的数，找到后也挖出此数填到前一个坑a[j]中。**
**4. 再重复执行2，3二步，直到i==j，将基准数填入a[i]中**



##### 6.2 代码实现

```python
# 6. 快速排序
def quick_sort(L, start, end):
# L：待排序的序列；start排序的开始index,end序列末尾的index
# 对于长度为length的序列：start = 0;end = length-1
    if start < end :
        i, j, pivot = start, end, L[start]
        while i < j :
            # 从右开始向左寻找第一个小于pivot的值
            while L[j] > pivot and i < j :
                j -= 1
            # 从左开始向右寻找第一个大于pivot的值
            while L[i] < pivot and i < j :
                i += 1
            # 交换两者的位置
            L[i], L[j] = L[j], L[i]
        # 循环结束后，说明 i=j，此时左边的值全都小于pivot,右边的值全都大于pivot
        # pivot的位置移动正确，那么此时只需对左右两侧的序列调用此函数进一步排序即可
        # 递归调用函数：依次对左侧序列：从0 ~ i-1//右侧序列：从i+1 ~ end
        # 左侧序列继续排序
        quick_sort(L, start, i-1)
        # 右侧序列继续排序
        quick_sort(L, i+1, end)
    return L
```



-----

