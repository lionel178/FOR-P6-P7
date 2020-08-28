# 章节笔记

## 42 那些年常考的前端算法（上）

### 热身：数组乱序

洗牌算法：在数组末尾选取一个元素作为指针，再随机从前面选取一个元素和它交换，完成后指针前移

```js
Array.prototype.shuffle = function () {
  var arr = this;
  var m = arr.length,
    i;
  while (m) {
    i = Math.floor(Math.random() * m--);
    [arr[m], arr[i]] = [arr[i], arr[m]];
  }
  return arr;
};
```

### 算法的基本概念

#### 时间复杂度

通常认为：

- 没有循环语句，时间复杂度记作 O(1)，也称为常数阶
- 只有一重循环，那么算法的基本操作的执行频率与问题规模 n 呈线性增大关系，记作 O(n)
  ，也叫线性阶

那么如何让时间复杂度的计算“有理可依”呢？也有几个原则：

- 只看循环次数最多的代码
- 加法法则：总复杂度等于量级最大的那段代码的复杂度
- 乘法法则：嵌套代码的复杂度等于嵌套内外复杂度的乘积

#### 空间复杂度

## 43 那些年常考的前端算法（中）

闲谈：v8 引擎中采用了 Timsort 的全新算法来实现 sort

### 快速排序

快速排序的特点就是分治，实现思路就是随机选取一个基准点，然后小的放在左边数组，大的放在右边数组，最后合并起来

最简单的实现

```js
const quickSort = (arr) => {
  if (arr.length < 2) return arr;

  const pivot = arr[Math.floor(Math.random() * arr.length)];

  const left = [];
  const middle = [];
  const right = [];

  for (let i = 0; i < arr.length; i++) {
    const value = arr[i];
    if (value < pivot) {
      left.push(value);
    }

    if (value === pivot) {
      middle.push(value);
    }

    if (value > pivot) {
      right.push(value);
    }
  }

  return quickSort(left).concat(middle, quickSort(right));
};
```

优化方向有：在原数组上操作、尾递归调用优化、基准点选取优化

### 插入排序

插入排序可以理解为整理扑克牌, 抽出一张牌，比它大的后移一位，知道前一位没它大，就插在那里

```js
const insertSort = (arr) => {
  const len = arr.length;
  let preIndex;
  let current;

  for (let i = 1; i < len; i++) {
    preIndex = i - 1;
    current = arr[i]; // 抽牌

    while (preIndex >= 0 && arr[preIndex] > current) {
      arr[preIndex + 1] = arr[preIndex]; // 把大的排往后让一让
      preIndex--;
    }

    arr[preIndex + 1] = current; // 最后一张没它大的，就插进去
  }

  return arr;
};
```

优化方向：往前插的时候可以采用二分法（因为前面已经是有序的了），改成链表插入

### v8 Timsort 的实现

这里太难了，我暂时选择放弃

### 实战例题 1：交换星号

题目：一个字符串中只包含 _ 和 数字，请把 _ 号都放开头

思路：使用双指针，从后往前扫字符串，遇到数字则赋值给后面的指针，继续往后扫，遇到 \* 则不处理

```js
const isNumeric = (n) => !isNaN(parseFloat(n)) && isFinite(n);

const solution = (s) => {
  const n = s.length;
  let arr = s.split('');
  let j = n - 1;

  for (let i = n - 1; i >= 0; --i) {
    if (isNumeric(arr[i])) {
      arr[j--] = arr[i];
    }
  }

  for (; j >= 0; --j) {
    arr[j] = '*';
  }
  return arr.join('');
};
```

### 实战例题 2： 最长不重复子串

题目：给定一个字符串，返回它最长的不包含重复的子串长度。例如输入 abcabcbb 输出 3（对应 abc）

思路：
- 暴力枚举起点和重点，并判断重复，时间复杂度是 O(n²)
- 通过双指针、滑动窗口，动态维护窗口[i..j)，使窗口内不重复

## 44 那些年常考的前端算法（下）
