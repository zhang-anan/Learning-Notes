数据结构是计算机存储、组织数据的方式。  
数据结构是指相互之间存在一种或多种特定关系的数据元素的集合。  
**序列**是Python中最基本的数据结构，序列每个元素会被分配一个序号，也就是元素的位置，叫做索引。  
**Python3内置了7种序列类型：**
- List 列表
- Tuple 元组
- Ranges range函数
- Str 文本序列
- Binary 二进制(Bytes,bytearry,memoryview)
- Set,frozenset 集合
- Dict 字典

先了解列表
#### 列表
列表可以存储任何类型的数据，元素可以是字符串，数字等。
列表支持一系列的操作：
- append
```python
In [1]: friends = []
In [2]: friends.append('David')
In [3]: friends
Out[3]: ['David']
```
- extend  
append每次添加一个元素，如果要添加多个元素，可以用extend
```python
In [4]: friends.extend(['Chris','Any'])

In [5]: friends
Out[5]: ['David', 'Chris', 'Any']
```
- 列表分片  

索引从0开始,索引值可以写负数，表示从右向左数
```python
In [6]: friends[2]
Out[6]: 'Any'

In [7]: friends[-1]
Out[7]: 'Any'
```
还可以锁定索引范围
```python
In [8]: friends[1:3]
Out[8]: ['Chris', 'Any']

In [9]: friends[1:]
Out[9]: ['Chris', 'Any']

In [10]: friends[:1]
Out[10]: ['David']
```
- 分片步长  
步长为正，从左向右；步长为负，从右向左  
```python
In [11]: [0,1,2,3,4,5][:]
Out[11]: [0, 1, 2, 3, 4, 5]

In [12]: [0,1,2,3,4,5][0:6:1]
Out[12]: [0, 1, 2, 3, 4, 5]

In [13]: [0,1,2,3,4,5][0:6:2]
Out[13]: [0, 2, 4]

In [14]: [0,1,2,3,4,5][::-1]
Out[14]: [5, 4, 3, 2, 1, 0]
```
- 修改元素  
```python
In [15]: friends[0] = 'Andy'
In [17]: friends
Out[17]: ['Andy', 'Chris', 'Any']

In [18]: friends[1:3] = ['a','b','c']

In [19]: friends
Out[19]: ['Andy', 'a', 'b', 'c']
```
- insert  
往指定的位置插入
```python
In [20]: friends.insert(1,'d')

In [21]: friends
Out[21]: ['Andy', 'd', 'a', 'b', 'c']
```
- len  
列表的长度就是其中元素的个数,字符串，元组，字典都可以用len()来获取长度
```python
In [22]: len(friends)
Out[22]: 5
```
- 删除元素  
三种方式：知道索引用**del**关键字；从尾部去掉一个可用 **.pop()**,也指定弹出的索引 **.pop(index)**；不知道索引可用 **.remove('value')**，pop()会返回弹出的元素。
```python
In [24]: friends
Out[24]: ['Andy', 'd', 'a', 'b', 'c']

In [25]: del friends[0]

In [26]: friends
Out[26]: ['d', 'a', 'b', 'c']

In [27]: friends.pop()
Out[27]: 'c'

In [28]: friends.pop(0)
Out[28]: 'd'

In [29]: friends
Out[29]: ['a', 'b']

In [30]: friends.remove('b')

In [31]: friends
Out[31]: ['a']
```
remove如果元素不在列表里会报错  
```python
In [32]: friends.remove('f')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-32-d66a79a83c8c> in <module>()
----> 1 friends.remove('f')

ValueError: list.remove(x): x not in list
```
- 搜索元素  
通过内容查找索引，如果找不到会抛出异常  
.index(list, beg=0, end=len(list))
```python
In [33]: friends.index('a')
Out[33]: 0

In [34]: friends.index('f')
---------------------------------------------------------------------------
ValueError                                Traceback (most recent call last)
<ipython-input-34-b083d32e0304> in <module>()
----> 1 friends.index('f')

ValueError: 'f' is not in list
```
如果仅仅想知道元素是否存在的话，可用 **in**关键字
```python
In [35]: 'a' in friends
Out[35]: True
```
- 排序  
sorted()不影响原列表，.sort()会改变原列表
```python
In [36]: L = [1,3,2]

In [37]: sorted(L)
Out[37]: [1, 2, 3]

In [38]: L
Out[38]: [1, 3, 2]

In [39]: L.sort()

In [40]: L
Out[40]: [1, 2, 3]
```
- reverse  
reversed()函数和reverse方法让列表反向，可以与sorted联合使用
```python
In [41]: l = [1,2,3]
In [42]: reversed(l)
Out[42]: <list_reverseiterator at 0x7f2099150da0>
In [43]: list(reversed(l))
Out[43]: [3, 2, 1]

In [49]: l = [3,2,1]
In [50]: l.reverse()
In [51]: l
Out[51]: [1, 2, 3]

In [52]: sorted([1,3,2],reverse=False)
Out[52]: [1, 2, 3]

In [53]: sorted([1,3,2],reverse=True)
Out[53]: [3, 2, 1]
```
