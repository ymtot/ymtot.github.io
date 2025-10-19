---
layout: post
title:  "基于Python的数独直观技巧实现"
date:   2025-10-19 19:22:21 +0800
categories: Sodoku python
---


# 基于Python的数独直观技巧实现

> 很久之前写的

## 目录

[TOC]

## 关于数独你需要知道的[^1]

### 数独规则说明

什么是数独？数独是一种益智游戏，在空格里填入数字 1 到 9，使得每一行、每一列和 每一个用粗线围起来的 3×3 的九个单元格里，填数都包含 1 到 9 各一个。换句话说，没有 重复的数字出现。比如下面这个答案所给出的样子：

![1760866128](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191735777.png)

可以从图上看出，每一行、列和粗线围起来的区域（称为宫，Block），都有 9 个单元格， 并且每一组这样的9个单元格都不含有相同的数字，都是1到9各有一个，不多也不少。 这便是数独的规则。规则简单。

不过我们从规则里看不出什么比较有意义和实质性的东西，所以这里需要继续提及一些内容。

### 解的唯一性

需要注意的是，每一个题目都只有唯一的解（Solution）。所谓的解，也就是答案。每一个合适的题目都必须拥有唯一的答案。换而言之，每一个空格的填法都只有唯一的一个。但凡拥有某个（或某些）单元格出现多出一种填法的话，都算作多解题，这种题目不是合法的。 例如上题，就是一个合格的题目，答案是唯一的；另外，如果一眼就能看到题目所给的数字 不满足数独规则，或者是经过一部分推理逻辑得到一些填数并保证这些数字是正确的填入 后，此时出现不满足数独规则的情况，那么这种题目叫无解题，即不可能有解的题目。

### 基本表示手段

我们使用 r、c、b 和一个 1 到 9 的数字来表示行、列、小九宫格（我们称为宫，Block）， 数字是几，那就是顺数第几个。比如 r1，就表示顺数第一行；c6 则表示顺数的第六列；b4 则表示顺数的第四个宫。其中宫的编号方式是从左到右、从上到下。

### 直观技巧

数独技巧很多，可以分为直观技巧，和候选数技巧。直观法就是不需要任何辅助工具，从接到数独谜题的那一刻起就可以立即开始解题。使用候选数法解数独题目需先建立候选数列表，根据各种条件，逐步安全的清除每个宫格候选数的不可能取值的候选数，从而达到解题的目的。接下来介绍直观技巧。

#### 排除法（Hidden Single）

我们可以从规则直接得到这种技巧的推理逻辑。 试想一下，规则要求每一个区域必须填入 1 到 9 各一个，这也就意味着每一个区域里必须包含数字 1 到 9，也就意味着每一个区域的 1 到 9 都不可以缺失任意一个数。这便产生了这个技巧的相关内容。

> 排除法也称为摒除法

##### 宫排除(Hidden Single in Block)

看例子：

![1760866146](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191736342.png)

如图所示，观察 b3，发现 b3 一共有四处空格。不过，可以观察到，数字 8 只有唯一 一处可填位置 r3c7。首先，r23c8 不允许填 8 的原因是，r8c8 是 8，这样 c8 上的其它 任意位置就不再允许填入数字 8，当然也包括 r23c8；同理，由于 r4c9 是 8 的缘故，这 使得 r2c9 不能是 8，毕竟 r4c9 和 r2c9 同列（同列不能出现两个相同数字）。

这样便排除了三种填数可能，故只能使得 r3c7 = 8，毕竟刚才说过，1 到 9 必须都 得出现一个，要是数字 8 没有出现在 b3 里，b3 就必须存在有不是 8 的其它数字出现至少 两个，这便违背了数独规则，因而 r3c7 还真的是不得不填 8 了。

##### 行列排除（Hidden Single in Line）

因为区域分行、列、宫三种，所以既然有宫排除，就应该有行排除和列排除。行排除和 列排除统称行列排除（Hidden Single in Line），不过有时候也叫做线性排除，因为它们的排 除效果都是“一整条线”形式的排除。

与宫排除道理一样，看图。

行排除（Hidden Single in Row）

![1760866151](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191736806.png)

列排除（Hidden Single in Column）

![1760866156](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191736774.png)

> 宫排除更常用，由于宫的“形状”的优势，它比起行和列都容易观察一些。更好聚焦。

#### 唯一余数（Naked Single）

和排除不同，唯一余数（Naked Single）这种技巧并非针对行、列、宫作排除，而相当于针对单元格作排除。

![](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191737130.png)

如图所示，仔细观察 r6c9 一格。数数 r6c9 能填哪些数字。数字 1 不行，原因是 r7c9 是 1；数字 2 不行，原因是 r1c9 是 2（或者你看 r5c7 和 r6c5 也可以）；数字 3 不行 （r3c9）、数字 4 不行（r4c9）、数字 5 不行（r6c4）、数字 6 不行（r4c7 或 r6c6）、数 字 7 不行（r6c3）、数字 9 也不行（r4c1）。 那么，既然 1、2、3、4、5、6、7、9 都不行，那只能填 8 了，所以 r6c9 = 8。这 便是这个技巧所得到的结论。

> 唯一余数简称唯余
>
> 在实战中不好观察，也会和**区块**一起使用

唯一余数需要对数字十分敏感，我也做了相应的练习程序。

