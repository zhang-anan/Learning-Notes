python的特点之一是有丰富的内置数据类型，让开发变得轻松简单，本节了解一下最常用的字符串方法和数值。  

#### 字符串  
字符串也就是一串字符，可以是字母、数字和标点符号举个例子：
```
In [2]: 'hello,' + ' world!'
Out[2]: 'hello, world!'
```
可以使用双引号，也可以使用简洁明了的单引号，如果一个字符串特别长，可以使用三重引号：
```
In [3]: '''
   ...: This
   ...: is
   ...: a Test
   ...: '''
Out[3]: '\nThis\nis\na Test\n'
```  
观察输出，多了\n，这是换行符，表示在这个位置有换行，可用print()函数打印一下：
```
In [4]: print('\nThis\nis\na Test\n')

This
is
a Test
```  
print在python3里是一个函数，但在python2里是一个关键字。  
**python中一切皆为对象**，一个对象具有行为能力，通过方法可以执行这些行为，举个例子： 
```
In [5]: 'abc'.upper()
Out[5]: 'ABC'
```
通过字符串的```upper```方法，可以拿到字符串大写的形式。方法后面加上()表示执行。  
**下面介绍一些字符串常用的方法：**
- endswith/startswith
```
In [6]: 'this'.endswith('t')
Out[6]: False

In [7]: 'this'.startswith('t')
Out[7]: True
```  
- lower/upper
```
In [8]: 'abc'.upper()
Out[8]: 'ABC'

In [9]: 'ABC'.lower()
Out[9]: 'abc'
```  
- split/rsplit
```
In [10]: 'a b c'.split()
Out[10]: ['a', 'b', 'c']

In [11]: 'a,b,c'.split(',')
Out[11]: ['a', 'b', 'c']

In [12]: 'a,b,c'.split()
Out[12]: ['a,b,c']

In [13]: 'a b c'.rsplit(None, 1)
Out[13]: ['a b', 'c']
```
split()默认以空格作为分隔符，rsplit(None, 1)表示从右向左分隔，保留第一个和剩下的。

- strip/lstrip/rstrip
```
In [14]: 'abc'.strip()
Out[14]: 'abc'

In [15]: ' abc'.lstrip()
Out[15]: 'abc'

In [17]: 'abc '.rstrip()
Out[17]: 'abc'
```
strip和其衍生的方法用于移除字符串头尾指定的符号，默认空格和换行符，也可以是其他符号，如：
```
In [19]: 'abaca'.strip('a')
Out[19]: 'bac'
```
- replace
```
In [23]: 'abc'.replace('a','d').replace('d','e')
Out[23]: 'ebc'
```
replace('a','d')表示把'a'替换成'd'
- partition/rpartition
```
In [24]: '/a/b/c'.partition('/')
Out[24]: ('', '/', 'a/b/c')

In [25]: '/a/b/c'.rpartition('/')
Out[25]: ('/a/b', '/', 'c')
```
partition和rpartion就是分区，会把字符串分成头部、指定字符和尾部三个部分。
- join
```
In [26]: ', '.join(['a','b','c'])
Out[26]: 'a, b, c'
```
join可以把一个序列或者可迭代对象拼接起来，先了解一下用法，它把一个列表里的元素用逗号和空格拼接了起来,**之前**用+连接大量字符串的时候是非常低效率的，因为+连接的时候会引起内存复制，垃圾回收这样的操作，**所以不要用下面的方法：**
```
s = ''
for p in parts:
    s += p
```
- format
```
In [27]: 'thi{}'.format('s')
Out[27]: 'this'
```
format也是一个常用的方法，用{}进行占位，format方法传进去，可以放弃%这种占位的用法。
- dir('') 可了解更多内置方法。
- 转义字符串
```
In [29]: '他说：\'你好\''
Out[29]: "他说：'你好'"
```
#### 数值类型
python3的数值类型由三种，整型、浮点型和复数。  
```+ - * / **```  
浮点数有个问题，不能精确的表示十进制数，看下面例子：
```
In [32]: 1.1 - 0.2
Out[32]: 0.9000000000000001

In [33]: 1.1 * 0.2
Out[33]: 0.22000000000000003
```
这是因为浮点数的数学运算产生了误差，底层CPU和IEEE754标准进行浮点数运算的特征。如果要更加精确且能容忍一定的性能损耗可使用Decimal模块：
```
In [34]: from decimal import Decimal

In [35]: Decimal('1.1') - Decimal('0.2')
Out[35]: Decimal('0.9')
```
布尔值属于整型，每个python对象天生具有布尔值。
