# HashMap容器原理
* https://yikun.github.io/2015/04/01/Java-HashMap%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86%E5%8F%8A%E5%AE%9E%E7%8E%B0/

## 概述
`Hash table based implementation of the Map interface. This implementation provides all of the optional map operations, and permits null values and the null key. (The HashMap class is roughly equivalent to Hashtable, except that it is unsynchronized and permits nulls.) This class makes no guarantees as to the order of the map; in particular, it does not guarantee that the order will remain constant over time.`
* 基于Map接口实现
* 允许null 键/值
* 非同步
* 不保证有序(比如插入的顺序)

## 两个重要参数
* **容量capacity**:buckets的数目
* **负载因子load factor**: buckets填满程度的最大比例
* 如果对迭代性能要求高的话不要把`capacity`设置过大,也不要把`load factor`设置过小.当`bucket`填充的数目(即hashmap中元素的个数)大于`capacity * load factor`时就需要调整`buckets`的数目为当前的两倍

## put函数的实现
* 对`key`的`hashCode()`做hash,然后计算`index`
*  如果没碰撞直接放到bucket里面
    * 如果碰撞了,以**链表**形式保存在buckets后
        * 如果碰撞导致链表过长(*大于等于TREEIFY_THRESHOLD*),就把链表转换成**红黑树*
* 如果结点已经存在就替换`old value`**保证key的唯一性**
* 如果bucket满,就resize

## get函数的实现
* 如果是bucket里面第一个结点,直接命中
* 如果有冲突,则通过`key.equals(k)`去查找对应的entry
    * 若为树,则在树中通过`key.equals(k)`去查找
    * 若为链表,则在链表中通过`key.equals(k)`去查找

## 总结
#### HashMap的工作原理
