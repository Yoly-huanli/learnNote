[toc]

# 二叉树

## 1.二叉树所有路径

写出二叉树所有的路径

```js
var binaryTreePaths = function(root) {
   let res = []
   let list = []
   function dfs(root){
        if(!root) return
        list.push(root.val)
        if(!root.left && !root.right){
            res.push([...list].join("->"))
        }
     if(!root.left)  { dfs(root.left)  list.pop()}
     if(!root.right)  { dfs(root.right)  list.pop()}
    }
   dfs(root)
   return res
};
```

### JZ84二叉树中和为某一值的路径（不需要起始）

![image-20230218152201823](../img/image-20230218152201823.png)



不需要从根节点开始，也不需要在叶子节点结束，但是一定是从父亲节点往下到孩子节点，问有多少条路径满足要求

思路：

+ 从根开始判断是不是存在和为给定值的路径（结束条件不是必须为叶子结点才结束）
+ 把子树看做完整的一棵树，重新执行判断过程

```JS
let res = 0
function FindPath( root ,  sum ) {
    if(!root) return 0
    dfs(root, sum)
    FindPath(root.left,  sum )
    FindPath(root.right, sum )
    return res
}

function dfs(root, sum){
    if(!root) return
    if(root.val===sum){
      res = res+1
    }
    dfs(root.left, sum-root.val)
    dfs(root.right, sum-root.val)
}
```



## JZ81 调整数组顺序使奇数位于偶数前面(二)
> 输入：[1,2,3,4]
返回值：
[1,3,2,4]
说明：
[3,1,2,4]或者[3,1,4,2]也是正确答案 

其他思路： 前后各一个指针进行移动
```TS
export function reOrderArrayTwo(array: number[]): number[] {
    // write code here
    if(!array || array.length == 0 || array.length == 1){
        return array;
    }
    let first = 0, last = array.length - 1;
    while(first < last){
        if(array[first] % 2 == 1){
            first++;
        }
        if(array[last] % 2 == 0){
            last--;
        }
        if(array[first] % 2 == 0 && array[last] % 2 == 1){
            let temp = array[first];
            array[first] = array[last];
            array[last] = temp;
            first++;
            last--;
        }
    }
    return array;
}
```



[toc]
## 剑指offer


### 二叉树


#### ==**重建二叉树**==
+ 边界条件

```
输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

例如，给出
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：
    3
   / \
  9  20
    /  \
   15   7
```

+ 创建新节点，然后左右子树递归调用
+ 条件，前序不为空的时候调用
+ 用一个start指针，一个end指针，判断结束条件
```
var buildTree = function(preorder, inorder) {
    if(preorder.length){
        return generation(0,preorder.length-1)
    }
    function generation(start,end){
       if(start>end){
            return null
       }
       var root=preorder.shift()
       var node= new TreeNode(root) 
       var inorderindex=inorder.indexOf(root)
       node.left=generation(start,inorderindex-1)
       node.right=generation(inorderindex+1,end)
       return node
  }
  return null
};
```
#### 判断是否为子树
输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构

```
function HasSubtree(pRoot1, pRoot2)
{
    if(pRoot1==null || pRoot2==null){
        return false
    }
    return find(pRoot1, pRoot2)|| HasSubtree(pRoot1.left, pRoot2)|| HasSubtree(pRoot1.right, pRoot2)
}

function find(p1, p2){
    if(p2==null)return true;
    if(p1==null)return false;
    return p1.val==p2.val && find(p1.left, p2.left)&& find(p1.right, p2.right)
}
```
#### ==二叉树的镜像==
操作给定的二叉树，将其变换为源二叉树的镜像。

```
例如输入：

     4
   /   \
  2     7
 / \   / \
1   3 6   9
镜像输出：

     4
   /   \
  7     2
 / \   / \
9   6 3   1
```
+ 中止条件，而且没有用循环
```
function Mirror(root)
{
     if(!root){
        return null
    }
    let temp=root.left;
    root.left=root.right;
    root.right=temp;
    Mirror(root.left)
    Mirror(root.right)
    return root
}
```
*******
#### 判断对称二叉树

```
请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

例如，二叉树 [1,2,2,3,4,4,3] 是对称的。

    1
   / \
  2   2
 / \ / \
3  4 4  3
```

