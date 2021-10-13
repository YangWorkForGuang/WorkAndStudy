[TOC]

# 前言

开始时间：2021年5月18日

```
struct ListNode* ReverseList(struct ListNode* pHead ) {
    // write code here
    if(pHead == nullptr) {
        return pHead;
    }
    ListNode *pre = nullptr;
    ListNode *p = nullptr;
    while(pHead != nullptr) {
        p = pHead->next;
        pHead->next = pre;
        pre = pHead;
        pHead = p;
    }
    return pHead;
}
```



# 剑指offer

## JZ001二维数组中的查找

[题目链接](https://www.nowcoder.com/ta/coding-interviews)

**题目描述**

```
在一个二维数组中（每个一维数组的长度相同），每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。
[
  [1,2,8,9],
  [2,4,9,12],
  [4,7,10,13],
  [6,8,11,15]
]
给定 target = 7，返回 true。
给定 target = 3，返回 false。
```

**解题思路**

1. 遍历+跳跃

> （DIY）从最右下的元素开始向左开始遍历二维数组。如果元素大于target，一直遍历，直到到达数组最左端，当移动到最左端时，仍然大于target，则移动指针到上一行最后一个元素；如果元素小于target，则将指针移动到`i-1,j+1`位置，该位置元素与原位置元素不确定大小关系，但是均大于`i-1,j不变`位置的元素，从此位置开始重复以上的步骤。
>
> 代码如1所示
>
> 问题是：
>
> 1. 在极端的情况下，算法的时间复杂度为O(n^2)。
> 2. 由于指针斜着跳跃，需要对边界进行判断，增加了代码的复杂性。

2. 二分查找

> （常见思路）既然题目中提到了查找就应该考虑二分查找。在一个有序数组中查找就应该考虑二分查找。
> （思考）第一次考虑二分查找时以为要进行双重二分查找，脑子里面一下子非常的乱，不知道如何进行查找。但是实际上只需要逐行进行二分查找，时间复杂度O(nlogn)

3. 题目本身

   > （特殊思路）选取左下角或右上角的元素，即为a。加入选取左下角元素，当target大于1时，让元素往右走；当元素小于a时，让元素向上走。
   >
   > 为什么选取右上角或者左下角的元素？？两个元素都处于一种极端的情况，在所在列最大/最小，在所在行最大/最小。如果从左上角开始找时，向下向右开始找都是递增，会出现岔路。

**编码问题**

>数组为空的情况没有考虑到，同时判断数组为空的代码：`if(array==null||array.length==0||(array.length==1&&array[0].length==0)) return false;`仅仅写`if(array==null)return false;`会出现数组越界的错误。

**代码1**

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array==null||array.length==0||(array.length==1&&array[0].length==0))
            return false;
        int m = array.length;
        int n = array[0].length;
        int x = m-1;
        int y = n-1;
        if (array[m-1][n-1] < target) {
            return false;
        }
        while(true){
            if(array[x][y] == target && x<m && y<n){
                return true;
            }else if(array[x][y] < target && x<m && y<n){
                x = x-1;
                y = y+1;
                if(x<0 || y>=n)
                    return false;
            }else{
                if (y>0)
                    y = y-1;
                else{
                    x = x-1;
                    y = n-1;
                    if(x < 0)
                        return false;
                }
            }
        }
    }
}
```

**代码2**

```java
public static int binarySearch(int target, int[] array){
        //只是普通二分查找功能的实现，题目的解法就是遍历每一行，在每一行进行二分查找。
        int low = 0;
        int high = array.length-1;
        //二分查找就是不断的缩小查找区间，直到找到目标值或者查找区间为空。
        while(low <= high) {
            int mid = (low + high) / 2;
            if(array[mid] == target){
                return mid;
            }else if(array[mid] > target) {
                high = mid-1;
            }else if(array[mid] < target) {
                low = mid+1;
            }
        }
        return -1;
    }
```

代码3

```java
public class Solution {
    public boolean Find(int target, int [][] array) {
        if(array==null||array.length==0||(array.length==1&&array[0].length==0))
            return false;
        int m = array.length;
        int n = array[0].length;
        if (array[m-1][n-1] < target) {
            return false;
        }
        int x = m-1;
        int y = 0;
        while(x >= 0 && y < n) {
            if(array[x][y] == target){
                return true;
            }else if(array[x][y] < target){
                y++;
            }else if(array[x][y] > target){
                x--;
            }
        }
        return false;
    }
}
```

## JZ002替换空格

有很多的解法，但是个人感觉都没有什么意思。调用小函数+自己的操作与调用大函数相比有什么优越的呢？

```java
public String replaceSpace (String s) {
        // write code here
        if (s == null)
            return s;
        char[] str = s.toCharArray();
        StringBuffer sb = new StringBuffer("");
        for(int i=0;i<str.length;i++){
            if(str[i] != ' '){
                sb.append(str[i]);
            }else{
                sb.append("%20");
            }
        }
        return sb.toString();
    }
