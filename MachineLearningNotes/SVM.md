# 支持向量机
## 1.间隔与支持向量
* 样本空间中任意点x到超平面(w,b)的**距离**:  
$r = \frac{w^Tx + b}{||w||}$  
    * 特别的,对于超平面,表达式中分子为1,支持向量到超平面的距离是$\frac{1}{||w||}$,间隔是$M = \frac{2}{||w||}$
* 假设超平面(w,b)能将训练样本正确分类:$f(x_i):$  
    $w^Tx_i + b \geq +1 , y_i = +1$  
    $w^Tx_i + b \leq -1 , y_i = -1$

* 最大化间隔M = $max \frac{2}{||w||}$,即:  
    $min \frac{1}{2}||w^2||$  
    ` s.t.`  $y_i(w^Tx_i + b) \geq 1$,  `i = 1,2,..m`
    * 上式表述**在超平面能正确分类样本的条件下使得间隔最大化**
    * 这里的s.t.限制条件即$f(x_i)$的另一种表达形式
    * 用拉格朗日乘数法求使得上式最小化的最优参数w和b

## 2.拉格朗日对偶
* **目标函数:**  
$min \frac{1}{2}||w^2||$  
` s.t.`  $y_i(w^Tx_i + b) \geq 1$,  `i = 1,2,..m`

#### 拉格朗日函数

* 处理**具有约束条件的函数**问题时,可以把目标函数和约束条件融入一个拉格朗日函数,再通过这个函数来寻找最优点.
    * 步骤是给每个约束条件乘拉格朗日算子然后和目标函数相加
    * 不等式约束条件变形为小于0的函数,算子大于0

