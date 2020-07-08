# 算法入门

## 算法入门系列课程1 - 周而复始

### 算法概述

1. 什么是算法？

   解决问题的正确方法和具体的实施步骤。

   例子1：如何在两栋相距50m的大楼的两个房间牵一条线（两个房间都有窗）？

   - 养一只鸟（如鸽子），将线送过去
   - 用很长的杆子将线递过去
   - 用无人机（遥控飞行器）将线送过去

   如何评价这些方法的好坏？**少花钱，不费事**！

   例子2：大教室里坐了几百名学生一起听课，如何快速的统计学生人数？

   例子3：向列表容器中**逆向**插入100000个元素。

   - 方法1：

     ```Python
     nums = []
     for i in range(100000):
         nums.append(i)
     nums.reverse()
     ```

   - 方法2：

     ```Python
     nums = []
     for i in range(100000):
         nums.insert(0, i)
     ```

   例子3：生成Fibonacci数列（前100个Fibonacci数）。

   - 方法1 - 递推：

     ```Python
     a, b = 0, 1
     for num in range(1, 101):
         a, b = b, a + b
         print(f'{num}: {a}')
     ```

   - 方法2 - 递归：

     ```Python
     def fib(num):
         if num in (1, 2):
             return 1
         return fib(num - 1) + fib(num - 2)
     
     
     for num in range(1, 101):
         print(f'{num}: {fib(num)}')
     ```

   - 方法3 - 改进的递归：

     ```Python
     def fib(num, temp={}):
         if num in (1, 2):
             return 1
         elif num not in temp:
             temp[num] = fib(num - 1) + fib(num - 2)
         return temp[num]
     ```

   - 方法4  - 改进的递归：

     ```Python
     from functools import lru_cache
     
     
     @lru_cache()
     def fib(num):
         if num in (1, 2):
             return 1
         return fib(num - 1) + fib(num - 2)
     ```