```

## JZ003从尾到头打印链表

**解题思路**

1. 保留遍历结果

   > 无论是利用数组还是栈来保存中间结果，这些方法都太过于简易，没有什么意义。

2. 递归

   > 当你想到能够用栈的时候，也一定要想到能够用递归。两个本质上是一样的。
   
3. 增加一个辅助节点（详情见JZ015）

**代码（递归）**

```java
import java.util.ArrayList;
public class Solution {
    ArrayList<Integer> list = new ArrayList<Integer>();
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        // 目前的思路就是先遍历一遍链表，用数组或者栈保存数组，然后重新建立链表。
        // 能不能只遍历一次链表？
        if(listNode != null) {
            printListFromTailToHead(listNode.next);
            list.add(listNode.val);
        }
        return list;
    }
}
```

这段代码有很多值得学习的地方：

- 将list定义在函数之外
- 递归很多次，但是只有一次需要返回list，即当`listNode == null`

## JZ004重建二叉树+

> 面试遇到了中序遍历的非递归做法，但是思路是一点也想不起来。

**思路**

（有很多次都是有个大致思路，与答案也是非常的接近，但是最终就是想不清楚，就是想不清楚。）

将前序序列的第一个值作为根节点，同时也将该值作为分割点将中序序列分割得到左子树的中序序列和右子树的中序序列以此得到左子树的节点个数L和右子树的节点个数R，将前序序列的除第一个值之外的前L个数作为左子树的前序序列，后R个数作为右子树的前序序列对左子树和右子树重复上述操作直到序列都为null时返回。

（可能真的是写的代码太少了）

**代码**

```java
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if(pre == null || in == null){
            return null;
        }
        TreeNode node = function(pre, 0, pre.length-1, in, 0, in.length-1);
        return node;
    }
    public TreeNode function(int[] pre, int leftPre, int rightPre, int[] in, int leftIn, int rightIn) {
        if (leftPre > rightPre || leftIn > rightIn) {
            return null;
        }
        TreeNode head = new TreeNode(pre[leftPre]);
        for(int i=leftIn; i<= rightIn; i++) {
            if(pre[leftPre] == in[i]) {
                head.left = function(pre, leftPre+1, leftPre+i-leftIn, in, leftIn, i-1);
                head.right = function(pre, leftPre+i-leftIn+1, rightPre, in, i+1, rightIn);
                break;  // 不必进行下面的循环了。
            }
        }
        return head;
    }
}
```



### **前序遍历**

<img src="https://img-blog.csdnimg.cn/20181120205151339.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3UwMTI1MzUxMzI=,size_16,color_FFFFFF,t_70" style="zoom:67%;" />

用栈，先将右孩子压入栈，再将左孩子压入栈。（之前思考的时候考虑到栈，但是仅仅考虑左孩子先入右孩子后入的场景了，只能说思路还是要开阔一点。）

```java
public static void preTraversal2(TreeNode root) {
        // 第一次没有想到用栈，因为居然没有考虑到右孩子先入栈的情况。思维已经这么固化了吗？
        if(root == null)
            return ;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode temp ;
        stack.push(root);
        while(!stack.empty()) {
            temp = stack.pop();
            preorderList2.add(temp.val);
            if (temp.right != null) {
                stack.push(temp.right);
            }
            if (temp.left != null) {
                stack.push(temp.left);
            }
        }
    }
```



### **中序遍历**（未理解）

[参考链接](https://blog.csdn.net/u012535132/article/details/84329729)

思路：为什么要选取辅助节点P？当一个节点没有右孩子的时候，要标记处该节点已经访问过了。当p=null时，相当于结束了一次循环。

1. 只要有左孩子就压入栈中；
2. 将栈顶元素进行出栈操作，并且访问该出栈元素q
   1. 当q没有右孩子时，将辅助节点P设置为null。进行下一次循环（即跳过1步骤，直接从栈中取出元素）。
   2. 当q有右孩子时，将右孩子压入栈，并使`p = q.right`，再重复（2）操作；
3. 直到栈中元素全部出栈。

```java
public static void inTraversal2(TreeNode root) {
        if(root == null) {
            return ;
        }
        Stack<TreeNode> s = new Stack<TreeNode>();
        s.push(root);
        TreeNode temp = root;
        while(!s.empty()) {
            // temp = s.pop();
            // left baby
            if(temp != null && temp.left != null) {
                s.push(temp.left);
                temp = temp.left;
            }else{
                temp = s.pop();
                inorderList2.add(temp.val);
                if(temp != null && temp.right != null) {
                    s.push(temp.right);
                    temp = temp.right;
                }else{
                    temp = null;
                }
            }
        }
    }
```



### 后序遍历---逆前序遍历

首先看下后序遍历的定义：若二叉树非空，则依次执行如下操作： (1)后序遍历左子树；(2)后序遍历右子树；(3)访问根结点。

和前序遍历的定义对比一下：若二叉树非空，则依次执行如下操作：(1) 访问根结点；(2) 先序遍历左子树；(3) 先序遍历右子树。

是否有什么发现？是的，如果把前序遍历的第2步和第3步互换一下，则正好的后序遍历的步骤完全相反！方便起见，我把它命名为逆后序遍历，它的定义：*\*若二叉树非空，则依次执行如下操作：(1) 访问根结点；(2) 逆后序遍历右子树；(3) 逆后序遍历左子树。\**

其数学原理不得而知，但是完全可以以此为基础实现后续遍历。

```java
public static void postTraversal2(TreeNode root) {
        // 通过逆前序遍历的方式得到了后续遍历的结果。
        if(root == null)
            return ;
        Stack<TreeNode> stack = new Stack<TreeNode>();
        TreeNode temp ;
        stack.push(root);
        while(!stack.empty()) {
            temp = stack.pop();
            postorderList2.add(temp.val);
            if (temp.left != null) {
                stack.push(temp.left);
            }
            if (temp.right != null) {
                stack.push(temp.right);
            }
        }
        Collections.reverse(postorderList2);
    }
```

## JZ005用两个栈实现队列

**思路**

> a，b两个栈，其中a栈只负责压入数据。当弹出数据时，判断b栈是否为空，如果为空，将a栈中全部数据压入到b栈，如果不为空，从b栈顶端弹出数据。

**代码**

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if(stack2.isEmpty()) {
            while(!stack1.isEmpty()) {
                stack2.push(stack1.pop());
            }
        }
        return stack2.pop();
    }
}
```

## JZ006旋转数组的最小数字

**题目**

> 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
> 输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。
> NOTE：给出的所有元素都大于0，若数组大小为0，请返回0。

如何理解这个非递减数组呢？？数组中任何一个数字都要大于等于前面一个数字。

**思路**

1. 从后往前找第一个小于前面数字的数字。

   > 本质上还是遍历整个数组。

