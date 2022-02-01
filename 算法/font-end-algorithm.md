---
title: 前端校招-------算法题
date: 2021-02-15 22:03:11
tags: 算法
---
**算法：**

## **1、一个DOM树，其中有两个节点，找出这两个节点公共的父节点？**

遍历父结点将父结点存入数组，然后找两个数组中第一个相同元素



## **2、如何反转二叉树？（镜像二叉树）**

```
function Mirror(root){  

   // write code here

   if(root === null) {  

		return  ;

   }  

let temp = root.left;  

root.left = root.right; 

root.right = temp;  

Mirror(root.left);

Mirror(root.right);

}
```



## **3、 进程和线程的区别**

根本区别：进程是操作系统资源分配的基本单位，而线程是任务调度和执行的基本单位

在开销方面：每个进程都有独立的代码和数据空间（程序上下文），程序之间的切换会有较大的开销；线程可以看做轻量级的进程，同一类线程共享代码和数据空间，每个线程都有自己独立的运行栈和程序计数器（PC），线程之间切换的开销小。

所处环境：在操作系统中能同时运行多个进程（程序）；而在同一个进程（程序）中有多个线程同时执行（通过CPU调度，在每个时间片中只有一个线程执行）

内存分配方面：系统在运行的时候会为每个进程分配不同的内存空间；而对线程而言，除了CPU外，系统不会为线程分配内存（线程所使用的资源来自其所属进程的资源），线程组之间只能共享资源。

包含关系：没有线程的进程可以看做是单线程的，如果一个进程内有多个线程，则执行过程不是一条线的，而是多条线（线程）共同完成的；线程是进程的一部分，所以线程也被称为轻权进程或者轻量级进程。



## **4、如果需要对数据进行插入和删除，什么数据结构最快**

链表



## **5、如果还需要考虑数据的可索引性质，什么数据结构最好**

哈希表、B+树

B+树：

B+ 树是一种树数据结构，是一个n叉树，每个节点通常有多个孩子，一颗B+树包含根节点、内部节点和叶子节点。根节点可能是一个叶子节点，也可能是一个包含两个或两个以上孩子节点的节点。  

**B+ 树通常用于数据库和操作系统的文件系统中。**  

B+ 树元素自底向上插入。  



![image-20190904001918142](/Users/caiyifan/Library/Application Support/typora-user-images/image-20190904001918142.png)

所有的叶子结点中包含了全部关键字的信息，及指向含有这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大的顺序链接。(而B 树的叶子节点并没有包括全部需要查找的信息)  

所有的非终端结点可以看成是索引部分，结点中仅含有其子树根结点中最大（或最小）关键字。(而B 树的非终节点也包含需要查找的有效信息)

1) 有n棵子树的结点中含有n个关键字； (B~树是n棵子树有n+1个关键字)

2) 所有的叶子结点中包含了全部关键字的信息，及指向含有这些关键字记录的指针，且叶子结点本身依关键字的大小自小而大的顺序链接。 (B~树的叶子节点并没有包括全部需要查找的信息)

3) 所有的非终端结点可以看成是索引部分，结点中仅含有其子树根结点中最大（或最小）关键字。 (B~树的非终节点也包含需要查找的有效信息)

B+树的有效内容均在叶子节点，B-树的有效内容不全在叶子节点上

B+树的头指针有两个，一个指向根节点，另一个指向关键字最小的元素，因此B+树有两种遍历的方式：

1.从根节点开始随机查询

2.从最小关键词顺序查询



##  6介绍下深度优先遍历和广度优先遍历，如何实现？

/*深度优先遍历三种方式*/

```
let deepTraversal1 = (node, nodeList = []) => {
  if (node !== null) {
    nodeList.push(node)
    let children = node.children
    for (let i = 0; i < children.length; i++) {
      deepTraversal1(children[i], nodeList)
    }
  }
  return nodeList
}
```

```
let deepTraversal2 = (node) => {
    let nodes = []
    if (node !== null) {
      nodes.push(node)
      let children = node.children
      for (let i = 0; i < children.length; i++) {
        nodes = nodes.concat(deepTraversal2(children[i]))
      }
    }
    return nodes
  }
```

