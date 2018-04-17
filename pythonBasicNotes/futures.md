```concurrent.futures``` 模块在python3.2开始是标准库，在python2中要安装futures模块。  
模块主要包含下面2个类：
1. ThreadPoolExecutor
2. ProcessPoolExecutor
也就是对threading和multiprocessing的进行了高级别的抽象，
暴露出统一的接口，方便开发者使用  

```python
# dwm32_1.py
  1 import time
  2 from concurrent.futures import ProcessPoolExecutor, as_completed
  3 NUMBERS = range(25, 38)
  4 def fib(n):
  5     if n<= 2:
  6         return 1
  7     return fib(n-1) + fib(n-2)
  8 start = time.time()
  9 with ProcessPoolExecutor(max_workers=3) as executor:
 10     for num, result in zip(NUMBERS, executor.map(fib, NUMBERS)):
 11         print('fib({}) = {}'.format(num, result))
 12 print('COST: {}'.format(time.time()-start))
```
输出：
```
fib(25) = 75025
fib(26) = 121393
fib(27) = 196418
fib(28) = 317811
fib(29) = 514229
fib(30) = 832040
fib(31) = 1346269
fib(32) = 2178309
fib(33) = 3524578
fib(34) = 5702887
fib(35) = 9227465
fib(36) = 14930352
fib(37) = 24157817
COST: 25.6186439991
```