2. 二分查找的变种

   > 难点就在于array[mid]与谁进行比较，也就是说target是谁。
   >
   > - 如果有target，那么array[mid]与target进行比较，确定下一个空间
   > - 如果没有target，那么array[mid]与端点进行比较。
   >
   > 接着与左端点还是右端点进行比较，先选择右端点。
   >
   > 1. 情况1，
   >
   >    ```
   >    arr[mid] > target：4 5 6 1 2 3
   >    ```
   >
   >    - arr[mid] 为 6， target为右端点 3， **`arr[mid] > target`, 说明[first ... mid] 都是 >= target** 的，因为原始数组是非递减，所以可以确定答案为 [mid+1...last]区间,所以 `first = mid + 1`
   >
   > 2. 情况2，
   >
   >    ```
   >    arr[mid] < target:5 6 1 2 3 4
   >    ```
   >
   >    - arr[mid] 为 1， target为右端点 4， `arr[mid] < target`, 说明答案肯定不在[mid+1...last]，但是arr[mid] 有可能是答案,所以答案在[first, mid]区间，所以`last = mid`;
   >
   > 3. 情况3，
   >
   >    ```
   >    arr[mid] == target:
   >    ```
   >
   >    - 如果是 1 0 1 1 1， arr[mid] = target = 1, 显然答案在左边
   >    - 如果是 1 1 1 0 1, arr[mid] = target = 1, 显然答案在右边
   >      所以这种情况，不能确定答案在左边还是右边，那么就让last = last - 1;慢慢缩少区间，同时也不会错过答案。
   >
   > 误区：那我们肯定在想，能不能把左端点看成target， 答案是不能。
   >
   > 原因：
   > 情况1 ：1 2 3 4 5 ， arr[mid] = 3. target = 1, arr[mid] > target, 答案在mid 的左侧
   > 情况2 ：3 4 5 1 2 ， arr[mid] = 5, target = 3, arr[mid] > target, 答案却在mid 的右侧
   > 所以不能把左端点当做target

**代码**

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if(array == null || array.length == 0)
            return 0;
        // 二分查找的前提是数组有序
        int left = 0;
        int right = array.length - 1;
        while(left < right) {
            if(array[left] < array[right]) 
                return array[left];  // 只有[left, right]是一个递增子数组的时候，才有可能出现这种情况。
            int mid = left + (right - left) / 2;
            if(array[mid] < array[right]) {
                right = mid;
            }else if(array[mid] > array[right]) {
                left = mid + 1;
            }else{
                right--;
            }
        }
        return array[left];
    }
}
```



## JZ007斐波那契数列

**思考**

1. 解决方法：

   1. 循环（不解释）

   2. 递归

      > 时间复杂度：O(2^n)，缺点慢，会超时。

   3. 记忆化搜索

      > 递归的主要问题是会有大量的重复计算，这里需要用数组或者map对中间结果进行保存。
      >
      > 时间复杂度：O(n)
      >
      > 空间复杂度：O(n) + 递归栈的空间
      >
      > ```java
      > public class Solution {
      >     public int Fibonacci(int n) {
      >         int arr[] = new int[40];
      >         return fib(n, arr);
      >     }
      >     public int fib(int n, int[] arr) {
      >         if(n == 0 || n == 1){
      >             return n;
      >         }
      >         if(arr[n] != 0) {
      >             return arr[n];
      >         }
      >         return arr[n] = fib(n-1, arr) + fib(n-2, arr);
      >     }
      > }
      > ```

   4. 动态规划

      > 通过动态规划来优化掉递归栈的空间。
      >
      > 记忆化搜索是从上往下递归，然后从下往上回溯。动态规划是直接从下往上求得答案。
      >
      > 代码就是直接循环的代码。

   5. 动态规划再次优化

      > 计算`f(5)`时只用到了`f(4) f(3)`，没有用到`f(2) f(1)`，用数组会浪费空间。因此只需要用三个变量。
      >
      > ```java
      > public class Solution {
      >     public int Fibonacci(int n) {
      >         if(n == 0 || n==1) {
      >             return n;
      >         }
      >         int a = 0, b=1, c=0;
      >         for(int i=2;i<=n;i++) {
      >             c = a + b; // b = a + b;
      >             a = b;     // a = b - a;
      >             b = c;
      >         }
      >         return c;      // return b;
      >     }
      > }
      > ```

   6. 动态规划再次优化+

      > 可以优化到用两个变量。
      >
      > <img src="https://uploadfiles.nowcoder.com/images/20190809/1078265_1565356960932_D0096EC6C83575373E3A21D129FF8FEF" style="zoom:50%;" />
      >
      > 例如one指向3，sum指向5，sum = one+sum指向8，one = sum-one指向5。仅用两个数字就保存了中间结果。

2. 难点

   1. 如何解决重复计算问题。

## JZ008跳台阶

**题目**

面试的时候遇到过，至少说出三种方法以及时间复杂度，当时直接懵了。

**思路**

1. 斐波那契数列
2. 记忆化搜索：目的是解决递归中重复计算的问题。
3. 动态规划：目的是优化掉递归栈空间。
4. 动态规划的空间优化，目的是优化掉保存中间结果的数组。

代码1

```java
public class Solution {
    public int jumpFloor(int target) {
        if(target <= 2) {
            return target;
        }
        return jumpFloor(target-1) + jumpFloor(target-2);
    }
}
```

代码2

```java
public class Solution {
    public int jumpFloor(int target) {
        int[] dp = new int[45];  // 
        dp[0] = 0;
        dp[1] = 1;
        dp[2] = 2;
        return solution(target, dp);
    }
    public int solution(int target, int[] dp) {
        if(target > 0 && dp[target] != 0)
            return dp[target];
        dp[target] = solution(target - 1, dp) + solution(target - 2, dp);
        return dp[target];
    }
}
```

**代码3**

```java
public class Solution {
    public int jumpFloor(int target) {
        int[] dp = new int[target+1];  // 
        dp[0] = 1;
        dp[1] = 1;
        // dp[2] = 2;
        for(int i = 2;i<=target;i++) {
            dp[i] = dp[i-1] + dp[i-2];
        }
        return dp[target];
    }
}
```

代码4

```java
public class Solution {
    public int jumpFloor(int target) {
        // 这里有三个变量与两个变量的写法，但是本质上没有什么区别。
        int res = 1;
        int first = 1;
        int last = 1;
        for(int i = 2;i<=target;i++) {
            res = first + last;
            first = last;
            last = res;
        }
        return res;
    }
}
```

## JZ009跳台阶的扩展

题目

> 一只青蛙一次可以跳上1级台阶，也可以跳上2级……它也可以跳上n级。求该青蛙跳上一个n级的台阶总共有多少种跳法。
>
> Input: 3
> Output: 4

思路

1. 自己的思路

   > 既然跳一步就加上fib(n-1)，跳两步就加上fib(n-2)，那么跳3,4，……，n就相应的加上fib(n-3)，fib(n-4)，……，fib(0)。
   >
   > f[n] = f[n-1] + f[n-2] + ... + f[0]

2. 公式继续推导

   >f[n] = f[n-1] + f[n-2] + ... + f[0]
   >
   >f[n-1] = f[n-2] + f[n-3] + ... + f[0]
   >
   >所以一合并，f[n] = 2*f[n-1]，初始条件f[0] = f[1] = 1.
   >
   >此时可以用递归的方法依次求出f[n].
   >
   >但是要注意我们可以根据公式看出f(n)与n的关系。
   >$$
   >f(n) = 2^{n-1}
   >$$
   >

代码

```java
public class Solution {
    public int jumpFloorII(int target) {
        int[] dp = new int[target+1];
        // int[] res = new int[target];
        dp[0] = 1;
        dp[1] = 1;
        for(int i=2;i<=target;i++) {
            for(int j=0;j<i;j++) {
                dp[i] += dp[j];
            }
        }
        return dp[target];
    }
}
```

## JZ010矩形覆盖

思路

千万不要一上来就考虑n的情况，一定要从n=1开始逐步的分析，总结出规律。

## JZ011二进制中1的个数

## JZ013**调整数组顺序使奇数位于偶数前面**

思路

1. 定义一个新数组，遍历一遍数组找出奇数，在遍历一遍数组找出偶数。
2. 定义一个新数组，设立头尾两个指针，头指针从头到尾遍历奇数，尾指针从尾到头遍历偶数。

## JZ014**链表中倒数第k个结点**（完全无法理解递归的代码）

思路

1. 栈

   > 全部压入栈，再依次弹出k个节点。

2. 双指针

   > 最简单的方式就是使用两个指针，**第一个指针先移动k步，然后第二个指针再从头开始，这个时候这两个指针同时移动，当第一个指针到链表的末尾的时候，返回第二个指针即可**。注意，如果第一个指针还没走k步的时候链表就为空了，我们直接返回`null`即可。

3. 递归

   > 我对于递归这种方法还是不够了解。
   >
   > 有时间可以看看这篇博客[426，什么是递归，通过这篇文章，让你彻底搞懂递归](https://mp.weixin.qq.com/s/PgSSYc50ajnbh8zD6Ei07g)
   >
   > 递归的三步骤：
   >
   > 1. 终止条件
   > 2. 递归调用
   > 3. 逻辑处理

代码3



## JZ015链表反转

**思路**

> 为什么自己就想不明白呢？？自己先花了三个节点，然后去捋清楚其中的关系，然后就想不清楚了。。。

1. 双链表（把节点摘下来）

   将需要反转的节点摘下来，同时定义两个辅助节点，其中第一个pre节点记录该节点前一个节点的位置，第二个节点p记录该节点后一个节点的位置。

   > 1. 题目所给的是单链表，想了一下反转后的样子：最后一个结点指向倒数第二个，倒数第二个指向倒数第三个，......，第二个指向第一个，**第一个指向null**;
   > 2. 知道了反转后各个结点指向哪之后，就需要开始调整每个结点的next指针。
   > 3. 这就需要把结点挨个从链表上**摘**下来，做调整；
   > 4. 这个调整过程需要两个指针辅助：pre记录其前一个结点位置，好让该结点的next指针指向前一个结点，但是在指向前一个结点前需要用一个指针p记录后一个结点地址，避免结点丢失。
   > 5. 例子：
   >    - 以head结点为例步骤如下：
   >    - 1.反转后head是指向null，所以未反转的时候其前一个结点应该是null，初始化pre指针为null；
   >    - 2.用p指针记录head的下一个结点head.next；
   >    - 3.从链表上摘下head，即让head.next指向pre；
   >    - 4.此时已完成head结点的摘取及与前一个节点的连接，则我们需要操作下一个结点：故需移动pre和head，让pre指向head，head指向下一个节点。
   >    - 重复这四个操作直到head走完原链表，指向null时，循环结束，返回pre。

2. 栈实现

   > 注意栈中最后一个节点，一定要指向null。

3. 递归实现

   > 一看就明白，自己写就是不会写。why？？
   >
   > ```java
   > public class Solution {
   >     public ListNode ReverseList(ListNode head) {
   >         if(head == null || head.next == null) {
   >             return head;
   >         }
   >         ListNode next = head.next;
   >         ListNode reverse = ReverseList(next);
   >         // reverse是反转之后的链表，那么next肯定是最后一个节点。
   >         next.next = head;
   >         head.next = null;
   >         return reverse;
   >     }
   > }
   > ```
   >
   > 还有一种方法可以提高递归的效率，但是难以理解，就暂时不去考虑了。

   

**代码1**

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if( head == null) {
            return null;
        }
        ListNode pre = null;
        ListNode p = null;
        while(head != null) {
            p = head.next;
            head.next = pre;  // 明确这里为什么可以将pre的值付给head.next
            pre = head;
            head = p;
        }
        return pre;
    }
}
```