#### 区块（Locked Candidates）

> 准确来说它是一种结构

##### 宫区块（Pointing）

看例子

###### 宫区块+排除

![1760866166](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191737252.png)

黄色部分就是一个**区块**，它表示只有这两格必须有一个3，才导致c1列排除顺利进行。

既然有区块排除，那就有区块唯一余数，下面我们来看第二则示例。

###### 宫区块 + 唯余

![1760866174](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191738626.png)

黄色部分表示这两格必须出现一个2，才导致绿色8出数这么顺利。

##### 行列区块（Claiming）

###### 行区块+排除

![1760866179](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191738889.png)

黄色是1的区块（r7）导致c5排除才顺利出数。保证了红色不是1.

> 可以看到宫区块同行列，行列区块同宫，它们的目的都是一样的，就是**删数**

### 说明

什么是候选数，候选数是每一单元格，在保证规则的前提下，在已知的数的约束下，计算出的可能填的数。

![1760866184](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191738718.png)

在数独中很多技巧都是候选数技巧。大部分题目仅通过直观技巧是很难解决的。越是难题直观技巧用的越少。

什么是**出数（Assignment）**，什么是**删数（Elimination）**。**出数**指的是能 够得出填数结论的行为，比如之前的技巧，利用区块和数组来达到排除和唯一余数的结论， 这种就叫出数结论；而删数，则是针对于候选数而言。得到的推理结论仅仅是排除一个候选 数情况的行为（我们称为删除或删减情况，所以叫做删数，而不是“排数”）。

## 程序功能

1. 利用直观技巧解数独
2. 利用DLX暴力求解数独
3. 打印数独
4. 唯余练习
5. 出题

## 程序设计思路
### 直观技巧解数独

#### 准备工作


使用numpy处理

```python
# 建立模型 以0代替空位
x = np.array([
        [0,9,3,0,0,0,7,0,0],
        [0,1,4,0,0,0,0,9,5],
        [7,0,0,8,0,0,0,0,4],
        [0,8,0,0,0,5,1,0,0],
        [1,0,6,4,0,0,0,0,0],
        [4,0,7,0,8,1,0,0,0],
        [6,4,0,0,5,0,0,1,0],
        [0,0,0,0,6,0,0,0,9],
        [0,0,0,0,0,7,0,6,0]
        ])
    x = Sodoku(x)
#数独类
class Sodoku:
    def __init__(self,data:np.array):
        self.inition_grid = data
        self.lock_num = []   # 用于处理区块
        self.lock_temp = []
    @staticmethod
    def __deal_B__(i:int):
        """
        返回该宫中所有坐标
        :param i: 宫
        :return: list
        """
        temp = []
        if i in [1, 2, 3]:
            for k in range(3):
                for j in range(3 * (i - 1), 3 * i):
                    temp.append((k, j))
        if i in [4, 5, 6]:
            for k in range(3, 6):
                for j in range(3 * (i - 4), 3 * (i - 3)):
                    temp.append((k, j))
        if i in [7, 8, 9]:
            for k in range(6, 9):
                for j in range(3 * (i - 7), 3 * (i - 6)):
                    temp.append((k, j))
        return temp
    def r(self,n:int):
        """
        输出该行所有数
        :param n: 行
        :return:
        """
        return self.inition_grid[n-1]
    def c(self,n:int):
        """
        列
        :param n:列
        :return:
        """
        return self.inition_grid[:,n-1]
    def b(self,n:int):
        """
        宫
        :param n:
        :return:
        """
        if 0<n<4:                   				# 使用numpy精准切片 切出宫来
            temp = self.inition_grid[0:3,3*n-3:3*n]
        elif 3<n<7:
            temp = self.inition_grid[3:6,3*n-12:3*n-9]
        else:
            temp = self.inition_grid[6:9,3*n-21:3*n-18]
        return temp
```

直观技巧的实现都是是基于每个单元格的候选数来完成的。所以要先计算数每个单元格的候选数。

```python
    def show_numn(self):
        """
        返回数独空位的所有坐标
        :return:
        """
        temp = self.inition_grid == 0   #numpy
        temp = list(zip(temp.nonzero()[0],temp.nonzero()[1])) 
        return temp
    def show_candidate(self):
        """
        按照 show_num 返回的顺序输出 对应位置的 候选数
        :return:
        """
        temp = []
        set0 = {1,2,3,4,5,6,7,8,9}
        for i,j,k in zip(self.show_row(),self.show_column(),self.show_block()):
            temp0 = []		#temp0 用于处理删数
            for s,t in zip(self.lock_num,self.lock_temp):
                if (i-1,j-1) == s:
                    temp0.append(t)
            set1 = set0-set(self.r(i))-set(self.c(j))-set(self.b(k).flatten())-set(temp0)
            temp.append(list(set1))			# 用set来完成
        return temp
```

之后在做一些工作

```python
    def show_block(self): #按照show_num返回的顺序 输出对应位置  是那一宫
        temp = []
        for i in self.show_numn():
            for j in range(1,10):
                if i in self.deal_B(j):
                    temp.append(j)
        return temp
    def show_row(self):
        """
        按照 show_num 返回的顺序 输出对应位置的 行数
        :return:
        """
        temp = []
        for j,k in self.show_numn():
            temp.append(j+1)
        return temp
    def show_column(self):
        """
        按照 show_num返回的顺序 输出对应位置的 列数
        :return:
        """
        temp = []
        for j,k in self.show_numn():
            temp.append(k+1)
        return temp
```

