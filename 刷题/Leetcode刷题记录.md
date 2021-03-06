[toc]

# 模板

**题目**

**重点**

**思路**

**代码**

**注意**

# 数组

## 0027 移除元素

**题目：移除元素**

**重点**：元素的顺序可以改变

**思路**

- 快慢指针

  - 当前几个元素不等于val时，会出现原地移动的问题。

- 快慢指针优化

  > 如果左指针 *left* 指向的元素等于*val*，此时将右指针 *right* 指向的元素复制到左指针*left* 的位置，然后右指针*right* 左移一位。如果赋值过来的元素恰好也等于*val*，可以继续把右指针right 指向的元素的值赋值过来（左指针*left* 指向的等于*val* 的元素的位置继续被覆盖），直到左指针指向的元素的值不等于*val* 为止。
  >
  > 当左指针*left* 和右指针*right* 重合的时候，左右指针遍历完数组中所有的元素。
  >
  > 这样的方法两个指针在最坏的情况下合起来只遍历了数组一次。与方法一不同的是，方法二避免了需要保留的元素的重复赋值操作。
  >
  > 作者：LeetCode-Solution
  > 链接：https://leetcode-cn.com/problems/remove-element/solution/yi-chu-yuan-su-by-leetcode-solution-svxi/

**代码**

```java
class Solution {
    public int removeElement(int[] nums, int val) {
        int fast = nums.length-1;
        int slow = 0;
        while(fast >= slow) {
            if(nums[slow] == val) {
                nums[slow] = nums[fast];
                fast--;
            }else {
                slow++;
            }
        }
        return fast+1;
    }
}
```

**注意**

- 要考虑到题目叙述中不太合理的部分
- 测试时注意一些边界条件，本题中注意到数组中只有一个元素，或者数组中多个元素相同并且值等于val。

## 0035 搜索插入位置

**题目**：[搜索插入位置](https://leetcode-cn.com/problems/search-insert-position/)

**重点**

二分查找

**思路**

通过考虑正常情况写出大致代码，思考特殊情况，写出代码的边界条件。

**代码**

```java
public static int searchInsert(int[] nums, int target) {
        if(target > nums[nums.length-1]) {
            return nums.length;
        }
        if(target <= nums[0]) {
            return 0;
        }
        int first = 0;
        int last = nums.length-1;
        int mid;
        while(first <= last) {
            mid = (first + last) / 2;
            if(nums[mid] == target){
                return mid;
            }else if(nums[mid] < target) {
                first = mid+1;
            }else {
                last = mid-1;
            }
        }
        return last+1;
    }
```



**注意**

注意特殊情况，只有一个值，两个值，三个值。

## 0066 加一

**题目**：[ 加一](https://leetcode-cn.com/problems/plus-one/)

**重点**

- 重点在于全是9的情况下，最高位+1之后，因为进位会出现新的位数。
- 我是通过flag来判断有没有进位。
- 但是思考一下，出现多余的一位一定是因为999...9这种情况，因为结果一定是1000...0这种结果。

**思路**

- 可以直接单独判断这种情况。

  ```java
  digits= new int[digits.length + 1];
  digits[0] = 1;
  return digits;
  ```

  因为Java数组初始化后，默认每一位为0。

**代码**

```java
class Solution {
    public int[] plusOne(int[] digits) {
        for (int i = digits.length - 1; i >= 0; i--) {
            digits[i]++;
            digits[i] = digits[i] % 10;
            if (digits[i] != 0) return digits;
        }
        digits = new int[digits.length + 1];
        digits[0] = 1;
        return digits;
    }
}
```

**注意**

这种题属于按部就班题，只要是按照题目的叙述就可以做出来，但是也要去思考，如何能够将一些情况适当的转化为更加简单的情况。