**代码2**

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if( head == null) {
            return null;
        }
        Stack<ListNode> s = new Stack<ListNode>();
        while(head != null) {
            s.push(head);
            head = head.next;
        }
        ListNode p = s.pop();
        ListNode newHead = p;
        while(!s.isEmpty()) {
            p.next = s.pop();
            p = p.next;
        }
        p.next = null;  // 最后一个节点必须指向null。
        return newHead;
    }
}
```

代码3

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if(head == null || head.next == null) {
            return head;
        }
        ListNode next = head.next;
        ListNode reverse = ReverseList(next);
        // reverse是反转之后的链表，那么next肯定是最后一个节点。
        next.next = head;
        head.next = null;
        return reverse;
    }
}
```

## JZ028**数组中出现次数超过一半的数字**

思路：

1. 排序取中值

   > 个人觉得非常的简单，但是时间效率非常低。

2. 一换一

   > 假设超过一般的数字可以和没有超过一半的数字一换一（就是相互抵消），那么数组中剩下的一定是超过一半的数字。
   >
   > 用preValue记录上一次访问的值，count表明当前值出现的次数，如果下一个值和当前值相同那么count++；如果不同count--，减到0的时候就要更换新的preValue值了，因为如果存在超过数组长度一半的值，那么最后preValue一定会是该值。

**代码2**

```java
import java.util.*;
public class Solution {
    public int MoreThanHalfNum_Solution(int [] array) {
        int num = array[0];
        int count = 1;
        for(int i=1;i<array.length;i++) {
            if(num == array[i]){
                count++;
            }else{
                count--;
                // 只有在count--的情况下才可能出现count=0的情况。
                if(count == 0){
                    num = array[i];
                    count = 1; // 计数器需要重新复制
                }
            }
        }
        return num;
    }
}
```



## JZ029最小的k个数