这样等到 show_num , show_block , show_row , show_column , show_candidate 五个和空位有关的list

分别输出这五个

![1760866196](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191742902.png)

![1760866204](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191742694.png)

利用这五个list完成模拟直观技巧，它们之间有很好的对应关系。

#### 摒除法实现

以宫摒除为例：

我们先以候选数视角看宫排除，他展现在在某一宫内所有候选数中只出现了一次的数。所以：

对每一宫扫描宫里的所有空位，统计出所有候选数，以及每个数出现的次数，当一个数出现的次数为1时可以摒除，位置出数，宫摒除完成。行列排除同理。

```flow
st=>start: Start
oop=>operation: 将某一个宫的所有候选数切出来
					扁平化统计次数
cond=>condition: 当有次数为1时
op=>operation: 找出这个数是谁的，出数
end=>end: End
st->oop->cond
cond(yes)->op->end
cond(no)->end
```

#### 唯余法实现

这个简单。		

遍历所有空位的候选数，当只有一个候选数时，该位置出数。

#### 区块实现

以候选数视角看区块，区块描述的是某一行列宫中只有两个特殊位置能填某个数，从而达到删数的目的。

以宫区块为例：

​		对每一宫遍历宫里所有空位，统计出所有候选数，以及每个数出现的次数，当一个数出现的次数为2时，进一步操作返回出这两个位置坐标，判断这两个是否时同行列，如果同行列，返回改行列号，对该行列进行统计如果不能进一步删数，则无效，如果可以删数，有效并输出。

```flow
st=>start: Start
oop=>operation: 将某一个宫的所有候选数切出来
					扁平化统计次数
cond=>condition: 如果有次数为2
op=>operation: 找出这两格
cond1=>condition: 判断这两格是否同行列
cond2=>condition: 判断是否有效
op1=>operation: 该区块有效，先输出
op2=>operation: 该区块无效
end=>end: End
st(right)->oop(right)->cond(right)
cond(yes)->op->cond1
cond1(yes)(right)->cond2
cond2(yes)(right)->op1(right)->end
cond2(no)->op2(right)->end
cond1(no)->end
cond(no)->end
```

#### 区块和技巧组合实现

##### 区块+排除

```flow
st=>start: Start
op=>operation: 前提：排除法已经不能出数了
oop=>operation: 根据有效区块对盘进行删数处理
cond=>condition: 排除法可出数
op1=>operation: 说明"区块+排除"技巧实现
end=>end: End
st(right)->op(right)->oop->cond
cond(yes)->op1->end
cond(no)->end
```



同理也可以实现“区块+唯余”的技巧

#### 直观技巧主算法

主要是联系普通人做题的思路，以宫排除为主（因为好观察），当数字填入较多时考虑唯余法，遇到填不下去时再考虑区块。

```flow
st=>start: Start
op=>operation: 宫排除（达到一定次数）
op1=>operation: 根据现有盘一次 唯余
op2=>operation: 根据现有盘一次 行列排除
op3=>operation: 如果上面所有操作都没有出数,区块技巧一次
op4=>operation: 如果还是没有出数，完成盘面,结束
end=>end: End
st(right)->op(right)->op1(right)->op2->op3->op4->end
```



### 暴力求解数独

#### DLX算法简介

舞蹈链（Dancing Links）算法是用来解决**精准覆盖**问题。

精准覆盖问题定义：给定一个由0-1组成的矩阵，是否能找到一个行的集合，使得集合中每一列都恰好包含一个1。

$$
\begin{pmatrix}
  0& 0 & 1 & 0 & 1 & 1 & 0\\
 1 & 0 & 0 & 1 & 0 & 0 & 1\\
  0&  1& 1 & 0 & 0 & 1 & 0\\
 1 &  0&  0&  1& 0 & 0 & 0\\
 0 &  1&  0&  0&  0& 0 & 1\\
  0&  0&  0&  1&  1&  0& 1\\
\end{pmatrix}
$$
算法主要是基于**交叉十字循环双向链**的数据结构来完成的。

#### 数独问题转化为精准覆盖问题[^3]

首先看看数独问题（9*9的方格）的规则

1、每个格子只能填一个数字

2、每行每个数字只能填一遍

3、每列每个数字只能填一遍

4、每宫每个数字只能填一遍

换个说法就是

1、每个格子只能填一个数字

2、每行1-9的这9个数字都得填一遍（也就意味着每个数字只能填一遍）

3、每列1-9的这9个数字都得填一遍

4、每宫1-9的这9个数字都得填一遍

按照这样

第1列定义成：（1，1）填了一个数字

第2列定义成：（1，2）填了一个数字

……

第9列定义成：（1，9）填了一个数字

第10列定义成：（2，1）填了一个数字

……

第18列定义成：（2，9）填了一个数字

……

第81列定义成：（9，9）填了一个数字

1~81列就完成了**约束条件1：每个格只能填一个数字**

第82列定义成：在第1行填了数字1

第83列定义成：在第1行填了数字2

……

第90列定义成：在第1行填了数字9

第91列定义成：在第2行填了数字1

……