* 等式约束条件例:  
目标函数:  
![](http://images.cnblogs.com/cnblogs_com/jerrylead/201103/201103131234477453.png)  
构造拉格朗日函数,$\beta$是拉格朗日算子:  
![](http://images.cnblogs.com/cnblogs_com/jerrylead/201103/201103131234483700.png)  
然后分别对参数求偏导  
![](http://img.blog.csdn.net/20140507153113328?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTA2NzM2MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

* 不等式约束条件例: 
目标函数:   
![](http://images.cnblogs.com/cnblogs_com/jerrylead/201103/201103131234511451.png)  
构造拉格朗日函数  
![](http://images.cnblogs.com/cnblogs_com/jerrylead/201103/201103131234524417.png)  
设如下等式$\theta(w)$*拉格朗日函数的最大值*和约束条件:  
![](http://images.cnblogs.com/cnblogs_com/jerrylead/201103/201103131234552417.png)  
![](http://img.blog.csdn.net/20140507154831031?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMTA2NzM2MA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)   
**如果条件不全部满足的话**,总有值$\alpha_i,\beta_i$使得拉格朗日函数$\theta(w)$的**最大值出现正无穷**:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/20140507154937109.jpg)  
于是拉格朗日函数的最大值函数$\theta(w)$可转换为:   
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/201103131235002969.png)  
    * f(w)的由来:此时w满足约束条件,$g(w_i) <= 0$,$h(w_i) = 0$,所以此时拉格朗日函数的第二项是负数,第三项是0,$\theta(w)$是拉格朗日函数的**最小值**,所以是f(w)  

    这时$\theta(w)$的最小值就是f(w)的最小值,我们的目标函数$min_w f(w)$可以转化为求$\theta(w)$的最小值:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/201103131235029805.png)  
同时设拉格朗日函数的最大值函数:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/201103131235057523.png)  
**将问题转化为先求拉格朗日函数关于w的最小值(此时把$\alpha \beta$看成常量)之后再求最大值**,如下:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/20110313123508158.png)  
这个就是原问题的对偶问题,相对于原问题只是更换了min和max的顺序.**一般更换顺序的结果是$max min(x) \leq min max(x)$**.我们可设:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/201103131235108008.png)  
在一定条件下:$d^* = p^* $.下面求二者相等的条件(也就是**对偶问题相等的条件**).  
**假设f和g都是凸函数,h是仿射的**(*仿射:存在ai,bi,使得$h_i(w) = a_i^Tw + b_i$并且$h_i(w) = 0,i=1..m$*),那么一定会**存在** $w^*,\alpha^*,\beta^*$,使得
    * $w^*$ 是原问题的解,$\alpha^*,\beta^*$是对偶问题的解
    * $d^* = p^* = L(w^*,\alpha^*,\beta^*)$
    * $w^*,\alpha^*,\beta^*$满足KKT条件:
        * ![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/20140507161926921.jpg)  

    **如果$w^*,\alpha^*,\beta^*$满足KKT条件,那么他们就是原问题和对偶问题的解**.从KKT条件:$\alpha_i^* g_i(w^*) = 0,i = 1 .. k$和$g_i(w^*) \leq 0$得出:如果$\alpha_i^* > 0$,那么$g_i(w^*) = 0$.
    * 也就是说明$g_i(w^*) = 0$时,w处于可行域的边界上,这时才是起作用的约束.而其他位于可行域内部的点($g_i(w^*) < 0$)都不是起作用的约束,也就是$\alpha^* = 0$的时候.


## 3.由拉格朗日对偶得出最优间隔分类器
在之前为了寻找最优间隔分类器,提出了如下优化问题:  
$min \frac{1}{2}||w^2||$  
` s.t.`  $y_i(w^Tx_i + b) \geq 1$,  `i = 1,2,..m`  
也就是:  
` s.t.`  $g_i(w) = 1 - y_i(w^Tx_i + b) \leq 0$,  `i = 1,2,..m`   
同时需要满足的KKT条件如下:   
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/20140507161926921.jpg)  
1. 构造目标问题的拉格朗日函数:  
$L(w,b,\alpha) = \frac{1}{2}||w^2|| + \sum_{i = 1}^m\alpha_i(1-y_i(w^Tx_i + b))$
2. L(w,b,a)对w和b求偏导等于零得:  
$w = \sum_{i = 1} ^ m\alpha_iy_ix_i$  
$0 = \sum_{i = 1}^m\alpha_iy_i$  
3. 将上式代入拉格朗日函数中得化简后的拉格朗日函数:  
$L(w,b,\alpha) = \sum_{i = 1} ^ m\alpha_i - \frac{1}{2}\sum_{i = 1}^m \sum_{j = 1} ^ m \alpha_i\alpha_j y_i y_j x_i^T x_j$
4. 现在我们得到了关于w和b的可以最小化的等式,一定会**存在**$w^*,\alpha^*,\beta^*$,使得$w^*$是原问题的解,$\alpha^*$是对偶问题的解.如果求出$\alpha^*$,就可以根据$w = \sum_{i = 1} ^ m\alpha_iy_ix_i$求出$w^*$.  
**对偶问题如下**:  
$max_\alpha : L(w,b,\alpha) = \sum_{i = 1} ^ m\alpha_i - \frac{1}{2}\sum_{i = 1}^m \sum_{j = 1} ^ m \alpha_i\alpha_j y_i y_j x_i^T x_j$  
    * s.t. : $\sum_{i = 1}^m \alpha_iy_i = 0$
    * $\alpha_i \geq0 , i = 1...m$
    * 需要优化来满足的KKT:
        *  $\alpha_i \geq0 , i = 1...m$
        *  $y_if(x_i) - 1 \geq 0$
        *  $\alpha_i(y_if(x_i) - 1) = 0$

