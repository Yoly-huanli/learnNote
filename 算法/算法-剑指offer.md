[toc]

总体思路：

+ 凡是需要O（n^2）实现的算法，都可以考虑双指针

+ 尽量不要修改原有的数据结构

# 性能好坏判断

```js
console.time('findNumber')
console.log('findNumber', findNumber(100)) //0.810302734375 ms
console.timeEnd('findNumber')
```

通过console.time和console.timeEnd可以得到中间的执行时间，从而判断好坏

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

### JZ82二叉树中是否存在和为某一值的路径

二叉树中是否存在一条路径，路径之和为给定的值

思路：

+ 路径：一定是需要遍历的，并且每遍历一层，就需要减去当前节点的值
+ 结束条件： 一定是叶子节点
+ 考虑root节点不存在的情况

```js
function hasPathSum( root ,  sum ) {
    if(!root) return false
    if(root.val==sum && !root.left && !root.right) return true
    return hasPathSum( root.left, sum-root.val ) || hasPathSum( root.right, sum-root.val ) 
}
```

### JZ34二叉树中和为某一值的所有路径

给出二叉树中和为某一个值的所有路径

![image-20230218151355308](../img/image-20230218151355308.png)

思路：

+ 结束条件是叶子节点，并且路径和满足条件
+ 节点遍历，遍历过程中，左节点不满足条件，要pop出当前节点，去遍历另一边，比如10-5-4这条路径不满足条件，pop出4， 去判断10-5-7
+ 需要两个变量保存最终结果和路径上的节点信息用于最后的返回
+ 需要考虑只有一个根节点的情况
+ 注意路径信息list需要拷贝，因为list是引用类型，后续的变动会影响当前的值，如果不拷贝，结果会变为[[][]]

```JS
let res= []  //存放最终的结果
let list = [] // 存放路径上的节点信息
function FindPath(root, expectNumber)
{
    dfc(root, expectNumber)
    return res
}
function dfc(root, sum){
    if(!root) return []
    list.push(root.val)
    if(root.val===sum && !root.left && !root.right){
        res.push([...list])
    }
   dfc(root.left, sum-root.val)
   dfc(root.right, sum-root.val)
   list.pop()
}
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

## 2.二叉树的遍历

二叉树每个节点最多有两个子节点，每个树的树节点都有value,left,right组成，left，right可能不存在，如下是一棵树的结构表示

<img src="../img/image-20220504210739486.png" alt="image-20220504210739486" style="zoom:50%;" />

### 前序遍历

根-->左-->右

```tsx
function preOrderTraverse(node:TreeNode| null){
  if(node===null) return 
  console.log(node.value)
  preOrderTraverse(node.left)
  preOrderTraverse(node.right)
}
preOrderTraverse(tree)
```

结果：

```
A
B
D
F
E
C
```

如果需要把最终的结果都要保存在一个数组里，那么此时需要一个数组用来存储最终遍历出来的结果，一个数组用来暂存节点

![image-20220504214033770](../img/image-20220504214033770.png)

```js
var preorderTraversal = function(root) {
    if(root===null){
        return []
    }
    var results=[]
    var stack=[root]
    while(stack.length){
        var node = stack.pop()
        results.push(node.val)
        if(node.right !==null){
            stack.push(node.right)
        }
        if(node.left !==null){
            stack.push(node.left)
        }
    }
    return results
};
```

### 中序遍历

左-->根-->右

```js
function inOrderTraverse(node:TreeNode| null){
  if(node===null) return 
  inOrderTraverse(node.left)
  console.log(node.value)
  inOrderTraverse(node.right)
}
inOrderTraverse(tree)
结果：
F
D
B
E
A
C
```

![image-20220504214148004](../img/image-20220504214148004.png)

```js
var inorderTraversal = function(root) {
    if(root===null){
        return []
    }
    var results=[]
    var stack=[]
    var cur = root
    while(true){
        while(cur!==null){
            stack.push(cur)
            cur=cur.left
        }
        if(stack.length==0){
            break
        }
        var cur=stack.pop()
        results.push(cur.val)
        cur=cur.right
    }
    return results
};
```

### 后序遍历

左-->右-->根

```JS