第99列定义成：在第2行填了数字9

……

第162列定义成：在第9行填了数字9

至此，用第82-162列（共81列）完成了**约束条件2：每行1-9的这9个数字都得填一遍**

第163列定义成：在第1列填了数字1

第164列定义成：在第1列填了数字2

……

第171列定义成：在第1列填了数字9

第172列定义成：在第2列填了数字1

……

第180列定义成：在第2列填了数字9

……

第243列定义成：在第9列填了数字9

至此，用第163-243列（共81列）完成了**约束条件3：每列1-9的这9个数字都得填一遍**

第244列定义成：在第1宫填了数字1

第245列定义成：在第1宫填了数字2

……

第252列定义成：在第1宫填了数字9

第253列定义成：在第2宫填了数字1

……

第261列定义成：在第2宫填了数字9

……

第324列定义成：在第9宫填了数字9

至此，用第244-324列（共81列）完成了**约束条件4：每宫1-9的这9个数字都得填一遍**

**有数字的格子**

以例子来说明，在（4，2）中填的是7

把（4，2）中填的是7，解释成4个约束条件

1、在（4，2）中填了一个数字。

2、在第4行填了数字7

3、在第2列填了数字7

4、在第4宫填了数字7

那么这4个条件，分别转换成矩阵对应的列为

1、在（4，2）中填了一个数字。对应的列N=（**4**-1）×9+**2**=29

2、在第4行填了数字7。对应的列N=（**4**-1）×9+**7**+81=115

3、在第2列填了数字7。对应的列N=（**2**-1）×9+**7**+162=178

4、在第4宫填了数字7。对应的列N=（**4**-1）×9+**7**+243=277

于是，（4，2）中填的是7，转成矩阵的一行就是，第29、115、178、277列是1，其余列是0。把这1行插入到矩阵中去。

**没数字的格子**

还是举例说明，在（5，8）中没有数字

把（5，8）中没有数字转换成

（5，8）中填的是1，转成矩阵的一行就是，第44、118、226、289列是1，其余列是0。

（5，8）中填的是2，转成矩阵的一行就是，第44、119、227、290列是1，其余列是0。

（5，8）中填的是3，转成矩阵的一行就是，第44、120、228、291列是1，其余列是0。

（5，8）中填的是4，转成矩阵的一行就是，第44、121、229、292列是1，其余列是0。

（5，8）中填的是5，转成矩阵的一行就是，第44、122、230、293列是1，其余列是0。

（5，8）中填的是6，转成矩阵的一行就是，第44、123、231、294列是1，其余列是0。

（5，8）中填的是7，转成矩阵的一行就是，第44、124、232、295列是1，其余列是0。

（5，8）中填的是8，转成矩阵的一行就是，第44、125、233、296列是1，其余列是0。

（5，8）中填的是9，转成矩阵的一行就是，第44、126、234、297列是1，其余列是0。

把这9行插入到矩阵中。由于这9行的第44列都是1（不会有其他行的44列会是1），也就是说这9行中必只有1行（有且只有1行）选中（精确覆盖问题的定义，每列只能有1个1），是最后解的一部分。这就保证了最后解在（5，8）中只有1个数字。

对于$（i，j）$填的数字为$k$可以知道

$$9(i-1)+j$$

$$9(j-1)+k+81$$

$$9(i-1)+k+81*2$$

$$9(B((i,j))-1)+k+81*3$$

列为1其余都为0，这是可计算的。

这样，从数独的格子依次转换成行（1行或者9行）插入到矩阵中。完成了数独问题到精确覆盖问题的转换。

### 生成数独算法

先是有一个可以生成一列随机数的函数

```python
def __deal_rand__():
    """
    产生一个随机排列的一列数
    :return:
    """
    x = [1,2,3,4,5,6,7,8,9]
    random.shuffle(x)
    return x
```

然后令盘第一行赋为随机一列其余都为0

再在2，3行，4、5、6行，7、8、9行各随机选一行并都赋值。最后检查是否有误（出现同列同宫重复数字的情况）

代码如下

```python
def set_question():
    """
    命一道题
    返回一个Sodoku对象
    :return:
    """
    result = np.int64(np.zeros((9,9)))
    result[0] = __deal_rand__()
    count = 0
    while True:
        result0 = result.copy()
        temp = random.randint(3,5)
        result0[temp] = __deal_rand__()
        temp = random.randint(6,8)
        result0[temp] = __deal_rand__()
        temp = random.randint(1,2)
        result0[temp] = __deal_rand__()
        grid = Sodoku(result0)
        count += 1
        if grid.check():
            break
    for i,j,k in only_solve(grid):
        result0[(i,j)] = k
    temp = random.randint(55,60)
    temp0 = np.ones((9,9))
    temp0 = np.nonzero(temp0)
    temp0 = list(zip(temp0[0],temp0[1]))
    random.shuffle(temp0)
    for i in range(temp):
        result0[temp0[i]] = 0
    result = Sodoku(result0)
    return result  ## #
```

之后让随机生成的仅有4行的盘用暴力求解算法求解出一个终盘。在随机挖空即可。

> 这样就可以获得一个有解的数独题目
>
> 但是，不能保证该题是否是有唯一解
>
> 同时也不能控制题目的难度
>
> 题目的难度和提示数多少没很大关系

### 打印，输入算法