```
// 非递归
let deepTraversal3 = (node) => {
  let stack = []
  let nodes = []
  if (node) {
    // 推入当前处理的node
    stack.push(node)
    while (stack.length) {
      let item = stack.pop()
      let children = item.children
      nodes.push(item)
      for (let i = children.length - 1; i >= 0; i--) {
        stack.push(children[i])
      }
    }
  }
  return nodes
}
```

//广度优先遍历

```
let widthTraversal2 = (node) => {
  let nodes = []
  let stack = []
  if (node) {
    stack.push(node)
    while (stack.length) {
      let item = stack.shift()
      let children = item.children
      nodes.push(item)
      for (let i = 0; i < children.length; i++) {
        stack.push(children[i])
      }
    }
  }
  return nodes
}
```

## 7、斐波那契数列

```
function Fibonacci(n)
{
    // write code here
    if(n<1){
        return 0;
    }
    if(n==1||n==2){
        return 1;
    }

    var f1 = 1;
    var f2 = 1;
    var temp;
    for(let i=2;i<n;i++){
        temp =f2
        f2 = f1+f2;
        f1 =temp;
}
return f2;

}
```

## 8、青蛙跳台阶

```
function jumpFloor(number)
{
    if(number<=0){
        return 0;
    }else if(number==1){
        return 1;
    }else if(number==2){
        return 2;
    }else{
        var f1=1;
        var f2=2;
        var fn;
        for(var i=2;i<number;i++){
            fn=f1+f2;
            f1=f2;
            f2=fn;
        }
        return fn;
    }
}
```



## 9、变态跳台阶

```
function jumpFloorII(n)
{
    // write code here
    if(n<=0){
        return 0;
    }
    if(n==1){
        return 1;
    }
    if(n==2){
        return 2;
    }
    return 2*jumpFloorII(n-1);
}
```



## 9、求树的深度

```
function TreeDepth(pRoot){
    if(pRoot){
        var right = 1 +TreeDepth(pRoot.right);
        var left = 1 +TreeDepth(pRoot.left);
    }else{
        return 0;
    }
}
```

## 10、树的遍历

```
function DLR(root){
    if(root){
        console.log(root.val)
    }
    if(root.left){
        DLR(root.left);
    }
    if(root.right){
        DLR(root.right);
    }
}
//前序遍历
function LDR(root){
    if(root.left){
        DLR(root.left);
    }
    if(root){
        console.log(root.val)
    }
    if(root.right){
        DLR(root.right);
    }
}
//中序遍历
function LRD(root){
    if(root.left){
        DLR(root.left);
    }
    if(root.right){
        DLR(root.right);
    }
    if(root){
        console.log(root.val)
    }
}
//后序遍历
```

## 11、JavaScript版本号对比

 * ```
   /*
   
   - 版本号比较方法
   - 传入两个字符串，当前版本号：curV；比较版本号：reqV
   - 调用方法举例：compare("1.1","1.2")，将返回false
     */
     function compare(curV,reqV){
     if(curV && reqV){
        //将两个版本号拆成数字
        var arr1 = curV.split('.'),
            arr2 = reqV.split('.');
        var minLength=Math.min(arr1.length,arr2.length),
            position=0,
            diff=0;
        while(position<minLength && ((diff=parseInt(arr1[position])-parseInt(arr2[position]))==0)){
            position++;
        }
        diff=(diff!=0)?diff:(arr1.length-arr2.length);
        //若curV大于reqV，则返回true
        return diff>0;
    }else{
      //输入为空
      console.log("版本号不能为空");
      return false;
   }
   }
   ```

## 最短路径

##### 1、Dijkstra 算法

