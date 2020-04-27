# 二分查找

## <font color = blue>内容概要</font>

1. 

   


## <font color = blue> 遗留问题</font>

1. 



### 二分查找变形问题（Binary Search）

+ 二分查找的变形问题

  ![](2.jpg)



### 变体一：查找第一个值等于给定值的元素

+ 假设有序数据集合中存在重复的数据，希望找到第一个值等于给定值的数据。

  + 简洁形式

    ```java
    public int bsearch(int[] a, int n, int value) {
      int low = 0;
      int high = n - 1;
      while (low <= high) {
        int mid = low + ((high - low) >> 1);
        if (a[mid] >= value) {
          high = mid - 1;
        } else {
          low = mid + 1;
        }
      }
    
      if (low < n && a[low]==value) return low;
      else return -1;
    }
    ```

  + 另一种实现方法

    ```java
    public int bsearch(int[] a, int n, int value) {
      int low = 0;
      int high = n - 1;
      while (low <= high) {
        int mid =  low + ((high - low) >> 1);
        if (a[mid] > value) {
          high = mid - 1;
        } else if (a[mid] < value) {
          low = mid + 1;
        } else {
          if ((mid == 0) || (a[mid - 1] != value)) return mid;
          else high = mid - 1;
        }
      }
      return -1;
    }
    ```

    + a[mid]跟要查找的 value 的大小关系有三种情况：大于、小于、等于。对于 a[mid]>value 的情况，需要更新 high= mid-1；对于 a[mid]<value 的情况，需要更新 low=mid+1。
    + 那当 a[mid]=value 的时候应该如何处理呢？如果我们查找的是任意一个值等于给定值的元素，当 a[mid]等于要查找的值时，a[mid]就是我们要找的元素。
    + 但是，如果我们求解的是第一个值等于给定值的元素，当 a[mid]等于要查找的值时，就需要确认一下这个 a[mid]是不是第一个值等于给定值的元素。
    + 重点看第 11 行代码。如果 mid 等于 0，那这个元素已经是数组的第一个元素，那它肯定是我们要找的；如果 mid 不等于 0，但 a[mid]的前一个元素 a[mid-1]不等于 value，那也说明 a[mid]就是我们要找的第一个值等于给定值的元素。
    + 如果经过检查之后发现 a[mid]前面的一个元素 a[mid-1]也等于 value，那说明此时的 a[mid]肯定不是我们要查找的第一个值等于给定值的元素。那就更新 high=mid-1，因为要找的元素肯定出现在[low, mid-1]之间。



### 变体二：查找最后一个值等于给定值的元素

+ 代码示意

  ```java
  public int bsearch(int[] a, int n, int value) {
    int low = 0;
    int high = n - 1;
    while (low <= high) {
      int mid =  low + ((high - low) >> 1);
      if (a[mid] > value) {
        high = mid - 1;
      } else if (a[mid] < value) {
        low = mid + 1;
      } else {
        if ((mid == n - 1) || (a[mid + 1] != value)) return mid;
        else low = mid + 1;
      }
    }
    return -1;
  }
  ```

  + 重点看第 11 行代码。如果 a[mid]这个元素已经是数组中的最后一个元素了，那它肯定是我们要找的；
  + 如果 a[mid]的后一个元素 a[mid+1]不等于 value，那也说明 a[mid]就是我们要找的最后一个值等于给定值的元素。如果我们经过检查之后，发现 a[mid]后面的一个元素 a[mid+1]也等于 value，那说明当前的这个 a[mid]并不是最后一个值等于给定值的元素。就更新 low=mid+1，因为要找的元素肯定出现在[mid+1, high]之间。



### 变体三：查找第一个大于等于给定值的元素

+ 查找第一个大于等于给定值的元素。比如，数组中存储的这样一个序列：3，4，6，7，10。如果查找第一个大于等于 5 的元素，那就是 6。

+ 代码示意

  ```java
  public int bsearch(int[] a, int n, int value) {
    int low = 0;
    int high = n - 1;
    while (low <= high) {
      int mid =  low + ((high - low) >> 1);
      if (a[mid] >= value) {
        if ((mid == 0) || (a[mid - 1] < value)) return mid;
        else high = mid - 1;
      } else {
        low = mid + 1;
      }
    }
    return -1;
  }
  ```

  + 如果 a[mid]小于要查找的值 value，那要查找的值肯定在[mid+1, high]之间，所以，更新 low=mid+1。
  + 对于 a[mid]大于等于给定值 value 的情况，要先看下这个 a[mid]是不是我们要找的第一个值大于等于给定值的元素。如果 a[mid]前面已经没有元素，或者前面一个元素小于要查找的值 value，那 a[mid]就是我们要找的元素。这段逻辑对应的代码是第 7 行。
  + 如果 a[mid-1]也大于等于要查找的值 value，那说明要查找的元素在[low, mid-1]之间，所以，我们将 high 更新为 mid-1。



### 变体四：查找最后一个小于等于给定值的元素

+ 查找最后一个小于等于给定值的元素。比如，数组中存储了这样一组数据：3，5，6，8，9，10。最后一个小于等于 7 的元素就是 6

+ 代码示意

  ```java
  public int bsearch7(int[] a, int n, int value) {
    int low = 0;
    int high = n - 1;
    while (low <= high) {
      int mid =  low + ((high - low) >> 1);
      if (a[mid] > value) {
        high = mid - 1;
      } else {
        if ((mid == n - 1) || (a[mid + 1] > value)) return mid;
        else low = mid + 1;
      }
    }
    return -1;
  }
  ```

  