2. 如何评价算法的好坏？

   [渐近时间复杂度](<https://zh.wikipedia.org/wiki/%E6%97%B6%E9%97%B4%E5%A4%8D%E6%9D%82%E5%BA%A6>)和渐近空间复杂度。

3. 大***O***符号的意义？

   表示一个函数相对于输入规模的增长速度，也可以称之为函数的数量级。

   | 大*O*符号       | 说明               | 例子                                         |
   | --------------- | ------------------ | -------------------------------------------- |
   | $$O(c)$$        | 常量时间复杂度     | 布隆过滤器 / 哈希存储                        |
   | $$O(log_2n)$$   | 对数时间复杂度     | 二分查找（折半查找）                         |
   | $$O(n)$$        | 线性时间复杂度     | 顺序查找 / 桶排序                            |
   | $$O(n*log_2n)$$ | 对数线性时间复杂度 | 高级排序算法（归并排序、快速排序）           |
   | $$O(n^2)$$      | 平方时间复杂度     | 简单排序算法（选择排序、插入排序、冒泡排序） |
   | $$O(n^3)$$      | 立方时间复杂度     | Floyd算法 / 矩阵乘法运算                     |
   | $$O(2^n)$$      | 几何级数时间复杂度 | 汉诺塔                                       |
   | $$O(n!)$$       | 阶乘时间复杂度     | 旅行经销商问题                               |

### 穷举法

在计算机科学中，**穷举法**或者**暴力搜索法**是一个非常非常直观的解决问题的方法，这种方法通过一项一项的列举解决方案所有可能的候选项以及检查每个候选项是否符合问题的描述，最终得到问题的解。

虽然暴力搜索很容易实现，并且如果解决方案存在它就一定能够找到，但是它的代价是和候选方案的数量成比例的，由于这一点，在很多实际问题中，消耗的代价会随着问题规模的增加而快速地增长。因此，当问题规模有限或当存在可用于将候选解决方案的集合减少到可管理大小时，就可以使用暴力搜索。另外，当实现方法的简单度比速度更重要的时候，也可以考虑使用这种方法。

### 经典例子

1. **百钱百鸡**问题：公鸡5元一只，母鸡3元一只，小鸡1元三只，用100元买一百只鸡，问公鸡、母鸡、小鸡各有多少只？

   ```Python
   for x in range(21):
       for y in range(34):
           z = 100 - x - y
           if z % 3 == 0 and 5 * x + 3 * y + z // 3 == 100:
               print(x, y, z)
   ```

2. **五人分鱼**问题：ABCDE五人在某天夜里合伙捕鱼，最后疲惫不堪各自睡觉。第二天A第一个醒来，他将鱼分为5份，扔掉多余的1条，拿走了属于自己的一份；B第二个醒来，也将鱼分为5份，扔掉多余的1条，拿走属于自己的一份；然后C、D、E依次醒来，也按同样的方式分鱼，问他们至少捕了多少条鱼？

   ```Python
   fish = 6
   while True:
       total = fish
       enough = True
       for _ in range(5):
           if (total - 1) % 5 == 0:
               total = (total - 1) // 5 * 4
           else:
               enough = False
               break
       if enough:
           print(fish)
           break
       fish += 5
   ```

3. **暴力破解口令**：

   ```Python
   import re
   
   import PyPDF2
   
   with open('Python_Tricks_encrypted.pdf', 'rb') as pdf_file_stream:
       reader = PyPDF2.PdfFileReader(pdf_file_stream)
       with open('dictionary.txt', 'r') as txt_file_stream:
           file_iter = iter(lambda: txt_file_stream.readline(), '')
           for word in file_iter:
               word = re.sub(r'\s', '', word)
               if reader.decrypt(word):
                   print(word)
                   break
   ```

   
### 现实中的递归

从前有座山，山里有座庙，庙里有个老和尚，正在给小和尚讲故事呢！故事是什么呢？从前有座山，山里有座庙，庙里有个老和尚，正在给小和尚讲故事呢！故事是什么呢？从前有座山，山里有座庙，庙里有个老和尚，正在给小和尚讲故事呢！故事是什么呢？……

野比大雄在房间里，用时光电视看着未来的情况。电视画面中，野比大雄在房间里，用时光电视看着未来的情况。电视画面中，野比大雄在房间里，用时光电视看着未来的情况……

阶乘的递归定义：$$0! = 1$$，$$n!=n*(n-1)!$$ ，使用被定义对象的自身来为其下定义称为递归定义。

[德罗斯特效应](https://zh.wikipedia.org/wiki/%E5%BE%B7%E7%BD%97%E6%96%AF%E7%89%B9%E6%95%88%E5%BA%94)是递归的一种视觉形式。图中女性手持的物体中有一幅她本人手持同一物体的小图片，进而小图片中还有更小的一幅她手持同一物体的图片……

![](./res/droste.png)

### 递归的应用

在程序中，一个函数如果直接或者间接的调用了自身，我们就称之为递归函数。

写递归函数有两个要点：

1. 收敛条件 - 什么时候结束递归。
2. 递归公式 - 每一项与前一项（前*N*项）的关系。

例子1：求阶乘。

```Python
def fac(num):
    if num == 0:
        return 1
    return num * fac(num - 1)
```

Python对递归的深度加以了限制（默认1000层函数调用），如果想突破这个限制，可以使用下面的方法。

```Python
import sys

sys.setrecursionlimit(10000)
```

例子2：爬楼梯 - 楼梯有*n*个台阶，一步可以走1阶、2阶或3阶，走完*n*个台阶共有多少种不同的走法。

```Python
def climb(num):
    if num == 0:
        return 1
    elif num < 0:
        return 0
    return climb(num - 1) + climb(num - 2) + climb(num - 3)
```

**注意**：上面的递归函数性能会非常的差，因为时间复杂度是几何级数级的。

优化后的代码。

```Python
from functools import lru_cache


@lru_cache()
def climb(num):
    if num == 0:
        return 1
    elif num < 0:
        return 0
    return climb(num - 1) + climb(num - 2) + climb(num - 3)
```

不使用的递归的代码。

```Python
def climb(num):
    a, b, c = 1, 2, 4
    for _ in range(num - 1):
        a, b, c = b, c, a + b + c
    return a
```

**重点**：有更好的办法的时候，请不要考虑递归。

### 回溯法

**回溯法**是[暴力搜索法](https://zh.wikipedia.org/wiki/%E6%9A%B4%E5%8A%9B%E6%90%9C%E5%B0%8B%E6%B3%95)中的一种。对于某些计算问题而言，回溯法是一种可以找出所有（或一部分）解的一般性算法，尤其适用于约束满足问题（在解决约束满足问题时，我们逐步构造更多的候选解，并且在确定某一部分候选解不可能补全成正确解之后放弃继续搜索这个部分候选解本身及其可以拓展出的子候选解，转而测试其他的部分候选解）。

### 经典案例

例子1：**迷宫寻路**。

![](./res/maze.png)

```Python
"""
迷宫寻路
"""
import random
import sys

WALL = -1
ROAD = 0

ROWS = 10
COLS = 10


def find_way(maze, i=0, j=0, step=1):
    """走迷宫"""
    if 0 <= i < ROWS and 0 <= j < COLS and maze[i][j] == 0:
        maze[i][j] = step
        if i == ROWS - 1 and j == COLS - 1:
            print('=' * 20)
            display(maze)
            sys.exit(0)
        find_way(maze, i + 1, j, step + 1)
        find_way(maze, i, j + 1, step + 1)
        find_way(maze, i - 1, j, step + 1)
        find_way(maze, i, j - 1, step + 1)
        maze[i][j] = ROAD


def reset(maze):
    """重置迷宫"""
    for i in range(ROWS):
        for j in range(COLS):
            num = random.randint(1, 10)
            maze[i][j] = WALL if num > 7 else ROAD
    maze[0][0] = maze[ROWS - 1][COLS - 1] = ROAD


def display(maze):
    """显示迷宫"""
    for row in maze:
        for col in row:
            if col == -1:
                print('■', end=' ')
            elif col == 0:
                print('□', end=' ')
            else:
                print(f'{col}'.ljust(2), end='')
        print()


def main():
    """主函数"""
    maze = [[0] * COLS for _ in range(ROWS)]
    reset(maze)
    display(maze)
    find_way(maze)
    print('没有出路!!!')


if __name__ == '__main__':
    main()
```

**说明：**上面的代码用随机放置围墙的方式来生成迷宫，更好的生成迷宫的方式请参考[《简单的使用回溯法生成 Tile Based 迷宫》](<https://indienova.com/indie-game-development/generate-tile-based-maze-with-backtracking/>)一文。

例子2：**骑士巡逻** - 国际象棋中的骑士（马），按照骑士的移动规则走遍整个棋盘的每一个方格，而且每个方格只能够经过一次。

![](./res/knight_tour.gif)

```Python
"""
骑士巡逻
"""
import sys

SIZE = 8


def display(board):
    """显示棋盘"""
    for row in board:
        for col in row:
            print(f'{col}'.rjust(2, '0'), end=' ')
        print()


def patrol(board, i=0, j=0, step=1):
    """巡逻"""
    if 0 <= i < SIZE and 0 <= j < SIZE and board[i][j] == 0:
        board[i][j] = step
        if step == SIZE * SIZE:
            display(board)
            sys.exit(0)
        patrol(board, i + 1, j + 2, step + 1)
        patrol(board, i + 2, j + 1, step + 1)
        patrol(board, i + 2, j - 1, step + 1)
        patrol(board, i + 1, j - 2, step + 1)
        patrol(board, i - 1, j - 2, step + 1)
        patrol(board, i - 2, j - 1, step + 1)
        patrol(board, i - 2, j + 1, step + 1)
        patrol(board, i - 1, j + 2, step + 1)
        board[i][j] = 0


def main():
    """主函数"""
    board = [[0] * SIZE for _ in range(SIZE)]
    patrol(board)


if __name__ == '__main__':
    main()
```