利用excel来完成打印，用到xlwt，xlrd，xlwings这三个库，每个库我只用了一部分内容。

用xlwt来创建一个表格并写入数字，用xlwings来进一步美化表格（主要是设置边框很方便），完成打印，用xlrd来读取表格（用来做一个输入程序）。

#### 为了排版

采取奇数盘前插1列，偶数盘前插2列

![1760866222](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191753137.png)

这样这两个盘正好占一页。由于xlwings只能在一次程序中进行一次操作，第二次操作会取消第一次操作的结果。所以

创建了辅助类

```python
class DealPrint:
    def __init__(self,name):
        self.n = 1      #第几个盘
        self.delt = 0   #位置
        self.name = name#文件名
        self.br = []    #边框序数
        self.gr = []    #加绿区
        self.yl = []    #加黄区
```

所有加边框加绿加黄的操作都在最后打印之前操作。

### 唯余练习

代码如下

```python
import random
import datetime
def __rand__():
    x = list(range(1,10))
    random.shuffle(x)
    return x
def st_game():
    print("-" * 50)
    print("唯余练习 1.0v")
    print("进入后按非要求的字符即可退出")
    print("按任意键开始")
    temp = input("")
    T = 0
    F = 1
    all_time = 0
    ts = 0
    flag = True
    while True:
        print("-" * 50)
        print("正确COR[%d]错误INC[%d]总数TOT[%d]正确率ACC[%0.0f%%]平均T/K[%0.3f]" % (T, F, T + F, T / (T + F), ts))
        if flag:
            F = 0
            flag = False
        temp = random.randint(0, 8)
        x = __rand__()
        temp1 = x[temp]
        x.pop(temp)
        if temp in [0, 2, 4, 5]:
            for i in x:
                print(i, end="  ")
            print("")
        else:
            for i in x:
                print(i)
            print("")
        st = datetime.datetime.now()
        num = input("")
        end = datetime.datetime.now()
        all_time += (end - st).seconds
        try:
            num1 = int(num)
        except:
            break
        if int(num) not in [1, 2, 3, 4, 5, 6, 7, 8, 9]:
            break
        if int(num) == temp1:
            T += 1
        else:
            F += 1
        ts = all_time / (T + F)
    print("-" * 50)
    print("本次成绩：")
    print("正确COR[%d]错误INC[%d]总数TOT[%d]正确率ACC[%0.0f%%]平均T/K[%0.3f]" % (T, F, T + F, T / (T + F), ts))

```



## 结果分析

主菜单正常

![1760866228](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191754149.png)

### 手动录入

选择1

![1760866232](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191754921.png)

输入

> 如果非法输入的格都会被视为空格

![1760866237](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191754969.png)

在run中按下enter，excel自动关闭，进入二级菜单。

![1760866245](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191755509.png)

功能测试

![1760866253](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191755916.png)

![1760866258](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191755600.png)

![1760866263](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191756364.png)

![1760866268](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191756369.png)

![1760866275](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191756037.png)

如果故意输入一个错题会弹回的

![1760866284](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191756592.png)

![1760866289](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191756156.png)

如果是没有解的题在打印答案时会弹出

![1760866296](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191757406.png)

### 自动生成

![1760866305](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191757241.png)

二级菜单功能和手动一样的。

### 打印解题过程

![1760866312](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191757655.png)

排除法的体现

![1760866319](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191758913.png)

![](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759594.png)

![1760866331](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191758083.png)

唯余法体现

![1760866336](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759566.png)

区块技巧的体现

![1760866340](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759238.png)

如果解不了

![1760866345](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759209.png)

如果出现错题



![1760866354](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759712.png)

![1760866358](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191759230.png)

### 唯余练习

![1760866363](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191800618.png)

![](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191800618.png)

### 打印数独

有3个模式

![1760866372](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191800076.png)

先是手动输入，也可以不输

![1760866377](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191800050.png)

![1760866382](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191800382.png)

第一题是我自己输入的，后三个题是自动生成的。

其他模式基本都是这个模式，只是带这答案的，如果手动输入是错题会弹回的，不会写入。

![1760866388](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191801098.png)

![1760866391](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191801124.png)

打印出带答案(一页)的

![1760866396](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191801095.png)

分页