```
const INF = Number.MAX_SAFE_INTEGER;
// 查找最近的点
const minDistance = (dist, visited) => {
  let min = INF;
  let minIndex = -1;
  for (let v = 0; v < dist.length; v++) {
    if (visited[v] === false && dist[v] <= min) {
      min = dist[v];
      minIndex = v;
    }
  }
  return minIndex;
};
/** 
 * @param {图：邻接矩阵} graph 
 * @param {顶点索引} src 
 */
export const dijkstra = (graph, src) => {
  const dist = [];
  // 用于标识顶点 src 至其他顶点的距离是否确定
  const visited = [];
  const { length } = graph; 
  for (let i = 0; i < length; i++) {
    dist[i] = INF;
    visited[i] = false;
  }
  dist[src] = 0;
  for (let i = 0; i < length - 1; i++) {
    const u = minDistance(dist, visited);
    // 标识顶点 src 到此顶点的距离已经确认
    visited[u] = true;
    for (let v = 0; v < length; v++) {
      if (!visited[v] && graph[u][v] !== 0 && dist[u] !== INF && dist[u] + graph[u][v] < dist[v]) {
        // 更新 dist
        dist[v] = dist[u] + graph[u][v];
      }
    }
  }
  return dist;
};

// test 
const graph = [
    [0, 2, 4, 0, 0, 0],
    [0, 0, 2, 4, 2, 0],
    [0, 0, 0, 0, 3, 0],
    [0, 0, 0, 0, 0, 2],
    [0, 0, 0, 3, 0, 2],
    [0, 0, 0, 0, 0, 0]
];
```

##### 2、Floyd-Warshall 算法

```
export const floydWarshall = graph => {
  const dist = [];
  const { length } = graph;
  // 初始化邻接矩阵
  for (let i = 0; i < length; i++) {
    dist[i] = [];
    for (let j = 0; j < length; j++) {
      if (i === j) {
        dist[i][j] = 0;
      } else if (!isFinite(graph[i][j])) {
        dist[i][j] = Infinity;
      } else {
        dist[i][j] = graph[i][j];
      }
    }
  }
  for (let k = 0; k < length; k++) {
    for (let i = 0; i < length; i++) {
      for (let j = 0; j < length; j++) {
        // 如果经过下标为 k 顶点路径比原两点间路径更短，当前两点间权值设为更小的一个
        if (dist[i][k] + dist[k][j] < dist[i][j]) {
          dist[i][j] = dist[i][k] + dist[k][j];
        }
      }
    }
  }
  return dist;
};

// test
const INF = Infinity;
const graph = [
  [INF, 2, 4, INF, INF, INF],
  [INF, INF, 2, 4, 2, INF],
  [INF, INF, INF, INF, 3, INF],
  [INF, INF, INF, INF, INF, 2],
  [INF, INF, INF, 3, INF, 2],
  [INF, INF, INF, INF, INF, INF]
];

dist = floydWarshall(graph);

let s = '';
for (let i = 0; i < dist.length; ++i) {
  s = '';
  for (var j = 0; j < dist.length; ++j) {
    if (dist[i][j] === INF) s += 'INF ';
    else s += dist[i][j] + '   ';
  }
  console.log(s);
}
```

## 逆波兰式

中缀转后缀的大体思路为：

首先我们要建立两个数组对象； 一个用于存储符号表达式，另一个则用来记录后缀表达式元素。另外这两个数组采用push, pop的方式模拟栈的行为。

```
var symbol = ['#', '(', '+', '-', '*', '/', ')']
var symbolPriority = {
  '#': 0,
  '(': 1,
  '+': 2,
  '-': 2,
  '*': 3,
  '/': 3,
  ')': 4
}
```

// 如果遇到左括号则直接入栈
// 如果遇到右括号,则弹出站内只到出现左括号为止
// 如果站外操作符的优先级高于站内的优先级则入栈
// 如果栈外的操作符优先级低于或等于栈内的优先级，输出栈内的符号，并入栈栈外的符号
// 中缀表达式遍历完成，但是栈中还有符号存在，一一出栈输出