+ 终止条件，两边都为空，true,一边不为空，false
```
var isSymmetric = function(root) {
    function isMirror(r1, r2) {
        if(!r1 && !r2) return true
        if(!r1 || !r2) return false

        return r1.val === r2.val && isMirror(r1.left, r2.right) && isMirror(r1.right, r2.left)
    }

    return isMirror(root, root)
};
```

*******
#### 二叉树后序遍历序列
输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历结果。如果是则返回 true，否则返回 false。假设输入的数组的任意两个数字都互不相同。

```
参考以下这颗二叉搜索树：

     5
    / \
   2   6
  / \
 1   3

示例 1：

输入: [1,6,3,2,5]
输出: false

示例 2：

输入: [1,3,2,6,5]
输出: true
```
思路：
+ 牢记，二叉树后序遍历左右中，最后一个元素为根节点
+ 二叉搜索树，左节点永远小于根节点，右节点永远大于根节点

```
function VerifySquenceOfBST(sequence)
{
   //如果为[]，要返回false
   if(!sequence.length){
      return false  
   }
   return helper(sequence)
}
//正常的处理代码
function helper(postorder){
   let len=postorder.length;
   //叶子节点，true
   if(len<2){
       return true
   }
   //根节点与左子树
   let root=postorder[len-1];
   for(var i=0;i<len-1;i++){
       if(postorder[i]>root){
           break;
       }
   }
   //右子树是否都比根节点大，every只有全部为true，结果才是TRUE
   let noderight=postorder.slice(i,len-1).every(x=>x>root);
   if(noderight){
   //递归判断
       return helper(postorder.slice(0,i)) && helper(postorder.slice(i,len-1))
   }else{
       return false
   }
}
```

### 7.二进制中1的个数

```
输入一个整数，输出该数32位二进制表示中1的个数。其中负数用补码表示
```

```
function NumberOf1(n)
{  
  let count = 0;
     while(n!= 0){
            count++;
            n = n & (n - 1);
     }
        return count;
} 
```
举个例子：一个二进制数1100，从右边数起第三位是处于最右边的一个1。减去1后，第三位变成0，它后面的两位0变成了1，而前面的1保持不变，因此得到的结果是1011.我们发现减1的结果是把最右边的一个1开始的所有位都取反了。这个时候如果我们再把原来的整数和减去1之后的结果做与运算，从原来整数最右边一个1那一位开始所有位都会变成0。如1100&1011=1000.也就是说，把一个整数减去1，再和原整数做与运算，会把该整数最右边一个1变成0.那么一个整数的二进制有多少个1，就可以进行多少次这样的操作。

### 8.数值的整数次方

```
var myPow = function(x, n) {
    return x**n
};
//或者Math.pow(x,n)
```
手写思路：直接循环计算会超出时间限制，

```
所以把比如2^16变为(2^8)*(2^8),2^17变为(2^8)*(2^8)*2,再考虑负指数的情况。
```
```
var myPow = function(x, n) {
    if(n == 0){
        return 1;
    }
    let m=Math.abs(n);
    let re = myPow(x, Math.floor(m/2));
    let results= m %2 === 0 ? re* re : re*re*x;
    return n>=0 ? results : 1 / results;
};
```


### 12.顺时针输出矩阵
输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。

```
输入：matrix = [[1,2,3],[4,5,6],[7,8,9]]
输出：[1,2,3,6,9,8,7,4,5]
```

+ 遍历规则
+ 截止条件matrix.length==0和(top>bottom || left>right)

```
var spiralOrder = function(matrix) {
   if(matrix.length==0){
       return []
   }
   let container=[]
   let top=0;
   let right=matrix[0].length-1;
   let bottom=matrix.length-1;
   let left=0;
   while(top<=bottom && left<=right){
     for(let i=left;i<=right;i++){
         container.push(matrix[top][i]);
     };
     top++;
     for(var j=top;j<=bottom;j++){
         container.push(matrix[j][right]);
     }
     right--;
     if(top>bottom || left>right){
         break;
     }
     for(var m=right;m>=left;m--){
         container.push(matrix[bottom][m]);
     }
     bottom--;
     for(var n=bottom;n>=top;n--){
         container.push(matrix[n][left])
     }
     left++;
   }
   return container
};
```
### 13.栈的压入，弹出

