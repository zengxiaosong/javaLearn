# 数据结构分析

## 一、排序

排序方法有很多种，这里只是介绍几种常用的排序方法如：冒泡排序

### 1、冒泡排序（升序）

实现原理： 1、每次循环对前后两个数进行大小比较，然后对数值进行交换，全部值比较后最大的数字就会到最后去。

​					 2、对前面的n-1个数字按照 1 中进行比较，得出最大值，

​					 3、循环进行比较，直到比较前面2个数进行比较，到这里就比较了n-1次，每次的比较的数字也从n到2，

按照上面的这种方式就能得出冒泡排序的实现方法了。

**数组实现冒泡排序**：

```java
public static void main(String[] args) {
    int [] nums = {98,25,89,14,56,84,32,12};
    //设计排序的方法，升序设计,对数组的地址值进行改变，不用返回值
    sortArrays(nums);
    System.out.println(Arrays.toString(nums));
}
private static void sortArrays(int[] nums) {
    //排序进行比较的次数为n-1次
    for (int i = 0; i <nums.length-1 ; i++) {
        //比较的数从最后到前面依次减少
        for (int j = 0; j <nums.length-1-i; j++) {
            //前后两个数进行比较
            if (nums[j]>nums[j+1]){
                int thrid = nums[j];
                nums[j] = nums[j+1];
                nums[j+1] = thrid;
            }
        }
    }
}
```

> 按照上面的算法，排序要经过（n-1）+(n-2) +  ....+2+1 = n(n-1)/2 ,忽略量级的话，那么`时间复杂度也就是 O(n^2)`

### 2、选择排序（升序）

实现原理：   

				*  1、将剩余元素中最小的元素与首元素进行交换
				*  首位元素下标+1，

经过n-1次的选择比较就能得到排序的结果了

**使用数组实现过程**：

```java
public static void main(String[] args) {
        int [] nums = {98,25,89,14,56,84,32,12,420};
        //设计排序的方法，升序设计,对数组的地址值进行改变，不用返回值
        sortArrays(nums);
        System.out.println(Arrays.toString(nums));
    }
    private static void sortArrays(int[] nums) {
        //使用选择排序的方法设计实现
        for (int i = 0; i <nums.length-1 ; i++) {
            //标记首位元素下标
            int min = i;
            //查找出剩余元素中最小的值的下标
            for (int j = min+1; j <nums.length ; j++) {
                //如果后面的元素小于首元素，就将最小值的下标进行替换
                //这里仅仅是获得了每次比较最小值的下标，因此比较也是同下标得出来的值进行比较
                if(nums[j]<nums[min]){
                    min=j;
                }
            }
            //找到最小的元素下标后,将最小元素放在首位
            if(i!=min){
                int thrid = nums[i];
                nums[i] = nums[min];
                nums[min] = thrid;
            }
        }
}
```

> 同上面一样，时间复杂度同样是（n-1）+(n-2) +  ....+2+1 = n(n-1)/2 ,忽略量级的话，那么`时间复杂度也就是O(n^2)`

## 二、查找算法

### 1、二分法查找

实现原理：（**实现前提是一组已经排好顺序的数字**）

			* 首先定位首位，末尾，中间位
			* 查看中间位是否等于要查找的数字，等于就退出，否则就判断查找数在中间位的左边还是右边，
			* 左边，将末尾赋值为原中间位，重新计算中间位
			* 右边就将首位定义为中间位，重新计算中间位，
			* 循环下去，直到条件不符合为止。

**利用数组的实现**：

```
public static void main(String[] args) {
    int [] nums = {1,4,8,12,16,52,88,96,105,223};
    //对已经排好序的数字进行二分查找
    System.out.println(QueryNumber(nums,223));

}
public static int QueryNumber(int[] nums, int num){
    int fromNumIndex = 0;
    int toNumIndex = nums.length-1;
    int middleIndex = (fromNumIndex + toNumIndex)/2;
    while(fromNumIndex<=toNumIndex){
        if(num==nums[middleIndex]){
            return middleIndex;
        }else if (nums[middleIndex]<num){
            //也就是在右边的情况下
            fromNumIndex = middleIndex+1;
            middleIndex = (fromNumIndex + toNumIndex)/2;
        }else if(nums[middleIndex]>num){
            //也就是在左边的情况下
            toNumIndex = middleIndex-1;
            middleIndex = (fromNumIndex + toNumIndex)/2;
        }
    }
    //如果没找到就返回-1
    return -1;
}
```