function postOrderTraverse(node:TreeNode| null){
  if(node===null) return 
  postOrderTraverse(node.left)
  postOrderTraverse(node.right)
  console.log(node.value)
}
postOrderTraverse(tree)
结果：
F
D
E
B
C
A
```

![image-20220504214352138](../img/image-20220504214352138.png)

```JS
var postorderTraversal = function(root) {
    if(root===null){
        return []
    }
    var stack=[root]
    var results=[]
    while(stack.length>0){
        let nodes=stack.pop()
        results.unshift(nodes.val)
        if(nodes.left){
            stack.push(nodes.left)
        }
        if(nodes.right){
            stack.push(nodes.right)
        }
    }
    return results
};
```

## 寻找第K小值

寻找 BST 里的第 K 小值

```JS
export function getKthValue(node: ITreeNode, k: number): number | null {
    inOrderTraverse(node)
    return arr[k - 1] || null
}
```

# 栈

## 有效的括号

https://leetcode.cn/problems/valid-parentheses/

```
输入：s = "()[]{}"
输出：true
```

思路：

+ 使用栈，左括号入栈，右括号出栈
+ 注意字符串为空的情况
+ stack.pop操作会直接出栈，造成影响，刚开始判断要用stack[stack.length-1]

```js
isValid = function(s) {
   if(!s.length) return true
   let stack = []
   let leftSymbol = '{[('
   let rightSymbol = '})]'
   for(let i=0;i<s.length;i++){
      if(leftSymbol.includes(s[i])){
        stack.push(s[i])
      }
      else if(rightSymbol.includes(s[i])){
        let res = stack[stack.length-1]
        if(judgeMatch(res,s[i])){
            stack.pop()
        }else{
            return false
        }
      }
   }
   return stack.length===0
};

function judgeMatch(a,b){
    if(a==='(' && b===')') return true
    if(a==='{' && b==='}') return true
    if(a==='[' && b===']') return true
    return false
}
```

换种数据结构

```js
var isValid = function(s) {
  let mapp={
      '(':-1,
      ')':1,
      '{':-2,
      '}':2,
      '[':-3,
      ']':3,
  }
  let stack=[]
  for(let i=0;i<s.length;i++){
      if(mapp[s[i]]<0){
          stack.push(s[i])
      }
      else{
          let last=stack.pop()
          if(mapp[last]+mapp[s[i]]!=0){
              return false
          }
      }
  }
  if (stack.length>0){
      return false
  }
  return true
};
```

# 树

## 树与列表转换

list转化为树

```JS
// 原始 list 如下
let list =[
    {id:1,name:'部门A',parentId:0},
    {id:2,name:'部门B',parentId:0},
    {id:3,name:'部门C',parentId:1},
    {id:4,name:'部门D',parentId:1},
    {id:5,name:'部门E',parentId:2},
    {id:6,name:'部门F',parentId:3},
    {id:7,name:'部门G',parentId:2},
    {id:8,name:'部门H',parentId:4}
];
const result = convert(list, ...);
```

- 思路，检查父id对应的对象，是否有children属性，有则直接push进去，没有则创建，list里的item本身就是一个引用类型，所以obj的item改变，list的item也会改变，最终list

```js
const result = convert(list);
function convert(list) {
  const obj = {}
  const res = []
  list.forEach(item => {
    obj[item.id] = item
  })
  list.forEach(item => {
    if (item.parentId !== 0) {        
      obj[item.parentId]['children'] ? obj[item.parentId]['children'].push(item) : obj[item.parentId]['children'] = [item]
    } else {
      res.push(item)
    }
  })
  return res
}
console.log(result)
```

# 链表

## 1.反转链表

【JZ24】反转链表

![image-20220425013312563](../img/image-20220425013312563.png)

思想：每次把下一个指向next暂存起来避免指向丢失

```js
/*function ListNode(x){
    this.val = x;
    this.next = null;
}*/
function ReverseList(pHead)
{
    let pre = null
    let cur = pHead
    while(cur){
        let next = cur.next
        cur.next = pre
        pre = cur
        cur = next
    }
    return pre
}
```

### 

# 其他

## 1.岛屿数量

https://leetcode.cn/problems/number-of-islands/?favorite=2cktkvj

给你一个由 `'1'`（陆地）和 `'0'`（水）组成的的二维网格，请你计算网格中岛屿的数量。岛屿总是被水包围，并且每座岛屿只能由水平方向和/或竖直方向上相邻的陆地连接形成。

```js
输入：grid = [
  ["1","1","1","1","0"],
  ["1","1","0","1","0"],
  ["1","1","0","0","0"],
  ["0","0","0","0","0"]
]
输出：1
```

题解：

> 岛屿问题，固定套路 Flood fill 算法(在一个区域内，从某个点开始往外扩散找到与其联通的所有点，最终获得一个区域块,实现上通常为 DFS 或 BFS。)
>
> ![image-20230315025109568](../img/image-20230315025109568.png)

```js
function numIslands(grid) {
  const m = grid.length; //行
  const n = grid[0].length; //列
  let count = 0;
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      if (grid[i][j] === '1') { // 发现岛屿的一部分
        count++;
        floodFill(grid, i, j);
      }
    }
  }
  return count;
};

