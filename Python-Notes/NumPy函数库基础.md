# NumPy函数库基本操作

```python
from numpy import *

# mat() 函数将数组转化为矩阵
randMat = mat(random.rand(4, 4))  # 构造一个4*4随机数组
invRandMat = randMat.I
print(randMat * invRandMat)

# 求出计算机计算误差 eye()创建单位矩阵
error = randMat * invRandMat - eye(4 * 4)


# 数组操作
mm = array((1, 1, 1))
pp = array((1, 2, 3))
# print(pp + mm)
# print(pp ** 2)
# print(pp * 2)

# 矩阵操作
ss = mat([2,1,3])  # 把列表转化成矩阵
mm = mat(mm)
print(mm * ss.T)

# 原地排序
ss.sort()
# 拷贝排序
a = ss.argsort()

ss.mean()
shape(a)

```

#### tile()函数
* 对一个元素进行重复
* 在执行时比较数组维度d和元组维度reps,如果`d < reps`,则需要对数组补中括号[]来增加维度
* 元组数字从右到左,数组维度从最深(最深的树)到最低
    * 假设元组为`(a,b,c,d,e,f)`,则数组最深维度重复f次,然后次深维度重复e次...直到最低维度重复a次
* http://www.jianshu.com/p/4b74a367833c

```python
a = array([0, 1, 2])
b = tile(a, (2, 1))
print(b)

//结果为
[[0, 1, 2],  
 [0, 1, 2]]

 数组只有一个维度,d<reps,所以补充一个中括号: [[0,1,2]]
 元组从右到左,数组维度从最深的开始,对a最深维度重复1次,得到[[0,1,2]].然后对此深维度重复2次,得到[[0,1,2][0,1,2]]
```