**题目描述**

给定一个数组，找出其中最小的K个	数。例如数组元素是4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4。如果K>数组的长度，那么返回一个空的数组。

**解题思路**

1. 利用选择排序的思想。

   > 选择最小的数字。移动到数组的首位。直到前K个数字达到有序。时间复杂度：O(kn)

2. 利用堆的思想

   > 将一个数组调整为小根堆时间复杂度为：O(n)，选择前K个小数时间复杂度为O(kn)。

3. 构建大小为K的大根堆。

   > 建立一个大根堆。遍历数组，凡是大于堆顶的数字，不管；小于堆顶的数字，弹出堆顶并将新的数字加入堆中，利用优先队列自身去调整堆。（自己的叙述能力真的是有问题）

代码3

```java
import java.util.ArrayList;
import java.util.Queue;
import java.util.PriorityQueue;
import java.util.Collections;

public class Solution {
    public ArrayList<Integer> GetLeastNumbers_Solution(int [] input, int k) {
        ArrayList<Integer> list = new ArrayList<Integer>();
        if ( input == null || input.length == 0 || k <= 0 || k > input.length) {
            return list;
        }
        Queue<Integer> queue = new PriorityQueue<Integer>((x, y) -> y-x);
        for(int i=0;i<k;i++) {
            queue.add(input[i]);
        }
        for(int i=k;i<input.length;i++) {
            if(input[i] < queue.peek()){
                queue.poll();
                queue.add(input[i]);
            }
        }
        while(!queue.isEmpty()) {
            list.add(queue.poll());
        }
        Collections.reverse(list);
        return list;
    }
}
```

需要注意的点：

- 用优先队列来实现堆（默认为小根堆）
- 改写匿名类或者实现lamda函数构造大根堆
- Arraylist的反转

## JZ030连续子数组的最大和

**题目**

> 输入一个整型数组，数组里有正数也有负数。数组中的一个或连续多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为 O(n).
>
> INPUT:[1,-2,3,10,-4,7,2,-5]
>
> OUTPUT:18
>
> ```
> 输入的数组为{1,-2,3,10,—4,7,2,一5}，和最大的子数组为{3,10,一4,7,2}，因此输出为该子数组的和 18。 
> ```

**思路**

1. 动态规划

   > 首先要找出状态方程：`max( dp[ i ] ) = getMax( max( dp[ i -1 ] ) + arr[ i ] ,arr[ i ] )`
   >
   > > 但是个人认为状态方程很难找出，本题还会易于理解的，但是其他的题目就难以理解。
   >
   > 典型的动态规划。dp[n]代表以当前元素为截止点的连续子序列的最大和，如果dp[n-1]>0，dp[n]=dp[n]+dp[n-1]，因为当前数字加上一个正数一定会变大；如果dp[n-1]<0，dp[n]不变，因为当前数字加上一个负数一定会变小。使用一个变量max记录最大的dp值返回即可。

2. 分治算法

   > 易知，对于一数字序列，其最大连续子序列和对应的子序列可能出现在三个地方。或是整个出现在输入数据的前半部（左），或是整个出现在输入数据的后半部（右），或是跨越输入数据的中部从而占据左右两半部分。前两种情况可以通过递归求解，第三种情况可以通过求出前半部分的最大和（包含前半部分的最后一个元素）以及后半部分的最大和（包含后半部分的第一个元素）而得到，然后将这两个和加在一起即可。
   >
   > 时间复杂度：O(nlogn)

**代码1**

```java
public class Solution {
    public int FindGreatestSumOfSubArray(int[] array) {
        int max = array[0];
        for(int i=1;i<array.length;i++) {
            // 这里面array[i]不是原来的数组，而是dp数组。array[i]还好理解，arrry[i-1]才需要真正理解。
            array[i] += array[i-1] > 0 ? array[i-1] : 0;
            max = Math.max(max, array[i]);
        }
        return max;
    }
}
```

**代码2**

```java
package test;

public class Test1 {
    public static int FindGreatestSumOfSubArray(int[] array, int left ,int right) {
        if(left == right){
            if(array[left] <= 0)
                return 0;
            else
                return array[left];
        }
        int mid = left + (right - left) / 2;
        int leftRes = FindGreatestSumOfSubArray(array, left, mid);
        int rightRes = FindGreatestSumOfSubArray(array, mid+1, right);
        int maxLeft = array[mid], sumLeft = array[mid];
        int maxRight = array[mid+1], sumRight = array[mid+1];
        for(int i= mid-1;i>=left;i--) {
            sumLeft += array[i];
            maxLeft = maxLeft > sumLeft ? maxLeft : sumLeft;
        }
        for(int j=mid+2;j<=right;j++) {
            sumRight += array[j];
            maxRight = maxRight > sumRight ? maxRight : sumRight;
        }
        leftRes = leftRes > rightRes ? leftRes : rightRes;
        maxLeft = maxLeft > 0 ? maxLeft : 0;
        maxRight = maxRight > 0 ? maxRight : 0;
        return leftRes > (maxLeft + maxRight) ? leftRes : (maxLeft + maxRight);
    }
    public static void main(String[] args) {
        //String s = "This is a sample";
        int array[] = new int[]{1,-2,3,10,-4,7,2,-5};
        System.out.println(FindGreatestSumOfSubArray(array, 0, array.length-1));
    }
}
```



## **JZ032把数组排成最小的数**

**题目**

> 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组{3，32，321}，则打印出这三个数字能排成的最小数字为321323。

思路

1. ~~补位+排序~~

   > ~~例如题目中的3，32,321确定好最大数字的位数，然后每一个数字都按照最大数字进行补位。3补位成333,32补位成322（以该数字最后一位进行补位）。~~
   >
   > ~~问题是：其中的原理是什么，并且补位很难进行操作。~~
   >
   > 思路错误：取例32 与 322，按照此方法无法比较大小，但是从结果看。32322>32232。

2. 两个数字合并成字符串

   > **比较两个字符串s1, s2大小的时候，先将它们拼接起来，比较s1+s2,和s2+s1那个大，如果s1+s2大，那说明s2应该放前面，所以按这个规则，s2就应该排在s1前面**。
   >
   > 本质上这个问题就是一种将各个数字进行全排列，拼接成不同的数字，在选择其中最小的数字。我们先按照这种选择的思路去两两进行排列，对每个数字进行排序，最终对于排序好的各个数字进行一个全排列，这样得出的结果就是一个符合要求的最小的数字。