```
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。例如，序列 {1,2,3,4,5} 是某栈的压栈序列，序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。
```
+ 思路掌握,index为弹出序列的指针，stack为共同维护的一个栈，当前第一个弹出元素在push里没有的时候，要push进去stack，当恰好stack最后一个元素为弹出的元素，把stack里这个元素弹出，继续弹出的下一个元素判断
```
var validateStackSequences = function(pushed, popped) {
    var stack = [];
    var index = 0
    for(var i = 0; i < pushed.length; i++){
        stack.push(pushed[i]);
        while(stack.length && stack[stack.length - 1] === popped[index]){
            stack.pop();
            index ++;
        }
    }
    return stack.length === 0
};
```


### 15.不用加减乘除做加法

```
写一个函数，求两个整数之和，要求在函数体内不得使用 “+”、“-”、“*”、“/” 四则运算符号。

输入: a = 1, b = 1
输出: 2
```

```
var add = function(a, b) {
    while(a !== 0){
        let temp = (a ^ b); //不进位加法
        a = (a & b) << 1; ////进位部分赋值给a
        b = temp; ////不进位部分赋值给b
    }
    return b //返回不进位部分
};
```
****
### 17.构建乘积数组

```
给定一个数组 A[0,1,…,n-1]，请构建一个数组 B[0,1,…,n-1]，其中 B 中的元素 B[i]=A[0]×A[1]×…×A[i-1]×A[i+1]×…×A[n-1]。不能使用除法。

输入: [1,2,3,4,5]
输出: [120,60,40,30,24]
```
+ 暴力法两层循环超时，应该元素左边相乘，右边相乘，再相乘，第二步时直接得到结果
+ 注意边界，因为乘的是i-1,i+1的元素

```
var constructArr = function(a) {
    let B=[1];
    let len=a.length;
    let temp=1;
    for(let i=1;i<len;i++){
        temp=temp*a[i-1]
        B[i]=temp
    }
    temp=1;
    for(let j=len-1-1;j>=0;j--){
        temp=temp*a[j+1];
        B[j]=B[j]*temp
    }
    return B
};
```
******
### 19.左旋转字符串

```
汇编语言中有一种移位指令叫做循环左移（ROL），现在有个简单的任务，就是用字符串模拟这个指令的运算结果。对于一个给定的字符序列S，请你把其循环左移K位后的序列输出。例如，字符序列S=”abcXYZdef”,要求输出循环左移3位后的结果，即“XYZdefabc”。是不是很简单？OK，搞定它！
```
+ 添加判断条件，否则牛客无法通过
```
function LeftRotateString(str, n)
{    
    if(!str || str.length==0){
        return ''
    }
    return str.substring(n)+str.substring(0,n)
}
```


******




