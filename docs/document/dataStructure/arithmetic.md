# 算法相关习题

## 1、求数组中随机生成的字母的个数

给出一个数组长度为100，生成随机的a-z,

解题步骤如下：

```java
public static void getWordNum() {
    //生成随机的数组
    char[] nums = new char[100];
    for (int i=0; i<nums.length; i++) {
        int random = (int)(Math.random()*26);
        nums[i] = (char) (random + 'a');
    }
    System.out.println(Arrays.toString(nums));
    //对生成的字母进行处理
    //新建一个26位存储位置的字母来进行存储个数
    int[] charNums = new int[26];
    for (char num : nums) {
        charNums[num-'a'] +=1;
    }
    //输出元素个数：
    for (int i=0; i<charNums.length; i++){
        System.out.println("字母"+(char)(i+'a')+"的个数为:"+charNums[i]);
    }
```

## 2、获取数组中的最大值/最小值

```java
public static void main(String[] args) {
    int [] nums = {98,25,89,14,56,84,32,12};
    getMinNum(nums);
}

private static void getMinNum(int[] nums) {
    //标记初始的最小下标为
    int min=0;
    for (int i = 1; i <nums.length ; i++) {
        if(nums[i]<nums[min]){
            min =i;
        }
    }
    System.out.println("最小值为："+nums[min]);
}
```