**代码2**

```java
import java.util.*;

public class Solution {
    public String PrintMinNumber(int [] numbers) {
        if(numbers == null) 
            return "";
        for(int i=0;i<numbers.length;i++) {
            for(int j=i+1;j<numbers.length;j++) {
                int a = Integer.valueOf(numbers[i] + "" + numbers[j]);
                int b = Integer.valueOf(numbers[j] + "" + numbers[i]);
                if(a > b) {
                    int temp = numbers[j];
                    numbers[j] = numbers[i];
                    numbers[i] = temp;
                }
            }
        }
        StringBuffer sb = new StringBuffer();
        for(int i:numbers) {
            sb.append(i);
        }
        return sb.toString();
    }
}
```



### Arrays.sort() 库函数的使用

`Arrays.sort()`默认实现升序排序，但是如果需要进行降序操作，需要：

1. 不能使用`int float`等基本数据类型，而是`Integer Float`包装类型。

2. 额外调用`Collections.reverseOrder()`参数

   > ```java
   > package Sort;
   > 
   > import java.util.Arrays;
   > import java.util.Collections;
   > 
   > public class SortTest {
   >     public static void main(String[] args) {
   >         int[] numbers = new int[]{1,2,4,3,6,5,8,7,9};
   >         Integer[] num = new Integer[numbers.length];
   >         for(int i=0;i<numbers.length;i++) {
   >             num[i] = new Integer(numbers[i]);
   >         }
   >         Arrays.sort(num, Collections.reverseOrder());
   >         for(int i : num) {
   >             System.out.println(String.valueOf(i));
   >         }
   >     }
   > }
   > ```

3. 或者重写Comparator接口

   > ```java
   > package Sort;
   > 
   > import java.util.Arrays;
   > import java.util.Comparator;
   > 
   > public class SortTest {
   >     public static void main(String[] args) {
   >         int[] numbers = new int[]{1,2,4,3,6,5,8,7,9};
   >         Integer[] num = new Integer[numbers.length];
   >         for(int i=0;i<numbers.length;i++) {
   >             num[i] = new Integer(numbers[i]);
   >         }
   >         Arrays.sort(num, new Comparator<Integer>() {
   >             @Override
   >             public int compare(Integer o1, Integer o2) {
   >                 return o2-o1;
   >             }
   >         });
   >         for(int i : num) {
   >             System.out.println(String.valueOf(i));
   >         }
   >     }
   > }
   > ```

4. Comparator接口换为lambda表达式

   > ```java
   > package Sort;
   > 
   > import java.util.Arrays;
   > 
   > public class SortTest {
   >     public static void main(String[] args) {
   >         int[] numbers = new int[]{1,2,4,3,6,5,8,7,9};
   >         Integer[] num = new Integer[numbers.length];
   >         for(int i=0;i<numbers.length;i++) {
   >             num[i] = new Integer(numbers[i]);
   >         }
   >         Arrays.sort(num, (x,y) -> y-x);
   >         for(int i : num) {
   >             System.out.println(String.valueOf(i));
   >         }
   >     }
   > }
   > ```
   >

## JZ035数组中的逆序对

**思路**

1. 暴力

   > 题目中对于100%的数据，size小于等于2*10^5。暴力估计会超时。
   >
   > 时间复杂度O(n^2)

2. 归并排序的思想

   > 在归并排序中需要对两个有序的数组进行归并，在这个归并的过程中可以进行逆序对的统计。
   >
   > 数组a的元素`array[i]` 如果大于 数组b的元素`array[j]`，那么数组a中`array[i+1]……array[mid]`中的全部元素都大于`array[j]`。则可以统计逆序对为`mid-i+1`.
   >
   > 

**代码2**

```java
public class Solution {
    public int count = 0;
    public int InversePairs(int [] array) {
        if(array == null || array.length == 0) {
            return 0;
        }
        int mid = array.length / 2 ;
        sort(array, 0 ,array.length-1);
        // sort(array, mid+1, array.length-1);
        return count;
        
    }
    public void sort(int [] array, int low, int high) {
        if(low == high)
            return ;
        int mid = low + (high - low) / 2;
        int count = 0;
        sort(array, low, mid);
        sort(array, mid+1, high);
        merge(array, low, mid, high);
    }
    public void merge(int [] array, int low, int mid, int high) {
        // 归并是对两个已经排好序的数组进行归并。
        int[] temp = new int [high- low +1];
        int k = 0;
        int i = low, j = mid+1;
        // int count = 0;
        while(i<=mid && j<=high) {
            if(array[i] > array[j]){
                count += mid - i + 1;
                count %= 1000000007;
                temp[k++] = array[j++];
            }else{
                temp[k++] = array[i++];
            }
        }
        while(i<=mid) temp[k++] = array[i++];
        while(j<=high) temp[k++] = array[j++];
        k=0;
        i=low;
        while(k < temp.length) {
            array[i++] = temp[k++];
        }
        //return count;
    }
}
```

### 基本数据类型无法进行引用传递

## JZ037数字在升序数组中出现的次数

**题目**

升序数组

> 该如何利用升序数组这个性质呢？？

**思路**

1. 计数器+遍历（易）

## JZ036两个链表的一个公共结点（每天看一遍，学一下）

思路

1. 双指针

   > > 看了看题解豁然开朗，看了看代码叹为观止。人与人之间的差距这么大吗？
   > >
   > > 如果自己写代码，自己如何实现合并这个操作。
   >
   > 假设两个链表a，b长度相等，两个链表都从到尾遍历，则一定同时达到公共结点。如果两个链表不相等，则想法设法构造出两个相等的链表a+b，b+a，也是从头到尾一直遍历直到到达公共结点。
   >
   > 代码
   >
   > ```
   > public class Solution {
   >     public ListNode FindFirstCommonNode(ListNode pHead1, ListNode pHead2) {
   >         ListNode p1 = pHead1;
   >         ListNode p2 = pHead2;
   >         while(p1 != p2) {
   >             p1 = p1!=null ? p1.next : pHead2;
   >             p2 = p2!=null ? p2.next : pHead1;
   >         }
   >         return p1;
   >     }
   > }
   > ```
   >

## JZ041和为S的连续正数序列

**题目**