5. 这时可以为每个数据点设置一个松弛因子$\xi_i\geq0$,使得  
$y_i(w^Tx_i+b)\geq1-\xi_i$,同时对每个松弛因子$\xi_i$支付一个代价C,由此原问题转化为:  
![](http://img.blog.csdn.net/20160407155623855)  
C称为惩罚参数(C>0),C越大对误分类的惩罚越大.

然后建立拉格朗日函数,求偏导回代化简后求出对偶函数,得出目标函数:  
![](http://img.blog.csdn.net/20160407181454394)  
ui也是拉格朗日乘子  
![](http://img.blog.csdn.net/20160407180704750)   
由KKT条件可得  
![](http://img.blog.csdn.net/20160407183425008)  
![](http://img.blog.csdn.net/20160407183433245)


## 4. 核函数
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/1364952814_3505.gif)  
* 当数据线性不可分时,可将样本从原始空间映射到一个更为高维的特征空间,使得样本在这个特征空间内线性可分  
* 为了避免数据映射到高维空间后带来的大量计算"维数灾难",可以使用核函数代替两个高维特征向量的点乘内积
* **核函数定义**:  
$\phi(x)$表示将x映射后的特征向量,则核函数  
$k(x_i,x_j) = <\phi(x_i),\phi(x_j)> = \phi(x_i)^T \phi(x_j)$

现在回到SVM的情形,假设原始的数据是非线性的,我们通过一个映射$\phi(.)$将其映射到一个高维的空间中,数据变的线性可分了.这时就可以用原来的推导进行计算,只是现在推导是在新的空间进行.  
上次得到的最终分类函数:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/1351142890_4908.jpg)  
在映射过后的空间:  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/1351142890_4908.jpg)  
其中的$\alpha$通过求解对偶问题得到  
![](https://wizardforcel.gitbooks.io/dm-algo-top10/content/img/1351142906_9411.jpg)

## 5. SMO优化算法
### 5.1 算法原理
原理是**把整个二次规划问题分解为很多易于处理的小问题**.SMO每次优化只处理**两个样本**的优化问题,从而避免了迭代算法中只处理一个变量 

对SVM来说,由于等式约束的存在($\sum_{i= 1}^m y_i\alpha_i = 0$,固定m-1个参数会导致剩的1个参数也被固定)使得我们不可能单独优化一个变量,因此每次至少要对两个样本进行优化(优化它们对应的拉格朗日乘子).

当然,这样的一次最小优化不可能保证其结果就是所优化的拉格朗日乘子的最终结果,但会使得目标函数向极小值迈进一步.然后再对其他样本的拉格朗日乘子做最小优化,**直到所有的乘子都符合KKT条件时,目标函数达到最小,算法结束**

### 5.2 求拉格朗日乘子的最优解
设两个正在优化的拉格朗日乘子分别为$\alpha_1,\alpha_2$,固定其他参数,于是可以简化目标函数为只关于$\alpha_1,\alpha_2$的二元函数,Constant表示常数项(不包含$\alpha_1,\alpha_2$的项)  ,K(1,2)表示核函数K(x1,x2)  
$v_i = \sum_{j = 3} ^ m a_j y_j K(x_i,x_j), i = 1,2$
$min\ Ψ(\alpha_1,\alpha_2)=\frac12K_{11}\alpha_1^{2}+\frac12K_{22}\alpha_2^2+y_1y_2K_{12}\alpha_1\alpha_2-(\alpha_1+\alpha_2)+y_1v_1\alpha_1+y_2v_2\alpha_2+Constant\qquad \color{Red}{(1)}$
* 观察到目标函数求的是max,而转换后求的是min,这只是因为转换后加了一个负号,max就变成min了,是相同的式子两种不同表述形式

由等式约束得,$\alpha_1y_1 + \alpha_2y_2 = -\sum_{i = 3}^m\alpha_iy_i = \zeta$,其中$\zeta$是使得 $\sum_{i = 1}^m \alpha_iy_i = 0$成立的常数  
由上式把$\alpha_1$表示为$\alpha_2$的形式,然后带入简化后的目标函数,由于常数项Constant不影响目标函数的解,可以省略掉常数项,得到只关于参数$\alpha_2$的一元函数:  
$min\ Ψ(\alpha_2)=\frac12K_{11}(\zeta-\alpha_2y_2)^2+\frac12K_{22}\alpha_2^2+y_2K_{12}(\zeta-\alpha_2y_2)\alpha_2-(\zeta-\alpha_2y_2)y_1-\alpha_2+v_1(\zeta-\alpha_2y_2)+y_2v_2\alpha_2 \qquad \color{Red}{(3)}$  
然后对$\alpha_2$求导等于0
* $\frac{\partial \Psi (\alpha_2)}{\partial \alpha_2}=(K_{11}+K_{22}-2K_{12})\alpha_2-K_{11}\zeta y_2+K_{12}\zeta y_2+y_1y_2-1-v_1y_2+v_2y_2=0$
* 由上式求得了$\alpha_2$的解,代回等式约束条件中可得$\alpha_1$的解,分别记为$\alpha_1^{new},\alpha_2^{new}$,优化前的解记为$\alpha_1^{old},\alpha_2^{old}$  


下面对$\alpha_2$求解并**化简**得出$\alpha_2$的表达式:  
* $\zeta=\alpha_1^{old}y_1+\alpha_2^{old}y_2 \qquad \color{Red}{(4)}$  
* 假设SVM超平面模型为$f(x) = w^Tx + b$,将对拉格朗日函数w的偏导为0得到的w带入得  
$f(x)=\sum_{i=1}^N\alpha_iy_iK(x_i,x)+b$  
$f(x_i)$表示第i个样本的与测试值,  $y_i$表示第i个样本的真实值,定义误差:  
$E_i=f(x_i)-y_i \qquad \color{Red}{(5)}$
* 由于$v_i = \sum_{j = 3}^ N a_j y_j K(x_i,x_j), i = 1,2$,由于形式和f(x)很相似,转化为f(x)形式为::  
$v_1=f(x_1)-\sum_{j=1}^2y_j\alpha_jK_{1j}-b\qquad \color{Red}{(6)}$  
$v_2=f(x_2)-\sum_{j=1}^2y_j\alpha_jK_{2j}-b\qquad \color{Red}{(7)}$

把(4)(6)(7)带入(3)式求得  
$(K_{11}+K_{22}-2K_{12})\alpha_2^{new,unclipped}=(K_{11}+K_{22}-2K_{12})\alpha_2^{old}+y_2\left[y_2-y_1+f(x_1)-f(x_2)\right]$  
代入(5)式,**得出未考虑约束问题的$\alpha_2$**:  
$\alpha_2^{new,unclipped}=\alpha_2^{old}+\frac{y_2(E_1-E_2)}{K_{11} + K_{12} - 2K_{12}} \qquad \color{Red}{(8)}$

### 5.3 对原始解修剪(添加约束条件)
上述求出的解未考虑约束条件:
* $0 \leq a_{i = 1,2} \leq C$
* $\alpha_1y_1+\alpha_2y_2=\zeta$ 

在二维平面上直观表述上述两个约束条件:
* 矩形代表$\alpha_1$和$\alpha_2$之间所有可能的线性关系集合,每条线段代表一个线性关系,左图是$\alpha_1$和$\alpha_2$异号,因为同增.右图同号.  
![](http://img.blog.csdn.net/20160427143321581)  

**最优解必须要在方框内且在直线上取得**,因此 $L \leq a_2^{new} \leq H$

由图得出$\alpha_2$的**上下限**(max,min函数用于表示图中红黑线两种情况的结合写法):  
* 如果$y_1 ,y_2$异号:
    * $L = max(0,\alpha_2 - \alpha_1)$,  
    * $H= min(C,C + \alpha_2 - \alpha_1)$
* 如果$y_1,y_2$同号:
    * $L = max(0,\alpha_2 + \alpha_1 - C)$
    * $H = min(C, \alpha_2 + \alpha_1)$

则最终经过修剪后$\alpha_2^{new}$的值为:  
![](https://ws1.sinaimg.cn/large/006QRhAtgy1focaj291tpj30jr05gdg8.jpg)


由于其他N-2个变量固定,因此$\alpha_1^{old}y_1+\alpha_2^{old}y_2=\alpha_1^{new}y_1+\alpha_2^{new}y_2$,可得:  
$\alpha_1^{new}=\alpha_1^{old}+y_1y_2(\alpha_2^{old}-\alpha_2^{new})\qquad \color{Red}{(9)}$

### 5.4 取临界情况
大部分情况下,有$\eta=K_{11}+K_{22}-2K_{12}>0$,但是如下情况时$\alpha_2^{new}$需要**取临界值L或H**
* $\eta < 0$,当核函数K不满足Mercer定理时,矩阵K非正定,这时目标函数为凸函数,没有极小值,极值在定义域边界处取得
* $\eta = 0$,样本X1与X2值相同,这时目标函数为单调函数,极值在定义域边界处取得

计算方法:  
当$\alpha_2^{new} = L$或$\alpha_2^{new} = H$时,需要求$\alpha_1^{new}$的定义域$L_1 = \alpha_1^{new}$或$H_1 = \alpha_1^{new}$.  
由 $\alpha_1^{new}=\alpha_1^{old}+y_1y_2(\alpha_2^{old}-\alpha_2^{new}  )$计算得出:  
![](http://img.blog.csdn.net/20160427165538639)  

带入固定其他算子后的目标函数(1)中,比较Ψ(α1=L1,α2=L)与Ψ(α1=H1,α2=H)的大小,α2取较小的函数值对应的边界点  
![](http://img.blog.csdn.net/20160427164937605)  
其中  
![](http://img.blog.csdn.net/20160427170049944)

### 5.5 启发式选择变量
#### 第一个变量的选择
第一个变量的选择是外循环过程.首先遍历整个样本集,选择违反KKT条件的$\alpha_i$作为第一个变量,接着采取选择第二个变量的方法选择第二个变量,对这两个变量优化.当遍历完整个样本集后，遍历非边界样本集(0<αi<C)中违反KKT的αi作为第一个变量，同样依据相关规则选择第二个变量，对此两个变量进行优化。当遍历完非边界样本集后，再次回到遍历整个样本集中寻找，即在整个样本集与非边界样本集上来回切换，寻找违反KKT条件的αi作为第一个变量.直到样本集中所有的变量都符合KKT条件  

此图由有松弛变量条件的KKT条件推导而出  
![](http://img.blog.csdn.net/20160407180704750)
![](http://img.blog.csdn.net/20160427212013553)  
KKT条件说明:在两条间隔线外面的点$\alpha_i$为0,间隔线中间的为C,间隔线上面的在0-C之间
#### 第二个变量的选择
选择第二个变量的过程是内循环.假设在外循环中找到第一个变量a1,第二个变量的选择希望能使$\alpha_2^{old}$能有较大的变化.由于α2是依赖于|E1−E2|计算的,因此选择最大的|E1−E2|来近似最大化步长,步骤如下:
1. 首先在**非边界集上**选择能够使函数值足够下降的样本作为第二个变量
2. 如果非边界集上没有,则在整个样本集上选择第二个变量
3. 如果整个样本集依然不存在,则重新选择第一个变量

### 5.6 阈值b的计算
**每次完成两个变量的优化后**,要对b的值进行更新,因为b的值关系到f(x)的计算,即关系到下次优化时Ei的计算
* 如果$0<\alpha_1^{new}<C$,由KKT条件$y_1(w^Tx_1+b)=1$,得$\sum_{i = 1} ^ N \alpha_iy_iK_{i,1} + b = y_i$  
由此得  
$b_1^{new}={y_1-\sum_{i=3}^N \alpha_iy_iK_{i1}}-\alpha_1^{new}y_1K_{11}-\alpha_2^{new}y_2K_{21}$  
转换为Ei误差的形式为:  
$b_1^{new}=-E_1-y_1K_{11}(\alpha_1^{new}-\alpha_1^{old})-y_2K_{21}(\alpha_2^{new}-\alpha_2^{old})+b^{old}$
* 如果$0<\alpha_2^{new}<C$  
$b_2^{new}=-E_2-y_1K_{12}(\alpha_1^{new}-\alpha_1^{old})-y_2K_{22}(\alpha_2^{new}-\alpha_2^{old})+b^{old}$
* 如果同时满足$0<\alpha_i^{new}<C$,则$b_1^{new}=b_2^{new}$
* 如果同时不满足$0<\alpha_i^{new}<C$,则b1new 与b2new以及他们之间的数都满足KKT阈值条件,这时选择它们的中点