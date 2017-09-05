---
title: 快速排序 & 归并排序小记
date: 2017-08-15 15:54:29
tags: 
    - sort
    - leetcode
categories: 技术
---

排序是在学习算法和编程中最基础的一部分，而快速排序和归并排序又是这其中使用最普遍的两种排序方式，具体两种排序的实现原理网上有大量的讲解，这里只记录一些具体的代码实现，方便自己忘记的时候进行查阅。

<!--more-->

### 快速排序(Quick Sort)

**时间复杂度:** 平均: O(N___log___N)  最优: O(N)  最差: O(N^2)
**空间复杂度:** O(logN) (因为递归调用，所以是O(logN)不是O(1))

* 方法一:  

```java
public void quickSort(int[] nums, int left, int right) {
    int i = left, j = right, key = nums[right];
    while(i < j) {
        while(i < j && nums[i] < key)
            i++;

        nums[j] = nums[i];

        while(i < j && nums[j] >= key)
            j--;

        nums[i] = nums[j];
    }
    nums[i] = key;

    if(i - 1 > left)
        quickSort(nums, left, i - 1);

    if(i + 1 < right)
        quickSort(nums, i + 1, right);
}
```

* 方法二:

```java
public void quickSort(int[] nums, int left, int right) {
    int i = left, j = right, mid = (right - left) / 2 + left;
    int key = nums[mid];
    while(i <= j) {
        while(nums[i] < key)
            i++;
    		
        while(nums[j] > key)
            j--;

    	if(i <= j) {
            int temp = nums[i];
            nums[i] = nums[j];
            nums[j] = temp;
            i++;
            j--;
        }
    }
    if(j > left)
        quickSort(nums, left, j);
    
    if(i < right)
        quickSort(nums, i, right);
	
}
```

注意，在方法二中，从right往左遍历的时候，不能考虑 `nums[j] == key` 的情况，否则有可能会出现数组左边都是大于 `key` 右边都是等于 `key` 的情况，这时排序就无法继续进行。  

### 归并排序(Merge Sort)

**时间复杂度:** 平均: O(NlogN)  最优: O(NlogN)  最差: O(NlogN)
**空间复杂度:** O(N)

```java
private void mergeSort(int[] nums, int left, int right) {
    if(left >= right) return;

    int mid = (right - left) / 2 + left;
    mergeSort(nums, left, mid);
    mergeSort(nums, mid + 1, right);

    mergeArray(nums, left, mid, right);
}

private void mergeArray(int[] nums, int start, int mid, int end) {
    int i = start, j = mid + 1, index = 0;
    int[] temp = new int[end - start + 1];
    while(i <= mid && j <= end) {
        if(nums[i] < nums[j])
            temp[index++] = nums[i++];
        else
            temp[index++] = nums[j++];
    }

    while(i <= mid)
        temp[index++] = nums[i++];

    while(j <= end)
        temp[index++] = nums[j++];

    for(int x = 0; x < index; x++)
        nums[x + start] = temp[x];
}
```