> 小明很喜欢数学,有一天他在做数学作业时,要求计算出9~16的和,他马上就写出了正确答案是100。但是他并不满足于此,他在想究竟有多少种连续的正数序列的和为100(至少包括两个数)。没多久,他就得到另一组连续正数和为100的序列:18,19,20,21,22。现在把问题交给你,你能不能也很快的找出所有和为S的连续正数序列? Good Luck!
>
> 返回值：
>
> 输出所有和为S的连续正数序列。序列内按照从小至大的顺序，序列间按照开始数字从小到大的顺序。

题目里面有很多的小细节，这些小细节决定了题目能不能简化，例如对于边界值能不能简化。本题：

- 至少包括两个数（那么左边界的值一定sum/2）。

利用这些小细节就可能简化运算。

**思路**

1. 前缀和

   > 对于求一个区间和，一贯的优化技巧是使用前缀和。
   >
   > | index | 0    | 1    | 2    | 3    | 4    | 5    |
   > | ----- | ---- | ---- | ---- | ---- | ---- | ---- |
   > | value | 0    | 1    | 2    | 3    | 4    | 5    |
   > | sum   | 0    | 1    | 3    | 6    | 10   | 15   |
   >
   > sum[i]表示前i个数的和。比如`sum[1] = 1`,表示前一个数的和为`1`，`sum[2] = 3`, 表示前`2`个数的和为`3`.现在我们要求区间`[2,4]`表示求第`2,3,4`个数的和，就等于`sum[4] - sum[1] = 9`