[toc]
![image](https://note.youdao.com/yws/res/21535/75CB09FC797B4F8B8811052C7E7BFEAF)
# 算法
## 时间复杂度
 O(N)
 O(logn)
 O(n^2)
 程序执行的次数
```
for (var i=0;i<n;i++){
    for (var j=i;i<n;i++){
}
//执行n+n-1+n-2+...等差数列O（n^2）
```
## 按位左移右移
+ 32位表示(位操作32位都写出来)
+ 从右开始，比如2表示10, 是 2^1  ， 2^0
+ 负数为正数的反码加一
### 左移右移数学运算
+ 左移右移，<<,>> 右移补符号位，左移补0
，<<<,>>> 把负数的移动当成正数，都补0
```
// 10 在二进制中表示为 1010 ，左移一位后变成 10100 ，转换为十进制也就是 20，右移一位为101，为5
10 << 1 // -> 20 左移相当于 a * (2 ^ b)
10 >> 1 // -> 5  右移动a / (2 ^ b)
13 >> 1 // -> 6 
```
+ 取非

```
取按位非等于-值-1
~25=-26 (反码+1=补码 反码=补码-1=负数-1)
```
+ 按位异或

```
按位异或^就是不进位加法,进位就是普通求和操作
```

+ 按位求和
```
 // a + b = (a ^ b) + ((a & b) << 1) 异或加上进位的部分
 function sum(a, b) {
   if (a == 0) return b 
   if (b == 0) return a 
   let newA = a ^ b 
   let newB = (a & b) << 1 
   return sum(newA, newB)
 }
```
+ NaN与Infinity按位，为0

```
-1 >>> 32 的值为(2^32-1  )
1的二进制代码 00000000 00000000 00000000 00000001
1的反码       11111111 11111111 11111111 11111110
-1的二进制码  11111111 11111111 11111111 11111111
>>>32 无符号右移32位
000000000 0000000000 000000000 000000000 11111111 11111111 11111111 11111111
右移大于32，只保留最低的5位，得到(2^32-1  )
```
### 进制转换
+ 10进制换为二进制toString()
+ 某进制转换为10进制parseInt()

```
parseInt(num,2).toString(16)  //二进制转十六进制
```
### 补码表示
+ JS中求二进制，(number).toString(2)，负数和正数表示除了符号外相同。

```
如(-123).toString(2) == ‘-1111011’, (123).toString(2) == ‘1111011‘
```
+ 负数正码取反（不包括符号位）+1即得到补码表示 

```
func twoComple(num){
    const reg = /1|0/g; 
    var binary = num.toString(2);
    var twoC = binary.replace(reg,(x) => {return x === "0" ? "1" : "0"}); //取反
    twoC = twoC.substr(1); //除去符号位
    num = Number(twoC) + 1; //加一
    twoC = '1' + num.toString(2); //添加符号位
    return twoC
```



## 栈
### 用数组模拟栈
```
class Stack { 
  constructor() { 
    this.stack = [] 
  } 
  push(item) { 
    this.stack.push(item) 
  } 
  pop() { 
    this.stack.pop() 
  } 
  peek() { 
    return this.stack[this.getCount() - 1] 
  } 
  getCount() { 
    return this.stack.length 
  } 
  isEmpty() { 
    return this.getCount() === 0 
  } 
 } 
```

*******************
## 队列
### 模拟一个队列
```
class Queue { 
  constructor() { 
    this.queue = [] 
  } 
  enQueue(item) { 
    this.queue.push(item) 
  } 
  deQueue() { 
    return this.queue.shift() 
  } 
  getHeader() { 
    return this.queue[0] 
  } 
  getLength() { 
   return this.queue.length 
  } 
  isEmpty() { 
   return this.getLength() === 0 
  } 
```


***************
## 二叉树
### 构造树
#### 前序+中序(leetcode 105)
![image](https://note.youdao.com/yws/res/15449/1C76832B0F0C43D4AE169758BBB5D930)
+ 要判断没有子树到的情况，null
```
var buildTree = function(preorder, inorder) {
    if(preorder.length){
        return generation(0,preorder.length-1)
    }
    function generation(start,end){
       if(start>end){
            return null
       }
       var root=preorder.shift()
       var node= new TreeNode(root) 
       var inorderindex=inorder.indexOf(root)
       node.left=generation(start,inorderindex-1)
       node.right=generation(inorderindex+1,end)
       return node
  }
  return null
};
```

******************
### 二叉搜索树
+ 节点的左子树只包含小于当前节点的数。 
+ 节点的右子树只包含大于当前节点的数。 
+ 所有左子树和右子树自身必须也是二叉搜索树。

#### 验证是否为二叉搜索树(leetcode 98)
思路：设定上下边界，再限制左节点要小于当前节点，右节点大于当前节点


```
var isValidBST = function(root) {
    //初始的情况
    return vaildate(root,-Infinity,Infinity)
};
function vaildate(node,min,max){
    if(node===null){
        return true;
    }
    if(node.val>=max || node.val<=min){
        return false;
    }
    // 为左右节点更新上下边界
    return vaildate(node.left,min,node.val) && vaildate(node.right,node.val,max)
}
```
#### 平衡二叉树(AVL)(leetcode 108)
平衡二叉树是指一个二叉树每个节点 的左右两个子树的高度差的绝对值不超过 1。

```
将一个按照升序排列的有序数组，转换为一棵高度平衡二叉搜索树。
```
+ 数组是升序的，因此每次选取中间的节点为根节点，小于的为左节点，大于的为右节点
```
var sortedArrayToBST = function(nums) {
    return generation(nums,0,nums.length-1)
};

function generation(nums,start,end){
    if(start>end){
        return null;
    }
    let midindex=start+parseInt((end-start)/2);
    let midnode=nums[midindex];
    let node=new TreeNode(midnode)
    node.left=generation(nums,start,midindex-1)
    node.right=generation(nums,midindex+1,end)
    return node
}
```


********
### B+树
![image](https://note.youdao.com/yws/res/29301/792ED83E896D44DEA5CBB884AB017651)
+ 是多叉而不是二叉了，使用多叉的目的是降低树的高度;
+ 每个节点不再只是存储一个key了，可以存储多个key；
+ 非叶子节点存储key，叶子节点存储key和数据。
+ 叶子节点两两相连，为顺序查询提供了帮助