```
function operaSymbol (char, symArr, resArr) {
  var lastChar = symArr[symArr.length - 1]
  if (!lastChar) {
    symArr.push(char)
    return
  }
  if (char === '(') {
    symArr.push(char)
  } else if (char === ')') {
    var curChar = symArr.pop()
    while (symArr && curChar != '(') {
      resArr.push(curChar)
      curChar = symArr.pop()
    }
  } else if (symbolPriority[char] > symbolPriority[lastChar]) {
    symArr.push(char)
  } else if (symbolPriority[char] <= symbolPriority[lastChar]) {
    while (lastChar && (symbolPriority[char] <= symbolPriority[lastChar])) {
      var curChar = symArr.pop()
      resArr.push(curChar)
      lastChar = symArr[symArr.length - 1]
    }
//      operaSymbol(char, symArr, resArr)
    symArr.push(char)
  } else {
    symArr.push(char)
  }
}
function toSuffixExpre (str) {
  var resArr = []
  var symArr = []
  // 用于记录数字
  var substr = ''
  for (var i = 0, len = str.length; i < len; i++) {
    // 判断是否是符号
    if(symbol.includes(str[i])) {
      resArr.push(substr)
      substr = ''
      operaSymbol(str[i], symArr, resArr)
    } else {
      substr += str[i]
      // resArr.push(str[i])
    }
  }
  resArr.push(substr)
  while (symArr.length > 0) {
    var curChar = symArr.pop()
    resArr.push(curChar)
  }
  var result = resArr.join(' ')
  return result
}
var test1 = '1+(2-3)*4+10/5'
console.log('the success is 1 2 3 - 4 * + 10 5 / +')
var result1 = toSuffixExpre(test1)
console.log(result1)
var test2 = 'A+(B-C/D)*E'
console.log('the success is ABCD/-E*+')
var result2 = toSuffixExpre(test2)
console.log(result2)
var test3 = '9+3+6*9-7'
var result3 = toSuffixExpre(test3)
console.log(result3)


```



## 稀疏数组和普通数组区别

所谓稀疏数组就是数组中大部分的内容值都未被使用（或都为零），在数组中仅有少部分的空间使用。因此造成内存空间的浪费，为了节省内存空间，并且不影响数组中原有的内容值，我们可以采用一种压缩的方式来表示稀疏数组的内容。 

## 二进制的原码补码，位运算思路；

反码=原码取反

补码=反码+1

## 要求设计 LazyMan 类，实现以下功能

```
LazyMan('Tony');
// Hi I am Tony

LazyMan('Tony').sleep(10).eat('lunch');
// Hi I am Tony
// 等待了10秒...
// I am eating lunch

LazyMan('Tony').eat('lunch').sleep(10).eat('dinner');
// Hi I am Tony
// I am eating lunch
// 等待了10秒...
// I am eating diner

LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(10).eat('junk food');
// Hi I am Tony
// 等待了5秒...
// I am eating lunch
// I am eating dinner
// 等待了10秒...
// I am eating junk food

class LazyManClass {
    constructor(name) {
        this.taskList = [];
        this.name = name;
        console.log(`Hi I am ${this.name}`);
        setTimeout(() => {
            this.next();
        }, 0);
    }
    eat (name) {
        var that = this;
        var fn = (function (n) {
            return function () {
                console.log(`I am eating ${n}`)
                that.next();
            }
        })(name);
        this.taskList.push(fn);
        return this;
    }
    sleepFirst (time) {
        var that = this;
        var fn = (function (t) {
            return function () {
                setTimeout(() => {
                    console.log(`等待了${t}秒...`)
                    that.next();
                }, t * 1000);  
            }
        })(time);
        this.taskList.unshift(fn);
        return this;
    }
    sleep (time) {
        var that = this
        var fn = (function (t) {
            return function () {
                setTimeout(() => {
                    console.log(`等待了${t}秒...`)
                    that.next();
                }, t * 1000); 
            }
        })(time);
        this.taskList.push(fn);
        return this;
    }
    next () {
        var fn = this.taskList.shift();
        fn && fn();
    }
}
function LazyMan(name) {
    return new LazyManClass(name);
}
LazyMan('Tony').eat('lunch').eat('dinner').sleepFirst(5).sleep(4).eat('junk food');
```