![1760866400](https://cdn.jsdelivr.net/gh/ymtot/picgo@main/imgs/202510191801768.png)

## 源码

由于源码比较多（大概1700+行）这展示部分关键代码

### DLX算法

```python
class CCL_node (object):
    """
    创建双向循环十字链表节点类
    """
    def __init__(self,row,x):
        """
        初始化节点
        每个节点一共7个属性
        :param row:节点的行号
        :param x: 用于标识列
        """
        self.val = x
        self.col = self
        self.row = row
        self.right = self
        self.left = self
        self.up = self
        self.down = self
    def remove_col(self):
        """
        删除一列，与head断开
        :return:
        """
        node = self
        while True:
            node.right.left = node.left
            node.left.right = node.right
            node = node.down
            if node == self:
                break
    def restore_col(self):
        """
        恢复
        :return:
        """
        node = self
        while True:
            node.right.left = node
            node.left.right = node
            node = node.down
            if node == self:
                break
    def remove_row(self):
        node = self
        while True:
            node.down.up = node.up
            node.up.down = node.down
            node = node.right
            if node == self:
                break
    def restore_row(self):
        node = self
        while True:
            node.up.down = node
            node.down.up = node
            node = node.right
            if node == self:
                break
def init_col(col_count:int):
    """
    初始化列
    :paramcol_count:总列数
    :return: head头
    """
    head = CCL_node(0,"head")
    for i in range(col_count):
        node = CCL_node(0,i)
        node.right = head
        node.left = head.left
        node.right.left = node
        node.left.right = node
    return head
def appene_row(index_list:list,head:CCL_node,row):
    """
    添加一行
    :param index_list:对应列号的列表
    :param head: 头
    :param row: 该行号 用于最终输出
    :return:
    """
    col = head.right
    last = None
    for i in index_list:
        while col != head:
            if col.val == i-1 :
                node = CCL_node(row,1)
                node.col = col
                node.down = col
                node.up = col.up
                node.down.up = node
                node.up.down = node
                if last is not None:
                    node.left = last
                    node.right = last.right
                    node.left.right = node
                    node.right.left = node
                last = node
            col = col.right
        col = head.right
def DLX_main(head:CCL_node,ans:list):
    """
    主算法
    :param head:头
    :param ans: 结果列表
    :return:
    """
    if head.right == head:
        return True                     #最终成功条件判断
    node = head.right
    while True:
        if node.down == node:           #保证每一列下面都有元素
            return False
        node = node.right
        if node == head:
            break
    restores = []
    first_col = head.right
    first_col.remove_col()
    restores.append(first_col.restore_col)
    node = first_col.down
    while True:
        if node.right != node:
            node.right.remove_row()
            restores.append(node.right.restore_row)
        node = node.down
        if node == first_col:
            break                      #删除第一列 及其对应的行
    st_restores = len(restores)        #记录恢复点 用于之后试错后的恢复
    try_row = first_col.down
    while try_row != first_col:         #主循环当第一列所有行都循环完之后结束
        ans.append(try_row.row)
        if try_row.right != try_row:
            node = try_row.right
            while True:
                del_col = node.col
                del_col.remove_col()
                restores.append(del_col.restore_col)
                del_col = del_col.down
                while del_col != node.col:
                    if del_col.right != del_col:
                        del_row = del_col.right
                        del_row.remove_row()
                        restores.append(del_row.restore_row)
                    del_col = del_col.down
                node = node.right
                if node == try_row.right:
                    break               #简化head 删除所有涉及到的行列
        if DLX_main(head,ans):          #递归：对简化之后的head 再次进行以上算法
            return True
        while st_restores < len(restores):  #恢复：恢复到 该层 的记录点
            restores.pop()()
        try_row = try_row.down
        ans.pop()
    while len(restores) > 0:
        restores.pop()()            #恢复本层函数所有操作
    return False                            #若主循环结束之后 没有弹出 返回False 用于判断 或最终输出
def api_result(index_list:list):
    """
    舞蹈链算法封装
    :param index_list:索引列表
    :return: 只返回有解时的答案
    """
    temp = []
    for i in index_list:
        temp.extend(i)
    temp = max(temp)
    head = init_col(temp)
    for i in range(len(index_list)):
        appene_row(index_list[i],head,i+1)
    temp = []
    result = DLX_main(head,temp)
    if result:
        return temp
    return None
def api_flag(index_list:list):
    """
    返回是否有解
    :param index_list:索引列表
    :return: bool
    """
    if api_result(index_list) is not None:
        return True
    return False
```

### 摒除法

```python
def deal_hidden(grid:Sodoku,mode):
    """
    摒除法
    :param grid:
    :param mode:
    :return:
    """
    if mode == "c":         #根据模式确定处理对象
        deal_set = np.array(grid.show_column())
    elif mode == "b":
        deal_set = np.array(grid.show_block())
    elif mode == "r":
        deal_set = np.array(grid.show_row())
    else:
        return
    result = []
    for i in range(1,10):
        flag_list = deal_set == i   #取出这个单元所有元素的索引
        temp = np.array(grid.show_candidate(),dtype=object)[flag_list]
        temp0 = []
        for k in temp:
            temp0.extend(k)
        temp0,flag = np.unique(temp0,return_counts=True)	#统计
        flag = flag == 1									#找出次数为1
        if True in flag:
            temp0 = temp0[flag]
            for j in temp0:
                flag_list0 = flag_list.copy()
                temp1 = []
                for k in temp:
                    if j in k :
                        temp1.append(True)
                    else:
                        temp1.append(False)
                flag_list0[flag_list0==True] = temp1		#大索引的一部分替换小索引
                temp1 = np.array(grid.show_numn(),dtype=object)[flag_list0]
                for s,t in temp1:							#这里的索引都是show_num的
                    result.append((s,t,j,i))
    return result
```

### 区块

```python
def block_lock(grid:Sodoku):
    """
    对宫区块进行输出
    :param grid:Sodoku
    :return:
    """
    result = []
    for i in range(1,10):
        flag_list = np.array(grid.show_block()) == i
        temp = np.array(grid.show_candidate(),dtype=object)[flag_list]
        temp0 = []
        for k in temp:
            temp0.extend(k)
        temp0,flag = np.unique(temp0,return_counts=True)
        flag = flag == 2
        if True in flag:
            temp0 = temp0[flag]
            for j in temp0:
                flag_list0 = flag_list.copy()
                temp1 = []
                for k in temp:
                    if j in k :
                        temp1.append(True)
                    else:
                        temp1.append(False)
                flag_list0[flag_list0==True] = temp1
                temp1 = np.array(grid.show_numn(),dtype=object)[flag_list0]
                if temp1[0,0] == temp1[1,0]:        #判断类型
                    flag_list0 = np.array(grid.show_row()) == temp1[0,0] + 1
                    temp2 = np.array(grid.show_candidate(),dtype=object)[flag_list0]
                    temp3 =[]
                    for k in temp2:
                        temp3.extend(k)
                    temp3,flag = np.unique(temp3,return_counts=True)
                    flag = flag ==2
                    if j not in temp3[flag]:    #判断是否有效
                        result.append(("r",temp1[0,0] + 1,j,i,tuple(temp1[0]),tuple(temp1[1])))
                if temp1[0,1] == temp1[1,1]:
                    flag_list0 = np.array(grid.show_column()) == temp1[0,1] + 1
                    temp2 = np.array(grid.show_candidate(),dtype=object)[flag_list0]
                    temp3 = []
                    for k in temp2:
                        temp3.extend(k)
                    temp3,flag = np.unique(temp3,return_counts=True)
                    flag = flag == 2
                    if j not in temp3[flag]:
                        result.append(("c",temp1[0,1] + 1,j,i,tuple(temp1[0]),tuple(temp1[1])))
    return result
```

### 直观技巧主算法

```python
def solve_sodoku(grid:sodoku_tools.Sodoku):
    """
    直观算法解题主流程
    :param grid:
    :return:
    """
    process = sodoku_show.DealPrint("hello")
    new_num = []
    old_num = grid.show_grid()
    sodoku_show.grid_write(new_num,old_num,[],[],process,"Qusetion","Initial Grid")
    grid0 = sodoku_tools.Sodoku(grid.inition_grid.copy())
    count = 0
    error = False
    while not grid0.finish():
        record1 = grid0.inition_grid.copy()             #设置记录点
        block_hidden(grid0, process, new_num, old_num)  #宫排除
        naked_single(grid0,process,new_num,old_num)     #唯余
        temp = sodoku_tools.deal_hidden(grid0,"r")      #行排除一次
        if len(temp) != 0:
            tip_list = []
            temp0 = []
            gr_list = []
            for i,j,k,t in temp:
                temp0.append((i,j,k))
                tip_list.append(t)
                gr_list.append((i,j))
            for new,gr,tip in zip(temp0,gr_list,tip_list):
                new_num.append(new)
                tip = "In r%d" % tip
                sodoku_show.grid_write(new_num,old_num,[gr],[],process,"行排除",tip)
                grid0.change_grid(new)
        block_hidden(grid0, process, new_num, old_num)  #宫排除到底
        naked_single(grid0,process,new_num,old_num)     #唯余
        temp = sodoku_tools.deal_hidden(grid0,"c")      #列排除一次
        if len(temp) != 0:
            tip_list = []
            temp0 = []
            gr_list = []
            for i,j,k,t in temp:
                temp0.append((i,j,k))
                tip_list.append(t)
                gr_list.append((i,j))
            for new,gr,tip in zip(temp0,gr_list,tip_list):
                new_num.append(new)
                tip = "In c%d" % tip
                sodoku_show.grid_write(new_num,old_num,[gr],[],process,"列排除",tip) #写入pdf
                grid0.change_grid(new)
        count += 1
        if len(grid0.show_block()) <= 20 :
            naked_single(grid0,process,new_num,old_num)
        if count >= 150:
            break
        if not grid0.check():
            print(grid0.lock_num)
            error = True
            break
        if np.all(record1 == grid0.inition_grid):   #如果没进展执行区块
            deal_lock(grid0,process,new_num,old_num)
        if np.all(record1 == grid0.inition_grid):   #还没进展执行多区块
            deal_lock_pro(grid0,process,new_num,old_num)
        if np.all(record1 == grid0.inition_grid):   #还没进展结束
            break
    if grid0.finish():
        sodoku_show.grid_write(new_num,old_num,[],[],process,"Solution:","")
    elif error:
        sodoku_show.grid_write(new_num,old_num,[],[],process,"这是个错题","尽量避免做错题")
    else:
        sodoku_show.grid_write(new_num,old_num,[],[],process,"我尽力了","It too hard")
    sodoku_show.grid_print(process)
```

### 写入excel

```python
def grid_write(new_list:list,old_list:list,green_reg:list,yellow_reg:list,sp:DealPrint,skil_name:str,tips:str):
    """
    按照标进行写入
    :param new_list:新数
    :param old_list: 提示数
    :param green_reg: 加绿区，本次出数
    :param yellow_reg: 加黄区，区块
    :param sp: 标
    :param skil_name:技巧名称
    :param tips: 技巧详细
    :return: None
    """
    if sp.n % 2 != 0:       #加入空行
        sp.n += 1
        sht1.row(sp.delt).height_mismatch = True
        sht1.row(sp.delt).height = 20 * 29
        sp.delt += 1
    else:
        sp.n += 1
        for i in range(2):
            sht1.row(i+sp.delt).height_mismatch = True
            sht1.row(i+sp.delt).height = 20 * 29
        sp.delt += 2
    for i in range(12):     #调整本次所有涉及的高度
        sht1.row(i+sp.delt).height_mismatch = True
        sht1.row(i+sp.delt).height = 20 * 29
    sht1.write_merge(sp.delt,sp.delt+1,0,8,skil_name,ski_font_sty)  #合并单元格并且写入技巧名称
    sht1.write_merge(sp.delt+2,sp.delt+2,0,8,tips,tip_font_sty)     #合并之后写人技巧详细
    temp = []
    for i,j,k in new_list:      #写新数
        temp.append((i,j))
    for i,j,k in old_list:      #写老数
        temp.append((i,j))
    y = np.ones((9,9))
    y = np.nonzero(y)
    y = list(zip(y[0],y[1]))
    for i,j in y:
        if (i,j) not in temp:   #空位加边框
            sht1.write(i+sp.delt+3,j,"",new_num_style)
    for i,j,k in new_list:      #写
        i = int(i)
        j = int(j)
        k = int(k)
        sht1.write(i+sp.delt+3,j,k,new_num_style)
    for i,j,k in old_list:
        i = int(i)
        j = int(j)
        k = int(k)
        sht1.write(i+sp.delt+3,j,k,old_num_style)
    filename = "%s.xls" % sp.name
    result.save(filename)       #保存
    sp.br.append(sp.delt + 4)
    for i in green_reg:         #记录绿区
        new = (i[0]+sp.delt+4,i[1]+1)
        sp.gr.append(new)       #记录黄区
    for i in yellow_reg:
        new = (i[0]+sp.delt+4,i[1]+1)
        sp.yl.append(new)
    sp.delt += 12              #更改标位置
```

### 打印

```python
def grid_print(sp:DealPrint):
    """
    打印
    :param sp:标
    :return:
    """
    filename = "%s.xls" % sp.name
    app = xw.App(visible=False, add_book=True)
    app.display_alerts = False
    app.screen_updating = False
    wb = app.books.open(filename)
    sht = wb.sheets[0]
    for i in sp.br:         #加框
        n = i
        for k in [n, n + 3, n + 6]:
            s = 'A%d:C%d' % (k, k + 2)
            cell = sht.range(xw.Range(s))
            cell.api.Borders(9).LineStyle = 1
            cell.api.Borders(9).Weight = 2
            cell.api.Borders(9).Color = 1
            cell.api.Borders(7).LineStyle = 1
            cell.api.Borders(7).Weight = 2
            cell.api.Borders(7).Color = 1
            cell.api.Borders(8).LineStyle = 1
            cell.api.Borders(8).Weight = 2
            cell.api.Borders(8).Color = 1
            cell.api.Borders(10).LineStyle = 1
            cell.api.Borders(10).Weight = 2
            cell.api.Borders(10).Color = 1
        for k in [n, n + 3, n + 6]:
            s = 'D%d:F%d' % (k, k + 2)
            cell = sht.range(xw.Range(s))
            cell.api.Borders(9).LineStyle = 1
            cell.api.Borders(9).Weight = 2
            cell.api.Borders(9).Color = 1
            cell.api.Borders(7).LineStyle = 1
            cell.api.Borders(7).Weight = 2
            cell.api.Borders(7).Color = 1
            cell.api.Borders(8).LineStyle = 1
            cell.api.Borders(8).Weight = 2
            cell.api.Borders(8).Color = 1
            cell.api.Borders(10).LineStyle = 1
            cell.api.Borders(10).Weight = 2
            cell.api.Borders(10).Color = 1
        for k in [n, n + 3, n + 6]:
            s = 'G%d:I%d' % (k, k + 2)
            cell = sht.range(xw.Range(s))
            cell.api.Borders(9).LineStyle = 1
            cell.api.Borders(9).Weight = 2
            cell.api.Borders(9).Color = 1
            cell.api.Borders(7).LineStyle = 1
            cell.api.Borders(7).Weight = 2
            cell.api.Borders(7).Color = 1
            cell.api.Borders(8).LineStyle = 1
            cell.api.Borders(8).Weight = 2
            cell.api.Borders(8).Color = 1
            cell.api.Borders(10).LineStyle = 1
            cell.api.Borders(10).Weight = 2
            cell.api.Borders(10).Color = 1
    for i in sp.gr:     #加绿色
        cell = sht.range(xw.Range(i))
        cell.color = (144,238,144)
    for i in sp.yl:     #加黄色
        cell = sht.range(xw.Range(i))
        cell.color = (255,228,181)
    sht.api.PrintOut()  #打印
    wb.save()
    wb.close()
	app.kill()
```

## 尾巴

结果不断的试验，我发现

1. 提示数和难度没很大关系

   通过打印解题过程有些很难的是一开始都填不了

   有些是做到一大半时卡住了（这时提示数大约50个）

   > 难度更多和结构有关

2. 依照[shudu.one](shudu.one)网站，如果你只按照简单的直观技巧，你只能完成初级和入门的题目。

3. 我所设计的出题算法大部分题是中级以上的难度[^2]

[^2]: 依照[shudu.one](shudu.one)网站的难度[^1]
[^1]:参照《标准数独技巧——从入门到精通》 张齐天
[^3]: 参考[算法实践——舞蹈链（Dancing Links）算法求解数独](https://www.cnblogs.com/grenet/p/3163550.html)

