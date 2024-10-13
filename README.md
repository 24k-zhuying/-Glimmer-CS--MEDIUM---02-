# CS-MEDIUM-02 定点数精确运算

## 课前准备--文件操作
问题：
在本次任务中你将实现用定点数实现精确的小数运算。但单独实现精确运算算法时仍然需要手动输入很长的定点数，难免会有不必要的麻烦。但当我们学会文件操作后，直接读取文件中的信息会更加快捷正确。

ps：所有运行截图都放在了一个png文件中

[代码展示](课前准备--文件操作.md)
[结果展示](photo.png)

## 任务1

1. 为什么会出现这样的现象？请阅读相关资料后提交与于arkdown文档。
  答：在计算机中,浮点数是用二进制表示的。0.2的二进制表示是一个无限循环小数0.001100110011···,同样，0.1 的二进制表示也是一个无限循环小数0.0001100110011···。当进行0.1+0.2的计算时，实际上是对这两个不精确的二进制表示进行加法运算，然后再将结果转换为十进制输出。由于二进制表示的不精确性，最终得到的结果是近似值，就出现了0.1+0.2>0.3的现象。
  2. 该如何获得精确值呢？请阅读相关资料后提交于markdown文档。（可以了解java中的BigInteger和BigDecimal类，python中的Decimal块和C语言gmp.h头文件；了解二进制中的移位运算）
  答：
  * 由java中的BigDecimal类与python中的Decimal块运算原理总结得：这两类都是先将小数的小数点去掉转化为整数并用一个变量记录，然后再用另一个变量记录小数点的位置，运算时用整数代替小数运算可以避免精度问题，运算完后再将结果转化为对应小数。
  * 由C语言gmp.h头文件中对于浮点数精度运算的原理总结得：该原理是将浮点数转化为分数，将浮点数运算变为分数的运算从而获得精确值。

  3. 课前热身：请提交代码实现二进制与十进制的相互转化。
   [代码展示](任务一.md)

   ## 任务2
   ### step1 定点数的表示与存储
  问题：正如之前了解过的数据结构，我们知道原生的几种类型由于存储方式的限定导致不能实现更多操作，所以在这里要学会使用PointFixedNum类来进行操作，注意符号位、整数部分、小数部分。、
  [代码展示](任务二step1.md)
  [运行结果](photo.png)

   ### step2 实现精确计算
   问题：完成两个定点数之间不丢失精度的加法、减法、乘法
   [代码展示](任务二step2.md)
   [运行结果](photo.png)
  
   ## 任务3
   问题：运用前文所学，完成CS_M_02中的连续计算，最后结果以十进制表示。
   [代码展示](任务三.md)
   [运行结果](photo.png)

   ## 总结感悟
   这道题花了有十几天（中间是国庆写的少），过程艰难且漫长，但却让我学会了很多新东西。在这过程中最难受的无非就是任务二的题目理解不是很恰当，定点数存储得与出题学长有出入，导致后续的难度直线上升，也出现了那个最让我难受的前置0的问题，但在反复的打磨中还是逐渐克服了，希望后续的做题过程能顺利。ps：做题过程写在了代码中。。