2. 滑动窗口

   > >我也明白其中的思想，但是为什么就是写不出来呢？是写的少，还是根本没有理解到滑动窗口的思想呢。
   >
   > 1. 什么是滑动窗口？
   >    顾名思义，首先是一个窗口，既然是一个窗口，就需要用窗口的左边界`i`和右边界`j`来唯一表示一个窗口，其次，滑动代表，窗口始终从左往右移动，这也表明左边界`i`和右边界`j`始终会往后移动，而不会往左移动。
   >    这里我用左闭右开区间来表示一个窗口。比如
   >    ![图片说明](https://uploadfiles.nowcoder.com/images/20200504/284295_1588586066412_7E35C7D253FD76C798B44ABB19E885BF)
   > 2. 滑动窗口的操作
   >
   > - 扩大窗口，`j += 1`
   > - 缩小窗口，`i += 1`
   >   算法步骤：
   >
   > 1. 初始化，`i=1,j=1`, 表示窗口大小为`0`
   > 2. 如果窗口中值的和小于目标值sum， 表示需要扩大窗口，`j += 1`
   > 3. 否则，如果狂口值和大于目标值sum，表示需要缩小窗口，`i += 1`
   > 4. 否则，等于目标值，存结果，缩小窗口，继续进行步骤`2,3,4`
   >
   > 这里需要注意2个问题：
   >
   > 1. 什么时候窗口终止呢，这里窗口左边界走到sum的一半即可终止，因为题目要求至少包含2个数
   > 2. 什么时候需要扩大窗口和缩小窗口？解释可看上述算法步骤。

**代码2**

这里犯了个错误，判断`res == sum`之后，没有接着对滑动窗口进行处理，导致陷入了死循环。

```java
public static ArrayList<ArrayList<Integer> > FindContinuousSequence(int sum) {
        // 如何解决重复计算问题
        // 双指针
        ArrayList<ArrayList<Integer>> list = new ArrayList<ArrayList<Integer>>();
        if(sum <= 0)
            return list;
        int i=1, j=1, res=1;
        while(i <= sum /2) {
            if(res == sum) {
                System.out.println("Found");
                ArrayList<Integer> arr = new ArrayList<Integer>();
                for(int k=i;k<=j;k++) {
                    arr.add(k);
                }
                list.add(arr);
                // 找到了之后如何处理？？不处理会陷入死循环。
                j++;
                res += j;
            }else if(res < sum) {
                j++;
                res += j;
            }else {
                res -= i;
                i++;
            }
        }
        // System.out.println(i + " " + j + " " + res);
        return list;
    }
```



## JZ042和为S的两个数字

独立做出 

加油！！！

## JZ046孩子们的游戏（圆圈中最后剩下的数）(还是未理解)

思路

1. 直接用链表模拟

   > 有ArrayList与LinkedList两种实现方式，但是在实现的过程中还是有一定的区别，代码中用了LinkedList的实现方式。

2. 利用递推公式

   > 利用公式`f(n) = (f(n-1) + m) % n,f(1) = 0`，进行循环或者递归操作。

代码1

```java
import java.util.*;
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if(n <=0 || m <= 0){
            return -1;
        }
        LinkedList<Integer> list = new LinkedList<Integer>();
        for(int i=0;i<n;i++) {
            list.add(i);
        }
        int cur = 0;
        while(list.size() > 1) {
            cur = (cur + m -1) % list.size(); 
            list.remove(cur);
        }
        return list.size() == 1 ? list.get(0) : -1;
    }
}
```

代码2

```java
import java.util.*;
public class Solution {
    public int LastRemaining_Solution(int n, int m) {
        if(n <=0 || m <= 0){
            return -1;
        }
        int res = 0;
        for(int i=2;i<=n;i++) {
            res = (res + m) % i;
        }
        return res;
    }
}
```



### 约瑟夫环

到了后面就有点看不懂了，但是结论是：`f(n) = (f(n-1) + m) % n`，就是利用数学归纳法得出该公式。

## JZ050数组中重复的数字

**题目**

> 在一个长度为n的数组里的所有数字都在0到n-1的范围内

本题有个特性，就是数字的大小是有范围的。

**思路**

1. 利用可以保持无重复性的数据结构set 或者map

2. 将每个元素放入到对应下标的位置上

   > ```java
   > import java.util.*;
   > public class Solution {
   >     public int duplicate (int[] numbers) {
   >         // write code here
   >         if(numbers == null || numbers.length == 0) {
   >             return -1;
   >         }
   >         int len = numbers.length;
   >         for(int i=0;i<len;i++) {
   >             if(numbers[i] != i) {
   >                 if(numbers[i] == numbers[numbers[i]])
   >                     return numbers[i];
   >                 int temp = numbers[numbers[i]];
   >                 numbers[numbers[i]] = numbers[i];
   >                 numbers[i] = temp;
   >                 i--;
   >             }
   >         }
   >         return -1;
   >     }
   > }
   > ```



## JZ051构建乘积数组

**解题思路**

问题在于：不能用除法，但是连乘耗时间，绝对不是解决问题的最佳方法。会出现大量的重复性乘法操作。

## JZ055链表中环的入口节点

**思路**

1. 哈希法

   > 1. 遍历单链表的每个结点
   > 2. 如果当前结点地址没有出现在set中，则存入set中
   > 3. 否则，出现在set中，则当前结点就是环的入口结点
   > 4. 整个单链表遍历完，若没出现在set中，则不存在环

2. 快慢指针

   > 1. 这题我们可以采用双指针解法，一快一慢指针。快指针每次跑两个element，慢指针每次跑一个。如果存在一个圈，总有一天，快指针是能追上慢指针的。
   >
   > 2. 如下图所示，我们先找到快慢指针相遇的点，p。我们再假设，环的入口在点q，从头节点到点q距离为A，q p两点间距离为B，p q两点间距离为C。
   >
   > 3. 因为快指针是慢指针的两倍速，且他们在p点相遇，则我们可以得到等式 2(A+B) = A+B+C+B. **（感谢评论区大佬们的改正，此处应为：如果环前面的链表很长，而环短，那么快指针进入环以后可能转了好几圈(假设为n圈)才和慢指针相遇。但无论如何，慢指针在进入环的第一圈的时候就会和快的相遇。等式应更正为 2(A+B)= A+ nB + (n-1)C）**
   >
   >    > 为什么慢指针在环中第一圈就会与快指针相遇？
   >
   > 4. 由3的等式，我们可得，`A = (n-2)(B+C) + C`。其中B+C为环的长度。
   >
   > 5. 这时，因为我们的slow指针已经在p，我们可以新建一个另外的指针，slow2，让他从头节点开始走，每次只走下一个，原slow指针继续保持原来的走法，和slow2同样，每次只走下一个。
   >
   > 6. 我们期待着slow2和原slow指针的相遇，因为我们知道A=C，所以当他们相遇的点，一定是q了。
   >
   > 7. 我们返回slow2或者slow任意一个节点即可，因为此刻他们指向的是同一个节点，即环的起始点，q。
   >
   > <img src="https://uploadfiles.nowcoder.com/images/20200216/664093853_1581796891319_57DB204B64D4328DA9CB2FC8F955C379" alt="图片说明" style="zoom:67%;" />

## JZ063数据流中的中位数

**题目描述**

如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。我们使用Insert()方法读取数据流，使用GetMedian()方法获取当前读取数据的中位数。

**解题思路**

最大的问题在于选用什么样的数据结构。

- 保留从之前数据流读取的数据，当新的数据从流中读取出之后，新的数据就插入到数据结构中。
- 当读取偶数个数字时，取中间两个数字；当读取奇数个数字时，取中间一个数字。

因此需要实现两个操作，插入（存）+读取（取）



| 数据结构       | 存数据  | 取数据 |
| -------------- | ------- | ------ |
| 无序数组       | O(1)    | O(n)   |
| 有序数组       | O(n)    | O(1)   |
| 链表           | O(1)    | O(1)   |
| 二叉搜索树     | \       | \      |
| 平衡二叉搜索树 | \       | \      |
| 大根堆与小根堆 | O(logn) | O(1)   |

> 二叉搜索树可以把插入新数据的平均时间复杂度降低到最低，但是由于二叉搜索树极度不平衡，从而看起来像一个排序的链表的时候，插入新数据的时间仍然是O（n），为了得到中位数，可以在二叉树节点中添加一个表示子树节点数目的字段。有了这个字段，可以在平均O（logN）时间内得到中位数，但最差情况仍然是O（logN）。
> 为了避免二叉搜索树的最差情况，可以利用平衡的二叉搜索树，即VAL树。通常VAL树的平衡因子是左右子树的高度差。可以稍作修改，把VAL树的平衡因子改为左右子树节点数目之差。有了这个改动，可以用O（logN）时间往VAL树中添加一个新节点，同时用O（1）时间得到所有节点的中位数。
> 虽然VAL树的效率很高，但是大部分编程语言的函数库是没有实现这个数据结构的，在面试期间短短几十分钟也是不可能实现这个功能的，所以只能考虑其他办法。
> ————————————————
> 版权声明：本文为CSDN博主「为心而斗」的原创文章，遵循CC 4.0 BY-SA版权协议，转载请附上原文出处链接及本声明。
> 原文链接：https://blog.csdn.net/qq_33575542/article/details/80881015

整体思路：

- 将整个数组分成两部分，左部分的最大值要小于右部分的最小值。左部分用大根堆实现，右部分用小根堆实现。
- 同时需要保证数据能够平均分配到两个堆中，因此两个堆中的数据量之差不能大于1。
- 当已读取的数据量为偶数时，将新的数据插入到小根堆，反制插入到大根堆。（这个地方为什么啊？？个人认为反过来也没有问题。）
- 新插入的数据要与大根堆/小根堆的堆顶进行比较。
  - 当要插入数字到小根堆中时，需要与大根堆堆顶数字进行比较，如果堆顶数字比较大，将数据流中的数字插入到大根堆，并且将大根堆堆顶数字弹出插入到小根堆。此时保证了小根堆中所有数字均大于大根堆。

**代码**

```java
import java.util.*;

public class Solution {
    
    Queue<Integer> min = new PriorityQueue<Integer>();
    Queue<Integer> max = new PriorityQueue<Integer>((x,y) -> y-x);
    int count = 0;
    public void Insert(Integer num) {
        if((count & 1) == 0) { //偶数情况下，插入最小堆。
            max.add(num); //比较最大值
            int maxNum = max.poll(); //比较最大值
            min.add(maxNum); //插入最小堆
        }else{
            min.add(num);
            int minNum = min.poll();
            max.add(minNum);
        }
        count++;
    }
    public Double GetMedian() {
        if((count & 1) == 0)  // 奇数
            return (max.peek() + min.peek()) / 2.0;
        else
            return (double)min.peek();
    }
}
```

# LeetCode

## LeetCode005最长回文子串

## NC113验证IP地址

**思路**

> 不难，考察的是Java语言的一些基础内容。

1. 将字符串进行分割

   `s.split("")`，注意一些字符需要进行转义，例如'.'，`变成s.split("\\.")`

2. 将字符串转化为数字

   `int temp = Integer.parseInt(s)`