// 深度遍历，找到的每一个为1的一直往深度扩展为1的全变为0，除非触及到了岛屿边界
function floodFill(grid, i, j) {
  const m = grid.length; //  行
  const n = grid[0].length; // 列
  if (i < 0 || i >= m || j < 0 || j >= n) return; // 跑到地图外了
  if (grid[i][j] === '0') return; // 到达岛屿边缘了

  grid[i][j] = '0'; // 淹没
  floodFill(grid, i - 1, j);
  floodFill(grid, i + 1, j);
  floodFill(grid, i, j - 1);
  floodFill(grid, i, j + 1);
}
```

### 岛屿的最大面积

找到给定的二维数组中最大的岛屿面积。如果没有岛屿，则返回面积为 `0` 。

> 计算每次淹没多少个1

```js
var maxAreaOfIsland = function(grid) {
    const m = grid.length;
    const n = grid[0].length;
    let maxNum =0;
    for(let i=0;i<m;i++){
        for(let j=0;j<n;j++){
          if(grid[i][j]===1){
              let areaOnly = fillflood(grid,i ,j)
              maxNum = areaOnly> maxNum? areaOnly: maxNum
          }
        }
    }
    return maxNum
};

function fillflood(grid,i ,j){
    const m = grid.length;
    const n = grid[0].length;
    if(i<0||i>=m || j<0 ||j>=n) return 0;
    if(grid[i][j]=== 0) return 0;
    grid[i][j] = 0
    return fillflood(grid,i-1 ,j)+ fillflood(grid,i+1 ,j)+ fillflood(grid,i ,j-1) + fillflood(grid,i ,j+1)+1
}
```

## .左旋转字符串

【JZ58】S = ”abcXYZdef” , 要求输出循环左移 3 位后的结果，即 “XYZdefabc” ，"aab",10输出"aba"

【思路】：计算需要移动的数量，切割并拼接

```js
function LeftRotateString(str, n)
{
    if (!str){
        return ''
    }
    if(str.length === 0 || n===0){
        return str
    }
    let num = n % str.length
    let leftSubStr = str.slice(0, num)
    let rightSubStr = str.slice(num)
    return rightSubStr+leftSubStr
}
```



## 6.斐波那契数列

（青蛙跳有几种方式）

输入 n ，求斐波那契（Fibonacci）数列的第 n 项

### 递归

```js
var fib = function(n) {
   if(n<=1){
       return n
   }
   return fib(n-1)+fib(n-2)
};
```

### 优化

> f(3)=f(1)+f(2)
>
> f(4)=f(3)+f(2)
>
> 那么中间的f(2)和f(3)可以保存下来，这样复杂度可以达到o(n)

- 注意：每次计算得到sum2都需要取模

```js
var fib = function(n) {
   if(n<=1){
       return n
   }
   let sum1=0;
   let sum2=1;
   let res = 0
   for(let i=2;i<=n;i++){
       res = sum1+sum2
       sum2=sum1
       sum1=res
   }
   return res
};
```


# 其他
## JZ17 打印从1到最大的n位数
> 描述
输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。
```ts
export function printNumbers(n: number): number[] {
    if(n <=0) return []
    const max = 10**n-1
    const res: number[] = []
    for (let i:number =1;i<=max; i++){
        res.push(i)
    }
    return res
}
```
## JZ83 剪绳子（进阶版）
> 给你一根长度为 n 的绳子，请把绳子剪成整数长的 m 段（ m 、 n 都是整数， n > 1 并且 m > 1 ， m <= n ），每段绳子的长度记为 k[1],...,k[m] 。请问 k[1]*k[2]*...*k[m] 可能的最大乘积是多少？例如，当绳子的长度是 8 时，我们把它剪成长度分别为 2、3、3 的三段，此时得到的最大乘积是 18 。
+ 动态规划<有其他解法更快，但是不太好理解>

```ts
export function cutRope(number: number): number {
    // 边界情况
    if (number < 2) return 0;
    if (number === 2) return 1;
    if (number === 3) return 2;

    // 创建一个数组来存储子问题的最优解
    const products = new Array(number + 1);

    // 0、1、2和3长度的绳子的最大乘积已知
    products[0] = 0;
    products[1] = 1;
    products[2] = 2;
    products[3] = 3;

    // 从底向上计算每个子问题的最优解
    for (let i = 4; i <= number; i++) {
        let max = 0;
        // j表示剪掉的绳子的长度
        for (let j = 1; j <= Math.floor(i / 2); j++) {
            const product = products[j] * products[i - j];
            if (product > max) {
                max = product;
            }
        }
        products[i] = max;
    }

    // 最终结果存储在products[n]中
    return products[number] % 998244353;
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