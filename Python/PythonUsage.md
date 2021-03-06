---
export_on_save:
 html: true
---

@import "/blog_head.md"

# Python 使用

## 数据结构与算法

### 压缩序列的赋值

压缩序列可以是如何可迭代对象。

```python {cmd}
a,b,c = [1,2,3]
print("1:", a,b,c)
a,b,c = "xyz"
print("2:", a,b,c)
a,*b,c = "1234567"
print("3:", a,b,c)
# 带*的解压出来都是list类型
a,*b,c="13"
print("4:", a,b,c)

def f1(a,*b):
    print("5:", a,b)
f1(1,3,4,5)

def f2(a,b):
    print("6:", a,b)
f2(*(1,2))
```

### collections.Counter

该类用于计算序列中出现次数最多的元素。
```python {cmd}
from collections import Counter
words = [
    'look', 'into', 'my', 'eyes', 'look', 'into', 'my', 'eyes',
    'the', 'eyes', 'the', 'eyes', 'the', 'eyes', 'not', 'around', 'the',
    'eyes', "don't", 'look', 'around', 'the', 'eyes', 'look', 'into',
    'my', 'eyes', "you're", 'under'
]
word_counts = Counter(words)
print(word_counts.most_common(3))
print(word_counts['into'])
word_counts.update(['into','into','eyes'])
print(word_counts.most_common(3))
```
`Counter`对象可以接受任意的由可哈希(hashable)元素构成的序列对象。

`Counter` 对象可以继续加减运算：
```python {cmd}
from collections import Counter
words1 = ['in', 'the', 'a', 'an', 'in', 'the', 'the']
words2 = ['the', 'a', 'with', 'in']
a = Counter(words1)
b = Counter(words2)
print(a)
print(b)
print(a+b)
print(a-b)
```
### 通过某个关键字排序字典列表
```python {cmd}
rows = [
    {'fname': 'Brian', 'lname': 'Jones', 'uid': 1003},
    {'fname': 'David', 'lname': 'Beazley', 'uid': 1005},
    {'fname': 'John', 'lname': 'Cleese', 'uid': 1001},
    {'fname': 'Big', 'lname': 'Jones', 'uid': 1004}
]
print("---sort by fname---")
for i in sorted(rows, key=lambda r:r['fname']):
    print(i)
print("\n---sort by uid (reverse)---")
for i in sorted(rows, key=lambda r:r['uid'], reverse=True):
    print(i)

print("\n---sort by multi-keywords(1)---")
for i in sorted(rows, key=lambda r:(r['lname'],r['fname'])):
    print(i)

# 使用 itemgetter 速率会稍稍快点
print("\n---sort by multi-keywords(2)---")
from operator import itemgetter
for i in sorted(rows, key=itemgetter('lname','fname')):
    print(i)

# max / min
print("\n---max / min---")
print(max(rows,key=lambda r:r['uid']))
print(min(rows, key=lambda r:r['fname']))
```

### 排序比支持原生比较的对象
```python {cmd}
class User:
    def __init__(self, user_id):
        self.user_id = user_id
    def __repr__(self):
        return f"User ({self.user_id})"
users = [User(23), User(3), User(99)]
print(sorted(users, key=lambda u:u.user_id))
from operator import attrgetter
print(sorted(users, key=attrgetter('user_id')))
```

### 通过某个字段将记录分组

```python {cmd}
from itertools import groupby
from operator import itemgetter
rows = [
    {'address': '5412 N CLARK', 'date': '07/01/2012'},
    {'address': '5148 N CLARK', 'date': '07/04/2012'},
    {'address': '5800 E 58TH', 'date': '07/02/2012'},
    {'address': '2122 N CLARK', 'date': '07/03/2012'},
    {'address': '5645 N RAVENSWOOD', 'date': '07/02/2012'},
    {'address': '1060 W ADDISON', 'date': '07/02/2012'},
    {'address': '4801 N BROADWAY', 'date': '07/01/2012'},
    {'address': '1039 W GRANVILLE', 'date': '07/04/2012'},
]
# groupby 之前需要先排序
rows.sort(key=itemgetter('date'))
for date, items in groupby(rows, key=itemgetter('date')):
    print(date, ":")
    for item in items:
        print("   ", item)
```

**注意：一个非常重要的准备步骤是在`groupby`之前进行数据排序，因为`groupby`仅仅检查连续的元素。**

### 过滤序列元素

```python {cmd}
mylist = [1,4,-5,10,-7,2,3,1]
# 求出大于0的元素
# 列表推导：缺点--输出非常大时需要产生一个大结果集，占内存
print([i for i in mylist if i>0])
# 生成器
g = (i for i in mylist if i>0)
for i in g:
    print(i, end=" ")
print()
# 将负数变为正数
print([i if i>0 else -i for i in mylist])

# 对于复杂情况 用filter，返回迭代器
values = ['1','2','-3','-','4','N/A','5']
def is_int(val):
    try:
        int(val)
        return True
    except ValueError:
        return False
print(list(filter(is_int, values)))

# 输出指定位置的数据 compress返回迭代器
from itertools import compress
output_e = [True]*len(mylist)
output_e[0]=output_e[3]=output_e[4]=False
print(list(compress(mylist, output_e)))
```


### 字典的最大值/最小值/排序
```python {cmd}
prices = {
    'ACME':45.23,
    'AAPL':612.78,
    'IBM':205.55,
    'HPQ':37.20,
    'FB':10.75
}
# 求键值最小/最大的键名
print(f"1 : {min(prices)}")
print(f"2 : {max(prices)}")
print(f"3 : {sorted(prices)}")
# 求值最小/最大的键名
print(f"4-1 : {min(zip(prices.values(), prices.keys()))[-1]}")
# lambda 后面跟的s是通过 in 可取的迭代器中的值
print(f"4-2 : {min(prices, key=lambda s:prices[s])}")
print(f"5-1 : {max(zip(prices.values(), prices.keys()))[-1]}")
print(f"5-2 : {max(prices, key=lambda s:prices[s])}")
print(f"6 : {[i[-1] for i in sorted(zip(prices.values(), prices.keys()))]}")

# 当值存在重复时 max的结果为key较大的
prices = {'AAA':20, 'ZZZ':20, 'BBB':20}
print(f"7 : {max(zip(prices.values(), prices.keys()))[-1]}")

# 寻找两个字典系统的键
a = {'x':1, 'y':2, 'z':3}
b = {'w':10, 'x':11, 'y':2}
# 集合求交操作
print(f"8-1 : {[i for i in a if i in b]}")
print(f"8-2 : {a.keys() & b.keys()}")
# 集合求差操作
print(f"9-1 : {[i for i in a if i not in b]}")
print(f"9-2 : {a.keys() - b.keys()}")
# 集合求异操作
print(f"10 : {a.keys()^b.keys()}")
# 集合并操作
print(f"11 : {a.keys()|b.keys()}")

# 寻找两字典键值对相等的item
print(f"12-1 : {[(i,a[i]) for i in a if i in b and a[i]==b[i] ]}")
print(f"12-2 : {a.items() & b.items()}")

# 输出指定键名的item
print(f"13-1 : { {i:a[i] for i in a.keys()-{'x'}} }")
print(f"13-2 : { {i:a[i] for i in a if i not in ['x']} }")
```
注：`zip()`函数创建的是一个只能访问一次的迭代器！

字典的`keys()`方法返回一个展现键集合的键视图对象，其支持集合操作：并(`|`)、交(`&`)、差(`-`)、求异(`^`)。这4中操作对 `set` 类型适用。如果想对集合的键执行一些普通的集合操作，可以直接使用键视图对象，而不用现将它们转换成一个`set`。

字典的`items()`也支持集合操作。但`values()`不支持集合操作。

### 从字典中提取子集

```python {cmd}
prices = {
    'ACME': 45.23,
    'AAPL': 612.78,
    'IBM': 205.55,
    'HPQ': 37.20,
    'FB': 10.75
}
# 获得值大于200的items
# 使用字典推导
p1 = {k:v for k,v in prices.items() if v>200}
print(p1)
tech_names = ["FB", "IBM", "ACME"]
p2 = {k:v for k,v in prices.items() if k in tech_names}
print(p2)
```

### 字典合并
```python {cmd}
from collections import ChainMap
a = {'x':1, 'z':2}
b = {'y':2, 'z':4}
c = ChainMap(a, b)
print("ChinaMap result:")
print(c['x'])
print(c['y'])
print(c['z'])
print(c)
print(list(c.keys()))
print(list(c.values()))
# ChainMap 只是存储了一个视图而已
print("\nChainMap just store a view:")
a['x'] = 10
print(c)
print(list(c.values()))
c['z'] = 20
print(a)

print("\ndict.update demo:")
# 也可使用update进行合并
d = dict(b)
d.update(a)
print(d)
# dict.update进行了拷贝而非视图
d['x'] = 30
print(a)
```

### 映射名称到序列元素 namedtuple

```python {cmd}
from collections import namedtuple
Subscriber = namedtuple('Subscriber', ['addr', 'joined'])
sub = Subscriber('jonesy@example.com', '2012-10-19')
print(sub)
print(sub.addr)
print(sub.joined)

# 支持所有的普通元组操作
print(len(sub))
print(sub[-1])
addr, joined = sub
print(addr, joined)
```

**命名元组的一个主要用途是将你的代码从下表操作中解脱出来。**

```python
# 普通元组
def compute_cost(records):
    total = 0.0
    for rec in records:
        total += rec[1]*rec[2]
    return total
# namedtuple
from collections import namedtuple
Stock = namedtuple('Stock', ['name', 'shares', 'price'])
def compute_cost(records):
    total = 0.0
    for rec in records:
        s = Stock(*rec)
        total += s.shares * s.price
    return total
```

**命名元组的另一个用途就是作为字典的替代，因为字典存储需要更多的内存空间。如果你需要构建一个非常大的包含字典的数据结构，那么使用命名元组会更加高效。但是：一个命名元组是不可更改的！**

如果真的需要改变属性的值，那么可以使用命名元组实例的 `_replace()`方法，它会创建一个全新的命名元组并将对应的字段用新的值取代：
```python {cmd}
from collections import namedtuple
Stock = namedtuple('Stock', ['name', 'shares', 'price'])
s = Stock('ACME', 100, 73.5)
print(s)
s = s._replace(shares=80)
print(s)
```

### 转换并计算数据

问题：你需要在数据序列上执行聚集函数，比如 `sum()`、`min()`、`max()`，但首先需要转换或过滤数据：
```python {cmd}
nums = [1,2,3,4,5]
# 求平方和
s = sum(x**2 for x in nums)
print(s)
```

注意：在`for`中的语句执行1遍，其他的执行循环的次数：
```python {cmd}
def f(x):
    print("call f")
    return x**2
a = [1,2,3,4,5,6,7]
print("---test 1---")
sum(x for x in range(f(3)))

print("\n---test 2---")
sum(f(x) for x in range(3))

print("\n---test 3---")
sum(x for x in a if x < f(3))
```
上面演示的是生成器表达式作为一个单独参数传递给函数时候的巧妙语法（并不需要多加一个括号），例如：
```python {cmd}
nums = [1,2,3,4,5]
print(sum(n for n in nums))
print(sum((n for n in nums)))
```

### 删除序列系统元素并保持顺序

```python {cmd}
a = [1,5,2,1,9,1,5,10]
# 会打乱顺序
print(1, ":", list(set(a)))
def dedupe(items):
    seen = set()
    for item in items:
        if item not in seen:
            yield item
        seen.add(item)
print(2, ":", list(dedupe(a)))
```
上面的方法只在序列中元素为hashable的时候才管用。消除不可哈希（如`dict`类型）的序列中重复元素：
```python {cmd}
a = [{'x':1, 'y':2}, {'x':1, 'y':3}, 
{'x':1, 'y':2}, {'x':2, 'y':4}]
def dedupe(items, key=None):
    seen = set()
    for item in items:
        val = item if key==None else key(item)
        if val not in seen:
            yield item
        seen.add(val)
print(list(dedupe(a, lambda s:(s['x'],s['y']))))
print(list(dedupe(a, lambda s:s['x'])))
```

### 命名切片

将切片从硬切片中独立出来：

```python {cmd}
record = '....................100 .......513.25 ..........'
# 硬切片方式
print(int(record[20:23])*float(record[31:37]))
# 命名切片方式
SHARES = slice(20, 23)
PRICE = slice(31,37)
print(int(record[SHARES])*float(record[PRICE]))
```

命名切片的优点：**一般来说，代码中如果出现大量的硬编码下标值，会使得可读性和可维护性大大降低，命名切片可以清晰地表达代码的逻辑。**

`slice`的使用：
```python {cmd}
items = [0,1,2,3,4,5,6]
a = slice(2,5,2)
print(a)
print(a.start, a.stop, a.step)
items[a] = [10,20]
print(items)
```

### defaultdict 模块

用于构建一个键对应多个值得字典（也称为 **multidict**）；

#### 使用 dict+容器
```python
d = {
    'a':[1,2,3],
    'b':[4,5]
}
e = {
    'a':{1,2,3},
    'b':{4,5}
}
```

#### 使用 collections.defaultdict
```python {cmd}
from collections import defaultdict
d = defaultdict(list)
d['a'].append(1)
d['a'].append(2)
d['b'].append(4)

e = defaultdict(set)
e['a'].add(1)
e['a'].add(2)
e['b'].add(4)
print(d)
print(e)
```

#### 自动创建键
`defaultdict`会自动为将要访问的键（就算当前字典中并不存在这样的键）创建映射实体。如果你并不需要这样的特性，你可以在一个普通的字典上使用`setdefault()`方法替代：
```python {cmd}
d = {}
d.setdefault('a',[]).append(1)
d.setdefault('a',[]).append(2)
d.setdefault('b',[]).append(4)
print(d)
```

使用`defaultdict`:
```python {cmd}
from collections import defaultdict
d = defaultdict(list)
d['a'].append(1)
d['a'].append(2)
d['b'].append(4)
print(d)
```

### heapq 模块

#### 获得集合中最大/最小的N歌元素列表
```python {cmd}
import heapq
nums = [12,33,41,64,75,27,94,67,28]
print(heapq.nlargest(3, nums))
print(heapq.nsmallest(3, nums))
```

两个函数都能接收一个关键字参数：
```python {cmd}
import heapq
portfolio = [
    {'name': 'IBM', 'shares': 100, 'price': 91.1},
    {'name': 'AAPL', 'shares': 50, 'price': 543.22},
    {'name': 'FB', 'shares': 200, 'price': 21.09},
    {'name': 'HPQ', 'shares': 35, 'price': 31.75},
    {'name': 'YHOO', 'shares': 45, 'price': 16.35},
    {'name': 'ACME', 'shares': 75, 'price': 115.65}
]
print(heapq.nlargest(2, portfolio, lambda s:s['price']))
print(heapq.nsmallest(2, portfolio, lambda s:s['price']))
```

#### 堆排序
```python {cmd}
import heapq
nums = [12,33,41,64,75,27,94,67,28]
a = list(nums) # 相当于 nums.copy()
heapq.heapify(a)
print(a)
```

#### 注意

- 当求最大/最小的一个值时，使用`max()/min()`
- 当求的元素个数较少，但不是一个时，使用`heapq.nlargest() / heapq.nsmallest()`
- 当求的元素个数较多，接近于集合个数时，使用`sorted(a)[:N] / sorted(a, reverse=True)[:N]`

#### 按优先级排序的队列

```python {cmd}
import heapq
class PriorityQueue:
    def __init__(self):
        self._queue = []
        self._index = 0
    def push(self, item, priority):
        heapq.heappush(self._queue, (-priority, self._index, item))
        self._index += 1
    def pop(self):
        return heapq.heappop(self._queue)[-1]
class Item:
    def __init__(self, name):
        self.name = name
    def __repr__(self):
        return f"Item({self.name})"
q = PriorityQueue()
q.push(Item('foo'), 1)
q.push(Item('bar'), 5)
q.push(Item('spam'), 4)
q.push(Item('grok'), 1)
print(q.pop())
print(q.pop())
print(q.pop())
print(q.pop())
```

### collections 模块

#### deque

deque (double-ended queue) 双端队列，是一种同时具有队列和堆栈性质的数据结构。

```python {cmd}
from collections import deque
q = deque(maxlen=3)
q.append(1)
q.append(2)
q.append(3)
print(q)
q.append(4)
print(q)
q.appendleft(5)
print(q)
q.pop()
print(q)
q.popleft()
print(q)
```
可以通过`list`实现，但使用`collections.deque`队列方案更加优雅，并且运行更快。

在队列两端插入或删除元素的时间复杂度为$O(1)$，而在列表的开头插入或删除元素的时间复杂度为$O(N)$

### yield 的使用

#### 可迭代对象

像`list`、`tuple`、`dict`、`文件`等可以用`for . in ...`语法进行遍历的类型称为可迭代对象；你把所有的值都存放在内存中，然后逐个取出，当可迭代对象作用内存非常大时，这种方法不可取。
```python {cmd}
a = [x*x for x in range(3)]
print(a)
for i in a:print(i, end=" ")
else: print()
for i in a:print(i, end=" ")
else: print()
```

#### 生成器

生成器是可迭代的，但是你 **只可以读取它一次**，因为它不是将所有数据都存放在内存中，而是实时生成的。

生成器在创建时用小括号：
```python {cmd output="text"}
b = (x*x for x in range(3))
print(b)
for i in b:print(i, end=" ")
else: print()
for i in b:print(i, end=" ")
else: print()
```
在运行的过程中计算出值。

#### yield

`yield` 是一个类似于 `return` 的关键字，只是这个函数返回的是生成器。

```python {cmd}
def create_generator():
    for i in range(3):
        yield i
g = create_generator()
for i in g:print(i, end=" ")
print()
for i in g:print(i, end=" ")
```

### 丢失的三元运算符 ?:

在C、C++、JavaScript中有三元运算符 `?:`：
```node {cmd}
a = 5
b = 9
console.log(a>b?a:b)
```
python 中的代价语法：
```python {cmd}
a = 5
b = 9
print(a if a>b else b)
```

### 丢失的for语句
在C、C++、JavaScript中有`for`语句：
```node {cmd}
for(i=0; i<10; ++i){
    if(i%2==0){
        console.log(i)
    }
}
```

在Python中：
```python {cmd}
[print(i, end=" ") for i in range(10) if i%2==0]
```

### Python的格式化字符串

#### 使用\%操作符

从版本1.0就存在的字符串格式化方式：
```python {cmd}
name = 'Fred'
height = 176.23
print("My name is %s, height is %.1f cm" %(name, height))
```

#### 使用string.format
从版本2.6开始引入：
```python {cmd}
name = 'Fred'
height = 176.23
print("My name is {0}, height is {1:.1f} cm".format(name, height))
print("My name is {sname}, height is {sheight:.1f} cm".format(sheight=height, sname=name))
```

#### 使用 f 字符串
从版本3.6开始引入：
```python {cmd}
name = 'Fred'
height = 176.23
print(f"My name is {name}, height is {height:.1f} cm")
```


## 内部函数

- `dir`：用于显示模块/类的属性

## Python的类

**Python** 中并不存在真正私有的方法。

### Python类中属性/方法命名

#### 以一个下划线开头 _foo

使用这种命名的原因：
- 不希望它被直接import
- 不希望它被直接访问
- 只是测试中的 function

例如，我们写了一个 `test.py`:
@import "test.py" {cmd="python"}

**真的不能`import`?**

```python {cmd}
from test import *
public_func()
_private_func()
```
上面的代码说明不能通过`from module_name import *` 的方式使用`_foo`函数；

```python {cmd}
from test import _private_func
_private_func()
```
但可以通过 `from modele_name import _foo` 的方式使用`_foo`函数；



#### 以一个下划线结尾 foo_

如`foo_`，这种方式主要是为了避免与Python的 **built-in keywords** 和 **built-in functions** 取一样的名字。

1. 如果命名与**built-in functions**相同：
```python {cmd}
print(list("abc"))
list = 12
print(list("abc"))
```
会造成原 **built-in functions** 不能使用，改进为：
```python {cmd}
print(list("abc"))
list_ = 12
print(list("abc"))
```

2. 如果命名与**built-in keywords**相同：
```python {cmd}
is=12
```
将直接抛出一个语法异常。


- 双下划线开头：在类成员中表示名字改编，这是为了使其避免与子类中的名字冲突

#### \_\_foo\_\_

不要使用，除非你是 Python 的核心开发人员。

#### \_\_foo

```python {cmd}
class A:
    def __foo1(self):
        print("called A::__foo1")
    def foo2(self):
        print("called A::foo2")
    def f(self):
        print("\n--- f ---")
        self.__foo1()
        self.foo2()
        print("-"*9, end="\n"*2)

a = A()
print(dir(a)[:3], "...")
# 实现了变名
a._A__foo1()

a.f()

class B(A):
    def __foo1(self):
        print("called B::__foo1")
    def foo2(self):
        print("called B::foo2")
b = B()
b.f()
```

**总结：**
- 如果要引用`__foo`函数，需要通过变换后的名：`_modulename__foo`
- `__foo` 类似的方法/属性不能被子类重写；

### 类的内置接口

#### \_\_repr\_\_

```python {cmd}
class A:
    def __repr__(self):
        return "I am A"
print(A())
```


## 获取全排列

```python {cmd}
import itertools
for i in itertools.permutations("abc"):
    print(i)
```

## 字符串和文本

### 使用多个界定符分割字符串

```python {cmd}
line = "asdf  fjk;  fj,ek, foo"
import re
print(re.split(r'[\s\;\,]+', line))
fields = re.split(r'([;,\s]+)', line)
print(fields)
print(fields[::2])

# 捕获与非捕获
print(re.split(r'(\s|,|;)+',line))
# (?:...)表示非捕获
print(re.split(r'(?:\s|,|;)+',line))
```
使用`re.split()`函数时，需要特别注意的是正则表达式中是否包含一个括号捕获分组。如果使用了分组捕获，那么被匹配的文本也将出现在结果列表中；


### 字符串开头或结尾匹配

```python {cmd}
import re
# 使用 str.startswith str.endswith
filename = "spam.txt"
print(filename.startswith('file:'))
print(filename.endswith('.txt'))
print(filename[-4:]==".txt")
print(re.match('[\s\S]*\.(txt|c)$', filename))
print(filename.endswith(('.txt','c')))

# 检查匹配并归类
filenames = ['Makefile','foo.c','bar.py','spam.c','spam.h']
print([f for f in filenames if f.endswith(('.c','.h'))])
print(any(f.endswith('py') for f in filenames))
```

### 用 shell 通配符匹配字符串

你可以使用Unix Shell 中常用的通配符（比如：*.py，Dat[0-9]*.csv等）去匹配文本字符串。
```python {cmd}
from fnmatch import fnmatch, fnmatchcase
print(fnmatch('foo.txt', '*.txt'))
print(fnmatch('foo.txt', '?oo.txt'))
print(fnmatch('Dat45.csv','Dat[0-9]*'))
# 大小写敏感(mac os)
print(fnmatch('foo.txt', '*.TXT'))
print(fnmatchcase('foo.txt', '*.TXT'))

addresses = [
    '5412 N CLARK ST',
    '1060 W ADDISON ST',
    '1039 W GRANVILLE AVE',
    '2122 N CLARK ST',
    '4802 N BROADWAY',
]
print([addr for addr in addresses if fnmatch(addr, '* ST')])
print([addr for addr in addresses if fnmatch(addr, '54[0-9]*CLARK*')])
```

`fnmatch()`函数的匹配能力介于简单的字符串方法和强大的正则表达式之间。如果在数据处理操作中只需要简单的通配符就能完成的时候，这通常是一个比较合理的方案。

如果你的代码需要做文件名的匹配，最好使用glob模块。

### 字符串匹配和搜索

```python {cmd}
# 简单的搜索
text = 'yeah, but no, but yeah, but no, but yeah'
print(text.find('no'))
# 对于复杂的匹配，使用 re 模块
import re
text1 = '11/27/2012'
text2 = 'Nov 27, 2012'
datepat = re.compile(r'\d+/\d+/\d+')
# 方式1：re.match
print('yes' if re.match(datepat, text1) else 'no')
# 方式2：pat.match
print('yes' if datepat.match(text2) else 'no')

text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
# re.match 总是从字符串开始去匹配
print('yes' if datepat.match(text) else 'no')
# 任意位置查找
print(datepat.findall(text))

# 在定义正则式时，通常会利用括号去捕获分组
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
m = datepat.match('11/27/2012')
print(m.group(0), m.group(1), m.group(2), m.group(3))
print(m.groups())

print(datepat.findall(text))
# 时间格式转换
for month, day, year in datepat.findall(text):
    print(f'{year}-{month}-{day}')
# findall 以列表形式返回，finditer 以生成器方式返回
for m in datepat.finditer(text):
    month, day, year = m.groups()
    print(f'{year}-{month}-{day}')

# match 只检查字符串的开头部分
m = datepat.match("11/27/2012ddasdf")
print('yes' if m else 'no')
```
注意：如果你打算做大量的匹配和搜索操作的话，最好先编译正则表达式，然后再重复使用它。模块级别的函数会将最近编译过的模式缓存起来，不会有太多的性能消耗，但如果使用预编译模式的话，你将会减少查找和一些额外的处理损耗。

### 字符串搜索和替换

```python {cmd}
# 简单的字符串替换
text = 'yeah, but no, but yeah, but no'
print(text.replace('yeah', 'yep'))
# 复杂的字符串替换
import re
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
datepat = re.compile(r'(\d+)/(\d+)/(\d+)')
print(datepat.sub(r'\3-\1-\2', text))

# 可读性更强的一种方式
# (?P<name>\d+) 表示捕获为name变量
# \g<name> 使用name
datepat = re.compile(r'(?P<month>\d+)/(?P<day>\d+)/(?P<year>\d+)')
print(datepat.sub(r'\g<year>-\g<month>-\g<day>', text))
```

对于更加复杂的替换，可以传递一个替换回调函数来替代：
```python {cmd}
import re
from calendar import month_abbr
text = 'Today is 11/27/2012. PyCon starts 3/13/2013.'
datepat = re.compile(r'(?P<m>\d+)/(?P<d>\d+)/(?P<y>\d+)')
def change_date(m):
    mon_name = month_abbr[int(m.group('m'))]
    return f"{m.group('d')} {mon_name} {m.group('y')}"
print(datepat.sub(change_date, text))
# 第一个返回值为替换结果，第二个返回值为替换次数
print(datepat.subn(change_date, text))
```

### 字符串忽略大小写的搜索替换

```python {cmd}
import re
text = 'UPPER PYTHON, lower python, Mixed Python'
print(re.findall(r'python', text, flags=re.IGNORECASE))
print(re.sub(r'python', 'snake', text, flags=re.IGNORECASE))

def matchCase(word):
    def replace(m):
        text = m.group()
        if text.isupper():
            return word.upper()
        elif text.islower():
            return word.lower()
        elif text[0].isupper():
            return word.capitalize()
        else:
            return word
    return replace
print(re.sub('python',matchCase('snake'),
    text ,flags=re.IGNORECASE))
```
注：上面的后面的例子中，`matchCase('snake')`返回了一个回调函数。

### 最短匹配模式

你正在使用正则表达式匹配某个文本模式，但是它找到的可能是最长匹配。修改变为最短匹配：
```python {cmd}
import re
text1 = 'Computer says "no."'
text2 = 'Computer says "no." Phone says "yes."'
str_pat = re.compile(r'\"(.*)\"')
print(str_pat.findall(text1))
# 最长匹配的例子(贪婪模式)
print(str_pat.findall(text2))
# 使用最短匹配 在匹配模式后加上 ? (懒惰模式)
print(re.findall(r'\"(.*?)\"', text2))
```

### 多行匹配模式
```python {cmd}
import re
comment = re.compile(r'/\*(.*?)\*/')
text1 = '/* This is one-line of comment */'
text2 = """
/*This is multi-line
 comment*/
"""
print(comment.findall(text1))
print(comment.findall(text2))
# '?'在后面表示最短匹配，'?:'在捕获组的前面表示不进行捕获
comment = re.compile(r'/\*([\s\S]*?)\*/')
print(comment.findall(text2))
comment = re.compile(r"/\*((?:.|\n)*)\*/")
print(comment.findall(text2))
# 使用 re.DOTALL 让点匹配所有字符
print(re.findall(r'/\*(.*)\*/', text2, flags=re.DOTALL))
```

findall 使用的注意点：
1. `re.M` 与有 `^ ... $` 的正则式搭配使用
1. `re.compile` 进行编译是就需要指定 `flag`，之后的 `findall` 中的 flag 将被忽略

### 删除字符串中不需要的字符
```python {cmd}
import re
# 使用 str.strip、str.lstrip、str.rstrip
# 删除开头/结尾的指定字符串 不会删除中间的字符
s = '   hello   world  '
print(f"'{s.strip()}'")
print(f"'{s.lstrip()}'")
print(f"'{s.rstrip()}'")
# strip 的参数中字符是或的关系
print(f"'{s.strip(' hd')}'")
# 删除中间的空格
print(f"'{s.strip().replace(' ','')}'")
# 删除中间多余的空格

print("'{0}'".format(re.sub(r'\s+', ' ', s.strip())))
```

### 审查清理文本数据字符串
```python {cmd}
# 改写指定字母
text = 'life is short, please use python'
print(text.translate({
    ord('s'):'S',
    ord('p'):'pop'
}))
```

### 字符串对齐
```python {cmd}
hw = "hello,world"
print(f"'{hw.ljust(20)}'")
print(f"\'{format(hw, '<20')}\'")
print("'{:<20}'".format(hw))

print(f"'{hw.rjust(20)}'")
print(f"\'{format(hw, '>20')}\'")
print("'{:>20}'".format(hw))

print(f"'{hw.center(20)}'")
print(f"\'{format(hw, '^20')}\'")
print("'{:^20}'".format(hw))

print(f"'{hw.center(20,'*')}'")
print(f"\'{format(hw, '*^20s')}\'")
print("'{:*^20s}'".format(hw))

print(f"\'{hw:*^20s}\'")

# 格式化数字
x = 1.2345
print(f"{x:>10}|")
print(f"{x:<10}|")
# 默认 . 后面是精确位数
print(f"{x:^+10.3}|")
# 加了f后表示小数点后几位
print(f"{x:^+10.3f}|")
print(f"{x:$^10.2}|")
```

### 合并拼接字符串

将几个小的字符串合并为一个。
```python {cmd}
# 如果要合并的字符串时一个序列或iterable
parts = ['Is', 'Chicago', 'Not', 'Chicago']
parts_dic = {'a':'Is', 'd':'Chicago', 'b':'Not', 'c':'Chi'}
print(' '.join(parts))
print(' , '.join(parts))
print(''.join(parts))
print(' '.join(parts_dic))
# 用 + 连接
a = 'Is Chicago'
b = "Not Chicago"
print(a + ' ' + b)
# 两个字符串字面量直接并排合并
print('Hello' ' ' 'world')
```
编写字符串相关的程序时，要注意效率：
```python {cmd}
data = ['ACME', 50, 91.1]
# 不可取的做法, +操作符存在大量内存复制、垃圾回收
s = ''
for i in data:
    # + 操作存在
    s += str(i) + ','
print(s[:-1])
# 好一些的做法
s = ','.join(str(i) for i in data)
print(s)

a = '1'; b='b'; c='3'
# ugly mode
print(a + ':' + b + ':' +c)
# still ugle
print(':'.join([a,b,c]))
# better
print(a,b,c,sep=":")
```

当混合使用I/O操作和字符串操作时，有时候需要仔细研究你的程序：
```python
# Versino 1 : string connection
# 如果s1、s2较小时合适，i/o操作通常较慢
with open('filename') as f:
    f.write(s1 + s2)

# Version 2 : seperate I/O operations
# 如果s1、s2较大时合适，避免 + 的大量内存占用 
with open('filename') as f:
    f.write(s1)
    f.write(s2)
```

大量小字符串的输出：
```python {cmd}
def sample():
    yield 'Is'
    yield 'Chicago'
    yield 'Not'
    yield 'Chicago'

print(' '.join(sample()))
for i in sample():
    print(i)
```

### 字符串中的变量替换
```python {cmd}
name = 'Fred'
weight = 71.32
# 原始版本
print('I am %s, weight is %.1f' %(name, weight))
# f 字符串版本
print(f'I am {name} ,weight is {weight:#^10.1f}' %vars())
```

### html 和 xml 的转换

```python
import html
# 将html中的特殊字符进行转换，使其能够在html中显示
text = 'This is <strong> "strong text" </strong>'
print(text)
print(html.escape(text))
# html.escape的第二个参数quote指示是否替换冒号
print(html.escape(text, quote=False))
# html.escape 的反过程
print(html.unescape('This is &lt;strong&gt; &quot;strong text&quot; &lt;/strong&gt;'))
```
```
This is <strong> "strong text" </strong>
This is &lt;strong&gt; &quot;strong text&quot; &lt;/strong&gt;
This is &lt;strong&gt; "strong text" &lt;/strong&gt;
This is <strong> "strong text" </strong>
```

### 字符串令牌解析
目标：将`text = 'foo = 23 + 42 * 10'`
解析为：`   tokens = [('NAME', 'foo'), ('EQ','='), ('NUM', '23'), ('PLUS','+'),
          ('NUM', '42'), ('TIMES', '*'), ('NUM', '10')]`

```python {cmd}
import re
# 定义令牌
NAME = r'(?P<NAME>[a-zA-Z][a-zA-Z0-9_]*)'
NUM = r'(?P<NUM>\d+)'
PLUS = r'(?P<PLUS>\+)'
TIMES = r'(?P<TIMES>\*)'
EQ = r'(?P<EQ>=)'
WS = r'(?P<WS>\s+)'
master_pat = re.compile('|'.join([NAME, NUM, PLUS, TIMES, EQ, WS]))
# 使用scanner进行扫描 方法1
print("{:*^30s}".format(' scanner method 1 '))
scanner = master_pat.scanner('foo = 42')
while True:
    m = scanner.match()
    if m:
        print((m.lastgroup, m.group()))
    else:
        break
print('{:*^30s}'.format(' scanner method 2 '))
# 方式2
scanner = master_pat.scanner('foo = 42')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
# 方式3
print('{:*^30s}'.format(' scanner method 3 '))
from collections import namedtuple
def generate_token(pat, text):
    Token = namedtuple('Token', ['type','value'])
    scanner = pat.scanner(text)
    for m in iter(scanner.match, None):
        yield Token(m.lastgroup, m.group())
for tok in generate_token(master_pat, 'foo = 42'):
    print(tok)
# 过滤生成的符号
print('{:*^30s}'.format(' filter the tokens '))
tokens = (t for t in generate_token(master_pat, 'foo = 42') 
    if t.type!='WS')
for t in tokens:
    print(t)
```
通常来讲，令牌化是很多高级文本解析与处理的第一步，为了使用上面的扫描方法，你需要记住：
1. 你必须确认你使用正则表达式指定了所有输入中可能出现的文本序列，如果有任何不可匹配的文本出现，扫描就会直接停止；
2. 令牌顺序也是有影响的，`re` 模块会按照指定好的顺序去做匹配，因此如果一个模式恰好是另一个更长模式的子字符串，你需要将长模式写在前面，如：
```python
import re
LT = r'(?P<LT><)'
LE = r'(?P<LE><=)'
EQ = r'(?P<EQ>=)'
# incorrect method
print('{:*^30s}'.format('incorrect method'))
master_pat = re.compile('|'.join([LT, LE, EQ]))
scanner = master_pat.scanner('<=')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
print('{:*^30s}'.format('correct method'))
master_pat = re.compile('|'.join([LE, LT, EQ]))
scanner = master_pat.scanner('<=')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
```
```
*******incorrect method*******
('LT', '<')
('EQ', '=')
********correct method********
('LE', '<=')
```
3. 留意子字符串形式的模式，比如你有以下两个模式：
```python {cmd}
import re
PRINT = r'(?P<PRINT>print)'
NAME = r'(?P<NAME>[a-zA-Z_][a-zA-Z0-9_]*)'
master_pat = re.compile('|'.join([PRINT, NAME]))
scanner = master_pat.scanner('printer')
for m in iter(scanner.match, None):
    print((m.lastgroup, m.group()))
```
更高阶的令牌化技术可能需要查看 *PyParsing* 或者 *PLY* 包。

### 一个简单的递归下降分析器

要求：根据一组语法规则解析文本并执行命令，或者构造一个代表输入的抽象语法树，如果语法非常简单，你可以自己写这个解析器，而不是使用一些框架。

解决方案：
在这个问题中，我们集中讨论特殊语法去解析文本的问题。为了这样做，你首先要以BNF或EBNF形式指定一个标准语法，比如，一个简单数学表达式语法可能像下面：
```
expr ::= expr + term
    | expr - term
    | term
term ::= term * factor
    | term /  factor
    | factor
factor ::= (expr)
    | NUM
```
或者，以EBNF形式：
```
expr ::= term {(+|-) term}*
term ::= factor {(*|/) factor}*
factor ::= (expr)
    | NUM
```
在EBNF中，包含在 {...}* 中的规则是可选的。* 表示0次或多次重复。

对于复杂的语法，你最好是选择某个解析工具，如PyParsing 或者是 PLY，下面是使用PLY写的表达式求值程序：
```python {cmd}
from ply.yacc import yacc
from ply.lex import lex

# Token list
tokens=['NUM','PLUS','MINUS','TIMES','DIVIDE','LPAREN','RPAREN']
# Ignored characters
t_ignore = ' \t\n'
# Token specifications (as regexs)
t_PLUS = r'\+'
t_MINUS = r'\-'
t_TIMES = r'\*'
t_DIVIDE = r'/'
t_LPAREN = r'\('
t_RPAREN = r'\)'

# Token processing functions
def t_NUM(t):
    r'\d+'
    t.value = int(t.value)
    return t

# error handler
def t_error(t):
    print('Bad character: {!r}'.format(t.value[0]))
    t.skip(1)

# bulid the lexer
lexer = lex()

# Grammer rules and handler functions
def p_expr(p):
    '''
    expr : expr PLUS term
        | expr MINUS term
    '''
    if p[2] == '+':
        p[0] = p[1] + p[3]
    elif p[2] == '-':
        p[0] = p[1] - p[3]

def p_expr_term(p):
    '''
    expr : term
    '''
    p[0] = p[1]

def p_term(p):
    '''
    term : term TIMES factor
        | term DIVIDE factor
    '''
    if p[2] == '*':
        p[0] = p[1]*p[3]
    elif p[2] == '/':
        p[0] = p[1]/p[3]

def p_term_factor(p):
    '''
    term : factor
    '''
    p[0] = p[1]

def p_factor(p):
    '''
    factor : NUM
    '''
    p[0] = p[1]

def p_factor_group(p):
    '''
    factor : LPAREN expr RPAREN
    '''
    p[0] = p[2]

def p_error(p):
    print('Syntax error')

parser = yacc()

print(parser.parse('2'))
print(parser.parse('2+3'))
print(parser.parse('2*(3+4) + 5'))
```

### 字节字符串上的字符串操作
```python {cmd}
# 字节字符串同样也支持大部分和文本字符串一样的内置操作
data = b'Hello World'
print(data[:5])
print(data.startswith(b'Hello'))
print(data.split())
print(data.replace(b'Hello', b'Hello Cruel'))
# 这些操作同样适用于字节数组
data = bytearray(b'Hello World')
print(data)
print(data.startswith(b'Hello'))
print(data.split())
print(data.replace(b'Hello', b'Hello Curel'))

# 可以用正则表达式匹配字节字符串
data = b'FOO:BAR,SPAM'
import re
print(re.split(b'[:,]+', data))

# 大多数情况下，在文本字符串上的操作均可以用于字节字符串
# 不同点1：字节字符串索引操作返回整数而不是单个字符
a = 'Hello World'
b = b'Hello World'
print(f'{a[0]} , 0x{b[0]:x}')
print(a[0], b[0])
# 不同点2：打印出来为 b'...'
print(b)
print(b.decode('ascii'))
# 不同点3：不能格式化
try:
    print(b'{:x}'.format(12))
except:
    print('catch a except')
# 实现的方式
print('{:x}'.format(12).encode('ascii'))
```
一些程序员为了提升程序执行的速度，会倾向于使用字节字符串而不是我蹦字符串，尽管操作字节字符串确实会比文本更加高效（因为厂里文本固有的Unicode相关开销），但通常会导致代码杂乱。你会经常发现字节字符串并不能和Python的其他部分工作，需要手动 encode/decode 操作。


## 数字日期和时间

在Python中执行整数和浮点数的数字运算是很简单。如果你需要执行分数、数组或者是日期和时间的运算，就需要做更多工作。

### 数字的四舍五入
```python {cmd}
print(round(1.24,1))
# 当一个值更好在两个边界，round函数返回离它最近的偶数
print(round(1.5,0))
print(round(2.5,0))
# round(number [,ndigits]), ndigits可以为负，表示小数前几位
print(round(12345, -3))
# 如果仅仅是为了显示，用str.format
print('{:-.0f}'.format(1.5))
# 不要试着去舍入浮点来“修正”表面上看起来正确的问题
a = 2.1
b = 4.2
c = a + b
print(c)
c = round(c, 2)
print(c)
```
对于大多数使用到浮点的程序，没有必要也不推荐怎么做，尽管在计算的时候回有一点点小的误差，但是这些小的误差是能被理解和容忍的。如果不能允许这些小误差（比如涉及到金融领域），就需要考虑使用 decimal 模块了。

### 执行精确的浮点数运算

浮点数的一个普遍问题是它们不能精确表示十进制数，并且即使是最简单的数学运算也会产生小的误差：
```python {cmd}
a = 0.2
b = 0.1
c = a + b
print(c)
print(c==0.3)
# 正确的判断
import math
print(math.isclose(c, 0.3))
```
这些错误是有底层CPU和IEEE754标准通过自己的浮点单元去执行算数时的特征。由于Python的浮点数据类型使用底层表示存储数据，因此你没办法去避免这样的误差。

如果你想更加精确（并能容忍一定的性能损耗），你可以使用decimal模块：
```python {cmd}
from decimal import Decimal
from decimal import localcontext
a = Decimal('4.2')
b = Decimal('2.1')
print(a+b)
print(a+b==Decimal('6.3'))
a = Decimal('1.3')
b = Decimal('1.7')
print(a/b)
with localcontext() as ctx:
    ctx.prec = 3
    print(a/b)
with localcontext() as ctx:
    ctx.prec = 7
    print(a/b)
```

decimal 模块实现了 IBM 的“通用小数运算规范”。

Python新手会倾向于使用decimal模块来处理浮点数的精确运算。然而，先理解你的应用程序目的是非常重要的。如果你是在做科学计算或者工程领域的计算、电脑绘图或者是科学领域的大多数运算，那么使用普通的浮点类型是比较普遍的用法。其中的一个原因是，在真实的世界中很少会要求精确到普通浮点数能提供的17位精度。第二个原因是，原生的浮点数计算要快的多。

误差的产生：
```python {cmd}
a = 1.23e+18
b = 1
c = -1.23e+18
# 1 被“丢失了”
print(a+b+c)
# 使用 math.fsum 进行精确运算
from math import fsum
print(fsum([a,b,c]))
```
### 数字的格式化输出

```python {cmd}
x = 1234.56789
print(f"'{x:.2f}'")
print(f"'{x:>10.1f}'")
print(f"'{x:<10.1f}'")
print(f"'{x:^10.1f}'")
print(f"'{x:,}'")
print(f"'{x:e}'")
print(f"'{x:.3E}'")
# 同时制定宽度和精度的一般形式是：[<>^]?width[,]?(.digits)?
print(f"'{x:,.2f}'")
#上述方法同样使用与Decimal
from decimal import Decimal
x = Decimal('1234.56789')
print(f'{x:*^15,.3f}')
```

### 二八十六进制表示整数
```python {cmd}
x = 1234
print(bin(x))
print(f'{x:b}')

print(oct(x))
print(f'{x:o}')

print(hex(x))
print(f'{x:x}')

# 整数是有符号的
x = -x
print(bin(x))
print(f'{x:b}')
# 显示负数对应的无符号二进制表示
# 32 位对应的无符号表示
print(f'{2**32+x:b}')
print(f'{2**32+x:x}')

# 将不同进制字符串转换为整数
print(int('4d2', 16))
print(int('10011010010', 2))
```

### 字节到大整数的打包与解包
```python {cmd}
data = b'\x00\x124V\x00x\x90\xab\x00\xcd\xef\x01\x00#\x004'
print(len(data))
print(f"{int.from_bytes(data, 'little'):x}")
print(f"{int.from_bytes(data, 'big'):x}")
print(int.from_bytes(data, 'big'))

x = 94522842520747284487117727783387188
print(x.to_bytes(16, 'big'))
```
大整数和字节字符串之间的转换操作并不常见，然而，在一些应用领域，比如密码学或网络，有时候也会出现。

### 复数

```python {cmd}
a = complex(2,4)
b = 2+4j
print(a)
print(a==b)
print(a.real, a.imag)
# 共轭复数
print(a.conjugate())
```
常见的运算可以工作：
```python {cmd}
a = 2+4j
b = 1-3j
print(a+b)
print(a-b)
print(a*b)
print(a/b)
```
Python 中大部分与数字相关的模块都能处理复数，比如如果使用numpy，可以很容易构造一个复数数组并在这个数组上执行各种操作：
```python {cmd}
import numpy as np
a = np.array([2+3j,4+5j,6-7j,8+9j])
print(a)
print(a+2)
print(np.sin(a))
```
Python的标准数学函数模块 math 不能产生复数值，因此你的代码不会出现复数返回值：
```python {cmd}
import math
print(math.sqrt(-1))
```
但是可以使用 cmath 模块：
```python {cmd}
import cmath
print(cmath.sqrt(-1))
# 或者是 ** 运算符
print((-1)**.5)
```

### 无穷大与NaN

Python并没有特殊的语法来表示正无穷、负无穷、NaN(非数字)的浮点数，但是可以使用`float()`来创建它们：
```python {cmd}
import math
a = float('inf')
b = float('-inf')
c = float('nan')
print(a) # inf
print(math.isinf(a)) # True
print(b) # -inf
print(math.isinf(b)) # True
print(c) # nan
print(math.isnan(c)) # True
# 无穷大的运算
print(a+100) # inf
print(a*100) # inf
print(10000/a) # 0.0
print(a/a) # nan
print(a+b) # nan
# NaN会在所有操作中传播
print(c + 20) # nan
print(c / 20) # nan
# 两个NaN值不相等
print(c==float('nan')) # False
```

### 分数运算

`fractions` 模块可以被用来执行包含分数的数学运算，比如：
```python {cmd}
from fractions import Fraction
a = Fraction(5, 4)
b = Fraction(7, 16)
print(a+b) # 27/16
print(a*b) # 35/64

# 获取 numerator / denominator
c = a * b
print(c.numerator) # 35
print(c.denominator) # 64

# 转换为小数
print(float(c)) # 0.546875

# 限制分母大小
print(c.limit_denominator(8)) # 4/7

# 小数转换为分数
x = 3.75
print(Fraction(*x.as_integer_ratio()))
```
在大多数程序中一般不会出现分数的计算问题，但是有时候还是需要用到的。比如在一个允许接收分数形式的测试单位以分数形式执行运算的程序中，直接使用分数可以减小手动转换为小数或浮点数的工作。

### 大型数组运算

涉及到数组的重量级运算操作，可以使用NumPy库。NumPy的一个主要特征是它会给Python提供一个数组对象，相比于标准的Python列表而言，更适合用来做数学运算。

传统的 list 操作：
```python {cmd id="20180408214923"}
# Python list
x = [1, 2, 3, 4]
y = [5, 6, 7, 8]
```
```python {cmd continue="20180408214923"}
print(x * 2)
```
```python {cmd continue="20180408214923"}
print(x+10)
```
```python {cmd continue="20180408214923"}
print(x+y)
```

Numpy 库的操作：
```python {cmd id="20180408215711"}
import numpy as np
ax = np.array([1,2,3,4])
ay = np.array([5,6,7,8])
```
```python {cmd continue="20180408215711"}
print(ax*2)
```
```python {cmd continue="20180408215711"}
print(ax+10)
```
```python {cmd continue="20180408215711"}
print(ax+ay)
```
```python {cmd continue="20180408215711"}
print(ax*ay)
```

如上所示，两种方案中数组的基本数学运算结果并不相同。特别地，NumPy中的标量运算（比如 ax*2 或 ax+10）会作用在每一个元素上，另外当两个操作都是数组的时候执行元素对等位置计算，并最终生成一个新的数组。

对这个数组中所有元素同时执行数学运算可以使得作用在数组上的函数简单而快速。比如，你想计算多项式的值：
```python {cmd continue="20180408215711"}
def f(x):
    return 3*x**2 - 2*x + 7
print(f(ax))
```

NumPy还为数组操作提供了大量的通用函数，这些函数可以作为math模块中的替代，比如：
```python {cmd continue="20180408215711"}
print(np.sqrt(ax))
print(np.cos(ax))
```
使用这些通用函数要比循环数组并使用 math 模块中的函数执行计算要快得多。因此只要有可能，尽量选择 NumPy 的数组方案。

底层实现中，NumPy 数组使用了 C 或者 Fortran 语言的机制分配内存。也就是说，它们是一个非常大的连续的并且有相同类型数据组成的内存区域。所以，你可以构造一个比普通 Python 列表大得多的数组，比如，你构建一个10000*10000的浮点数二维数组：
```python {cmd id="20180408221348"}
import numpy as np
grid = np.zeros(shape=(10000, 10000), dtype=float)
```
```python {cmd continue="20180408221348"}
print(grid)
```
所有普通操作都会作用在所有元素上：
```python {cmd continue="20180408221348" id="20180408221653"}
grid += 10
```
```python {cmd continue="20180408221653"}
print(grid)
```
```python {cmd continue="20180408221653"}
print(np.sin(grid))
```

关于 NumPy ，有一点需要特别地注意，那就是他扩展了Python列表的索引功能：
```python {cmd}
import numpy as np
a = np.array([[1,2,3,4],[5,6,7,8],[9,10,11,12]])
print(a, end='\n'*2)
print(a[1], end='\n'*2)
print(a[:, 1], end='\n'*2)
a[1:,1:3] += 10
print(a, end='\n'*2)
print(a+[100, 101, 102, 103], end='\n'*2)
print(a+[[100],[101],[102]], end='\n'*2)
print(np.where(a<10, a, 10))
```
NumPy 是 Python 领域中很多科学与工程库的基础，同时也是被广泛使用的最大最复杂的模块。

### 矩阵与线性代数运算

你需要执行矩阵和线性代数运算，比如矩阵乘法、寻找行列式、求解线性方程组等等。

NumPy 库有一个矩阵对象可以用来解决这个问题。矩阵类似于3.9小节中数组对象，但是遵循线性代数的计算规则：
```python {cmd}
import numpy as np
m = np.matrix([[1, -2, 3], [0, 4, 5], [7, 8, -9]])
print(m, end='\n'*2)
print(m.T, end='\n'*2)
# 返回矩阵的逆
print(m.I, end='\n'*2)
# m·m.I = identity matrix
print(m*m.I, end='\n'*2)

v = np.matrix([[2],[3],[4]])
print(v, end='\n'*2)

print(m*v)
```

可以在 `numpy.linalg`子包中找到更多的操作函数：
```python {cmd}
import numpy as np
# determinant 矩阵行列式的值
m = np.matrix([[1, -2, 3], [0, 4, 5], [7, 8, -9]])
v = np.matrix([[2],[3],[4]])
print(np.linalg.det(m), end='\n'*2)
# eigenvalues 特征值
print(np.linalg.eigvals(m), end='\n'*2)
# 解方程 mx=v
x = np.linalg.solve(m, v)
print(x, end='\n'*2)
print(m*x, end='\n'*2)
```

### 随机选择

从一个序列中随机抽取若干个元素，或者想生成几个随机数：
```python {cmd}
import random
values = [1,2,3,4,5,6]
# 随机选出 list 中的一个元素
print(random.choice(values))
print(random.choice(values))
# 随机选出 list 中的若干个元素
print(random.sample(values, 3))
# 打乱序列中元素的顺序
random.shuffle(values)
print(values)
# 随机生成整数，范围为 [0, 100] 的闭区间
print(random.randint(0,100))
# 随机生成 0~1 范围内的浮点数
print(random.random())
# 以等概率均匀分布生成 0~1 的随机数
print(random.uniform(0, 1))
# 以均值为0，标准差为1，生成高斯分布
print(random.gauss(0, 1))
# 获取 N 为随机位（二进制）的整数
print(f'{random.getrandbits(200):x}')
```

random 模块使用 Mersenne Twister 算法来计算生成随机数，这是一个确定性算法，但你可以通过 random.seed() 函数修改初始化种子。

### 基本日期与时间转换

与日期、时间相关的模块为 `datetime`。其子模块 `timedelta` 用于表示时间间隔：
```python {cmd}
# timedelta 表示一段时间间隔
from datetime import timedelta
a = timedelta(days=2, hours=14)
b = timedelta(hours=17.5)
c = a+b
print(c.days)
print(c.seconds)
print(c.seconds/3600)
print(c.total_seconds() / 3600)
```

如果你想表示指定的日期和时间，先创建一个 datetime 实例，然后使用标准的数学运算来操作它们：
```python {cmd}
from datetime import datetime
from datetime import timedelta
a = datetime(2018, 4, 9, 10, 49, 55)
# datetime 和 timedelta 的运算
print(a+timedelta(hours=10, minutes=5))
b = datetime(2012, 12,21)
# datetime 之间的运算
print(a-b)
# 获取当前时间
now = datetime.now()
print(now)
```

注意：datetime 会自动处理闰年：
```python {cmd}
from datetime import datetime
a = datetime(2012,3,1)
b = datetime(2012,2,28)
print(a-b)
a = datetime(2100,3,1)
b = datetime(2100,2,28)
print(a-b)
```

对于大多数基本的日期和时间处理问题，`datetime` 模块已经足够了，如果你需要执行更加复杂的日期操作，如处理时区、节假日计算等，可以考虑使用 `dateutil` 模块。

### 计算最后一个周五的日期

```python {cmd}
from datetime import datetime, timedelta
weekdays = ['Monday', 'Tuesday', 'Wednesday',
    'Thursday', 'Friday', 'Saturday', 'Sunday']
def get_previous_byday(dayname, start_date=None):
    if start_date==None:
        start_date = datetime.today()
    day_delta = (7+start_date.weekday() -
        weekdays.index(dayname))%7
    day_delta = day_delta if day_delta else 7
    return start_date-timedelta(days=day_delta)
print(get_previous_byday('Sunday'))

# 2016年最后一个星期五
print(get_previous_byday('Friday', datetime(2017, 1, 1)))
```
查看真实情况：
```sh {cmd="/bin/zsh"}
cal 12 2016
```
可见2016年最后一个周五真的是：2016-12-30。

如果要执行大量的日期计算，最好使用模块 `dateutil`。
```python {cmd}
from datetime import datetime
from dateutil.relativedelta import relativedelta
from dateutil.rrule import *
d = datetime(2017, 1, 1)
print(d+relativedelta(weekday=FR(-1)))
# 修改day属性
print(d.replace(day=10))
```

calendar 用于与日历相关的信息处理：
```python {cmd}
import calendar
# 返回 2017-2-1 星期几，共多少天
print(calendar.monthrange(2017, 2)) # 2表示周三
# 返回 2016-2-1 星期几，共多少天
print(calendar.monthrange(2016, 2)) # 0表示周一
```

### 字符串转换为时间
```python {cmd}
from datetime import datetime
text = '2018-4-9'
y = datetime.strptime(text, '%Y-%m-%d')
print(y)
print(datetime.strftime(y, '%A %B %d, %Y'))
```
注意：`strptime`的性能非常差，如果知道解析格式，最好自己写：
```python {cmd}
from datetime import datetime
def strptime(s):
    year_s, month_s, day_s = s.split('-')
    return datetime(int(year_s), int(month_s),int(day_s))
print(strptime('2018-4-9'))
```
经过测试，自定义的函数性能比`datetime.strptime`快7倍，尽量使用自定义的时间解析。

## 函数

使用 `def` 语句定义函数是所有程序的基础。本章的目的是讲解一些更加高级和不常见的函数定义和使用模式。涉及到的内容包括默认参数、任意数量参数、强制关键字参数、注解和闭包。另外一些高级的控制流和利用回调函数传递数据的技术也会在这里讲解到。

### 可接受任意数量参数的函数

```python {cmd}
# 计算平均值，参数数量不确定，至少为 1 个
def avg(first, *rest):
    return (first + sum(rest))/(1+len(rest))
print(avg(1,2,3,4))
print(avg(5))
```
在这个例子中， `rest` 形参是所有其他位置参数组成的元组。然后我们在代码中把它当成了一个序列来进行后续的计算。

为了接受任意数量的关键字参数，使用一个以 ** 开头的参数:
```python
import html
def make_element(name, value, **attrs):
    keyvalues = [f' {k}="{v}"' for k,v in attrs.items()]
    return (f'<{name}{"".join(keyvalues)}>'
     f'{html.escape(value)}</{name}>')
print(make_element('item', 'Albatross', 
    size='large', quantity=6))
print(make_element('p', '<spam>'))
```
```
<item size="large" quantity="6">Albatross</item>
<p>&lt;spam&gt;</p>
```

如果同时希望接收任意数量的位置参数和关键字参数，可以同时使用 \* 和 \*\* 。

```python {cmd}
def anyargs(*args, **kwargs):
    print(args) # a tuple
    print(kwargs) # a dict
```
**注意：** 一个 \* 参数只能出现在函数定义中最后一个位置参数后面，而 \*\* 参数只能出现在最后一个参数，有一点需要注意的是，在 * 参数后面仍然可以定义其他参数。

```python {cmd}
def a(x, *args, y):
    pass
def b(x, *args, y, **kwargs):
    pass
```
这种参数就是我们所说的强制关键字参数。

### 只接受关键字参数的函数

如果你希望函数的某些参数强制使用关键字参数传递，只需要将强制关键字参数放在某个 \* 参数或者单个 \* 后面就能达到这种效果，比如：
```python {cmd id="20180411092137"}
def recv(maxsize, *, block):
    'Receives a message'
    pass
def test(func, *args, **kwargs):
    try:
        func(*args, **kwargs)
    except TypeError:
        print('Catch TypeError')
    else:
        print('No Error')
```
```python {cmd continue="20180411092137"}
test(recv, 1024, True) # recv(1024, True)
test(recv, 1024, block=True) # recv(1024, block=True)
```
利用这种技术，我们还能在接受任意多个位置参数的函数中指定关键字参数，比如:
```python {cmd}
def minimum(*values, clip=None):
    m = min(values)
    if clip != None:
        m = clip if clip>m else m
    return m
print(minimum(1,5,2,-5,10))
print(minimum(1,5,2,-5,10,clip=0))
```
很多情况下，使用强制关键字参数会比使用位置参数表意更加清晰，程序也更加具有可读性：
```python
msg = recv(1024, False)
```
如果调用者对`recv`函数不是很熟悉，那他肯定不明白那个False参数到底用来干嘛，但是，如果代码编程下面的样子的话就清楚多了：
```python
msg = recv(1024, block=False)
```
另外，使用强制关键字参数也比使用 **kwargs 参数更好，因为在使用函数 help 时的输出也更加容易理解：
```python {cmd continue="20180411092137"}
help(recv)
```

### 给函数参数增加元信息

你写好了一个函数，然后想为这个函数的参数增加一些额外的信息，这样的话，其他使用者就能清楚地知道这个函数应该怎么用，使用函数参数注解是一个很好的办法，它能够提示程序员应该怎样使用这个函数，如下面是一个被注解了的函数：
```python {cmd}
def add(x:int, y:int) -> int:
    return x+y
help(add)
```
Python解释器不会对这些注释添加任何的语义。它们不会被类型检查，运行时跟没有加注解之前的效果也没有任何差距。然而，对于那些阅读源代码的人来讲就非常有帮助。第三方工具和框架可能会对这些注释添加语义，同时它们也会出现在文档中。

```python
def add(x:int, y:int) -> int:
    return x+y
print(add.__annotations__)
```
```
{'x': <class 'int'>, 'y': <class 'int'>, 'return': <class 'int'>}
```

### 返回多个值的函数

你希望构造一个可以返回多个值的函数。

为了能返回多个值，函数直接 return 一个元组就可以了，例如：
```python {cmd}
def myfun():
    return 1, 2, 3
a, b, c = myfun()
print(a, b, c,)
d = myfun()
print(d)
```
尽管 `myfun()` 看上去返回了多个值，其实是先创建了一个元组，然后返回的。
```python {cmd}
def myfun():
    return (1,2,3)
a, b, c = myfun()
print(a, b, c)
d = myfun()
print(d)
```
这个语法看上去比较奇怪，实际上我们使用的是逗号生成一个元组，而不是括号：
```python {cmd}
a = (1)
print(a)
b = 1,
print(b)
```

### 定义有默认参数的函数

定义一个函数或方法，它的一个或多个参数使可选的，并且有一个默认值：
```python {cmd}
def spam(a, b=24):
    print(a, b)
spam(1)
spam(1,2)
```

如果默认参数是一个可修改的容器，比如列表、集合或字典，可以使用 None 作为默认值
```python {cmd}
def spam(a, b:list=None) -> list:
    b = b if b is not None else []
    return []+b
help(spam)
```

如果你不想提供一个默认值，而是想仅仅测试一下某个默认参数是不是有参数传递进来，可以像下面这样写：
```python {cmd}
_no_value = object()
def spam(a, b=_no_value):
    if b is _no_value:
        print('No b value supplied')
    else:
        print('b value has supplied')
spam(1)
spam(1, 2)
spam(1, None)
```
可以看出，传递一个 None 值和不传值是两种不同的情况。

默认参数的值仅仅在函数定义的时候赋值一次：
```python {cmd}
x = 42
def spam(a, b=x):
    print(a, b)
spam(10)
x = 43
spam(10)
```
注意，当我们改变 `x` 的值时，对默认参数值并没有影响，这是因为在函数定义时就已经确定了它的默认值。

其次，默认参数的值应该是不可变的对象，比如 None、True、False、数字或字符串，特别不要写类似：`def spam(a,b=[])`的代码。如果这样做了，当默认值在其他地方被修改后，你将会遇到各种麻烦以及安全问题：
```python {cmd}
def spam(a, b=[]):
    print(b)
    return b
x = spam(1)
x.append(99)
spam(1)
```

这种结果不是你想要的，为了避免这种情况的发生，最好是将默认值设为 None，然后在函数里面检查它：
```python {cmd}
def spam(a, b=None):
    b = [] if b is None else b
    print(b)
    return b
x = spam(1)
x.append(99)
spam(1)
```

在测试 None 值时，使用 is 操作符很重要，也是这种方案的关键点，有时候大家会犯下面的错误：
```python {cmd}
def spam(a, b=None):
    if not b: # NO! use 'b is None' instead
        print('b is None :', b)
        b = []
    else:
        print('b is not None')
x = []
spam(1)
spam(1, x)
spam(1, 0)
spam(1, '')
```
应该修改为：
```python {cmd}
def spam(a, b=None):
    if b is None: # NO! use 'b is None' instead
        print('b is None :', b)
        b = []
    else:
        print('b is not None')
x = []
spam(1)
spam(1, x)
spam(1, 0)
spam(1, '')
```
或者是：
```python {cmd}
def spam(a, b=None):
    if b==None: # NO! use 'b is None' instead
        print('b is None :', b)
        b = []
    else:
        print('b is not None')
x = []
spam(1)
spam(1, x)
spam(1, 0)
spam(1, '')
```

最后一个问题是一个函数需要测试某个可选参数是否被使用者传递进来。这时候需要小心的是，你不能用某个默认值比如 None、0 或者 False 值来测试用户提供的值（因为这些值都是合法的，是可能被用户传递进来的）。

为了解决这个问题，你可以创建一个独一无二的私有对象实例，就像之前的 `_no_value` 变量那样。在函数里面，你可以通过检查被传递参数值跟这个实例是否一样来判断。这里的思路是用户不可能去传递这个 _no_value 实例作为输入，因此这里通过检查这个值就能确定某个参数是否被传递进来了。

### 定义匿名或内联函数

你想为 `sort()` 操作创建一个很短的回调函数，但有不想用 def 去写一个单行函数，而是希望通过某个快捷方式以内联方式来创建这个函数：
```python {cmd}
add = lambda x,y: x+y
print(add(3,5))
print(add('hello', 'world'))
```

lambda 表达式典型的使用场景是排序或数据 reduce 等：
```python {cmd}
names = ['David Beazley', 'Brain Jones',
    'Raymond Hettinger', 'Ned Batchelder']
print(sorted(names, key=lambda nm: nm.split()[-1]))
```
尽管 lambda 表达式也许你定义简单函数，但是它的使用是有限制的。你只能指定单个表达式，它的值就是最后的返回值。也就是说不能包含其他的语言特性了，包括多个语句、条件表达式、迭代以及异常处理等等。

你可以不使用 lambda 表达式就能编写大部分python代码。但是，当有人编写大量计算表达式值得短小函数或者需要用户提供回调函数的程序时候，你就会看到 lambda 表达式的身影了。

### 匿名函数捕获变量值

你可以用 lambda 定义一个匿名函数，并且在定义时捕获到某些变量的值：
```python {cmd}
x = 10
a = lambda y: x+y
def A(y):
    return x+y
x = 20
b = lambda y: x+y
def B(y):
    return x+y
print(a(10))
print(A(10))
print(b(10))
print(B(10))
```
在lambda表达式中，x是一个自由变量，在运行时绑定值，而不是定义时就绑定，**这跟函数的默认值参数定义时不同的**。因此，在调用这个lambda表达式的时候，x的值是执行时的值。

如果你想让某个匿名函数在定义时就捕获到值，可以将那个参数值定义成默认参数即可：
```python {cmd}
x = 10
a = lambda y,x=x:x+y
x = 20
b = lambda y,x=x:x+y
print(a(10))
print(b(10))
```

这里列出来的问题是新手很容易犯的错误，有些新手可能会不恰当地使用 lambda 表达式，比如通过在一个循环或列表推导中创建一个 lambda 表达式列表，并期望函数在定义时就记住每次的迭代值，例如：
```python {cmd}
# Wrong code
funcs = [lambda x:x+n for n in range(5)]
for f in funcs:
    print(f(0))
print()
# Right code
funcs = [lambda x,n=n:x+n for n in range(5)]
for f in funcs:
    print(f(0))
```
**通过使用函数默认值形式，lambda函数在定义时就能绑定到值。**

### 减少可到用对象的参数个数

问题：你有一个其他 python 代码使用的 callable 对象，可能是一个回调函数或者是一个处理器，但是它的参数太多了，导致调用时出错。

解决方案：如果要减少某个函数的参数个数，你可以使用 `functools.partial()`。`partial()`函数允许你给一个或多个参数设置固定的值，减少接下来被调用时的参数个数。
```python {cmd}
def spam(a, b, c, d):
    print(a, b, c, d)

# 使用 functools.partial 函数来固定某些参数值
from functools import partial
s1 = partial(spam, 1) # a=1
s1(2,3,4)
s2 = partial(spam, d=42)
s2(1,c=2,b=3)
s3 = partial(spam, 1, 2, d=35)
s3(3)
```
可以看出 `partial()` 固定某些参数，并返回一个新的 callable 对象。这个新的 callable 接受未赋值的参数，然后跟之前赋值过的参数合并起来，最后所有参数传递给原始函数，其内部的工作机制为：
```python {cmd}
def mypartial(f, *argv, **kwargv):
    return lambda *v,**kwv:f(*argv,*v,**kwv, **kwargv)

def spam(a, b, c, d):
    print(a, b, c, d)

s1 = mypartial(spam, 1) # a=1
s1(2,3,4)
s2 = mypartial(spam, d=42)
s2(1,c=2,b=3)
s3 = mypartial(spam, 1, 2, d=35)
s3(3)
```
本节要解决的问题是让原版不兼容的代码可以一起工作。
例子1,假设你有一个点的列表来表示 (x, y)坐标元组。你可以用一下的函数来计算两点之间的距离：
```python {cmd id="20180411135623"}
points = [(1,2),(3,4),(5,6),(7,8)]
import math
def distance(p1, p2):
    x1, y1 = p1
    x2, y2 = p2
    return math.hypot(x1-x2, y1-y2)
```
现在假设你想以某个点为基点，根据点和基点之间的距离来排序所有的这些点，列表的 `sort` 接受一个关键字参数来自定义排序逻辑，但是它只能接受一个单个参数的函数(distance()很显然不符合该条件)。现在我们可以通过使用 `partial()`来解决这个问题：
```python {cmd continue="20180411135623"}
pt = (4,3)
from functools import partial
points.sort(key=partial(distance, pt))
print(points)
pt = (6,5)
points.sort(key=lambda p:distance(p, pt))
print(points)
```

更进一步，`partial()` 通常被用来微调其他库函数所使用的回调函数的参数。

很多时候，`partial()` 能实现的效果，lambda 表达式也能实现,不过相比而言会显得比较臃肿，对于代码阅读的人来说更加难懂。这时候使用 `partial()` 可以更加直观地表达你的意图（给某些参数预先赋值）。

### 将单方法的类转换为函数

问题：你有一个除 `__init__()` 方法外只定义了一个方法的类，为了简化代码，你想将它转换成一个函数。

解决方案：大多数情况下，可以使用闭包将单个方法的类转换成函数，例如下面示例的类用于计数：
```python {cmd}
class MyCounter:
    def __init__(self, init=0):
        self.count = init
    def tick(self):
        self.count += 1
        return self.count
ct1 = MyCounter()
print(ct1.tick())
print(ct1.tick())
ct2 = MyCounter(100)
print(ct1.tick())
print(ct2.tick())
print(ct2.tick())
```
这个类可以被一个更简单的函数来替代：
```python {cmd}
def get_counter(init=0):
    closure_var = {'count':init}
    def counter():
        closure_var['count'] += 1
        return closure_var['count']
    return counter
ct1 = get_counter()
print(ct1())
print(ct1())
ct2 = get_counter(100)
print(ct1())
print(ct2())
print(ct2())
```
大部分情况下，你拥有一个单方法的原因是需要存储某些额外的状态来给方法使用。

使用一个内部函数或闭包的方法通常会更加优雅一些。简单来说，一个闭包就是一个函数，只不过在函数内部带上了一个额外的变量环境。闭包关键特点就是会记住自己被定义时的环境。因此，在我们解决方案中，`opener()`函数记住了 template 参数的值，并在接下来的调用中使用它。

任何时候，只要你碰到需要给某个函数增加额外的状态信息的问题，都可以考虑使用闭包。相比将你的函数转换成一个类而言，闭包通常是一种更加简洁和优雅的方案。

### 带额外信息的回调函数

问题：你的代码中依赖到回调函数的使用（比如时间处理器、等待后台任务完成后的回调等），并且你还需要然回调函数拥有额外的状态值，以便在它的内部使用到。

解决方案：这一小节主要讨论的是那些出现在很多函数库和框架中的回调函数的使用——特别是跟异步处理有关的。为了演示和测试，我们先定义如下一个需要调用回调函数的函数：
```python {cmd id="20180411155159"}
def apply_async(func, args, *, callback):
    # compute the result
    result = func(*args)
    # invoke the callback with the result
    callback(result)
def add(x, y):
    return x+y
```
实际上，这段代码可以做如何更高级的处理，包括线程、进程和定时器，但是这些都不是我们所要关心的。我们仅仅需要关注回调函数的调用。下面是一个演示如何使用上述代码的例子：
```python {cmd continue="20180411155159"}
def print_result(result):
    print('Got :', result)

apply_async(add, (2,5), callback=print_result)
apply_async(add, ('hello ', 'world'), callback=print_result)
```
注意到 `print_result()` 函数仅仅接受一个参数 result。不能再传入其他信息，而当你想让回调函数访问其他变量或特定环境的变量值的时候就会遇到麻烦。

为了让回调函数访问外部信息，一种方法是使用一个绑定方法来替代一个简单函数。比如，下面这个类会保存一个内部序列号，每次接收到一个 result 的时候，序列号加 1：
```python {cmd continue="20180411155159"}
class ResultHandler:
    def __init__(self):
        self.sequence = 0
    def handler(self, result):
        self.sequence += 1
        print(f'[{self.sequence}] Got : {result}')

r1 = ResultHandler()
apply_async(add, (2,3), callback=r1.handler)
apply_async(add, ('hello ','world'), callback=r1.handler)
r2 = ResultHandler()
apply_async(add, (10,20), callback=r2.handler)
```
第二种方式，作为类的替代，可以使用一个闭包捕获状态值：
```python {cmd continue="20180411155159"}
def get_handler():
    sequence = 0
    def handler(result):
        nonlocal sequence
        sequence += 1
        print(f'[{sequence}] Got : {result}')
    return handler
h1 = get_handler()
apply_async(add, (2,3), callback=h1)
apply_async(add, ('hello ','world'), callback=h1)
h2 = get_handler()
apply_async(add, (10,20), callback=h2)
```

还有另外一种更加高级的方法，可以使用协程来完成同样的事情：
```python {cmd continue="20180411155159"}
def make_handler():
    sequence = 0
    while True:
        result = yield
        sequence += 1
        print(f'[{sequence}] Got : {result}')
handler = make_handler()
next(handler) # Advance to the yield
apply_async(add, (2,3), callback=handler.send)
apply_async(add, ('hello ','world'), callback=handler.send)
```

基于回调函数的软件通常都有可能变得非常复杂。一部分原因是回调函数通常会跟请求执行代码断开，因此，请求代码和处理结果之间的执行环境实际上已经丢失了。如果你想让回调函数连续执行多步操作，那你必须去解决如何保存和恢复相关的状态信息了。

至少有两种主要的方式来捕获和保存信息，你可以在一个对象实例（通过一个绑定方法）或者在一个闭包中保存它。两种方式比起来，闭包或许更加轻量级和自然一点，因为它们可以很简单地通过函数来构造。它们还能自动捕获所有被使用到的变量，因此，你无需去担心如何去存储额外的状态信息。

如果使用闭包，你需要注意那些可修改变量的操作。在上面的方案中，`nonlocal`声明语句用来指示接下来的变量会在回调函数中被修改。如果没有这个声明，代码会报错。


而使用一个协程来作为一个回调函数就更加有趣了。它跟闭包方法密切相关，某种意义上来讲，它显得更加简洁，因为总共就一个函数而已。并且，你可以很自由地修改变量而无需去使用 nonlocal 声明。这种方式的唯一缺点是相对于其他 Python 技术而言，或许比较难以理解。另外一个比较难懂的部分：比如使用之前需要调用 `next()`，实际上这个步骤很容易被忘记。尽管如此，协程还有其他方面的用处，比如作为一个内联回调函数的定义。

### 内联回调函数 [unfinished]
问题：当你编写使用回调函数的代码时，担心很多小函数的扩张可能会弄乱程序控制流，你希望找到某个方法来让代码看上去更像是一个普通的执行序列；

解决方案：通过生成器和协程可以使得回调函数内联在某个函数中，为了演示说明，假设你有如下所示的一个执行某种计算任务然后调用一个回调函数的函数：
```python {cmd id="20180411163837"}
def apply_async(func, arg, *, callback):
    result = func(*arg)
    callback(result)
```
接下来让我们看一下下面的代码，它包含了一个 `Async` 类和一个 `inlined_async` 装饰器：
```python {cmd continue="20180411163837"}
from queue import Queue
from functools import wraps

class Async:
    def __init__(self, func, args):
        self.func = func
        self.args = args

def inlined_async(func):
    @wraps(func)
```

### 访问闭包中定义的变量

问题：你想要扩展函数中的某个闭包，允许它能访问和修改函数的内部变量。

解决方案：通常来讲，闭包的内部变量对于外界来讲是完全隐藏的。但是，你可以通过编写访问函数，并将其作为函数属性绑定到闭包上来实现这个目的：
```python {cmd}
def sample():
    n = 0
    # closure function
    def func():
        print('n =', n)
    # accessor method for n
    def get_n():
        return n
    def set_n(value):
        nonlocal n
        n = value
    # attach as function attributes
    func.get_n = get_n
    func.set_n = set_n
    return func

# usage
f = sample()
f()
f.set_n(10)
print(f.get_n())
f()
```
为了说明他如何工作，有两点需要解释一下。首先，`nonlocal`声明可以让我们编写函数来修改内部变量的值。其次，函数属性允许我们使用一种很简单的是将访问方法绑定到闭包函数上，这跟实例方法很像，尽管没有定义任何的类。

## 类与对象

本章主要关注点是和类定义有关的常见编程模型，包括让对象支持常见的 Python 特性、特殊方法的使用、类封装技术、继承、内存管理以及有用的设计模式。

### 改变对象的字符串显示

问题：你想改变对象实例的打印或显示输出，让它们更具有可读性。

解决方案：要改变一个实例的字符串表示，可重新定义它的 `__str__()` 和 `__repr__()` 方法。例如：
```python {cmd id="20180411211358"}
class Pair:
    def __init__(self, x, y):
        self.x = x
        self.y = y
    def __repr__(self):
        return f'Pair({self.x!r}, {self.y!r})'
    def __str__(self):
        return f'({self.x!s}, {self.y!s})'
```
```python
p = Pair(3, 4)
>>> p # 输出 __repr__() 的返回
>>> print(p) # 输出 __str__() 的返回
p = Pair('apple', 'banana')
>>> p
>>> print(p)
```
```
Pair(3, 4)
(3, 4)
Pair('apple', 'banana')
(apple, banana)
```

我们在这里还演示了在格式化的时候怎样使用不同的字符串表现形式。特别来讲，!r 格式化代码指明输出使用 `__repr__()` 来替代默认的 `__str__()` :

```python {cmd continue="20180411211358"}
p = Pair(3, 4)
print(f'{p!r}')
print(f'{p!s}')
print(f'{p}')

s = 'hello, world'
print(f'{s!r}')
print(f'{s}')
```

自定义 `__repr__()` 和 `__str__()` 通常是很好的习惯，因为它能简化调试和实例的输出。例如，如果仅仅是打印输出或者日志输出某个实例，那么程序员会看到实例更加详细与有用的信息。

`__repr__()` 生成的文本字符串标准做法是需要让 `eval(repr(x))==x` 为真。如果实在做不到，应该创建一个有用的文本字符串，并使用 \< 和 \> 括起来，比如：
```python {cmd}
f = open('test.py')
print(f'{f!r}')
```

如果 `__str__()` 没有被定义，那么就会使用 `__repr__()` 来替代输出。

### 自定义字符串的格式化

问题：你想通过 `format()` 函数和字符串方法使得一个对象能支持自定义的格式化。

解决方案：为了自定义字符串的格式化，我们需要在类上面定义 `__format__()` 方法。例如：
```python {cmd}
_formats = {
    'ymd' : '{d.year}-{d.month}-{d.day}',
    'mdy' : '{d.month}/{d.day}/{d.year}',
    'dmy' : '{d.day}/{d.month}/{d.year}'
}

class Date:
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    def __format__(self, code=''):
        if code == '':
            code = 'ymd'
        fmt = _formats[code]
        return fmt.format(d=self)
date = Date(2018, 4, 11)
print(f'{date}')
print(f"Today is {date:mdy}")
print(format(date, 'dmy'))
```

讨论：`__format__()` 方法给 Python 字符串格式化功能提供了一个钩子。这里需要强调的是，格式化代码的解析工作完全由类自己决定。因此，格式化代码可以是任何值。例如参考下面来自 datetime 模块中的代码：
```python {cmd}
from datetime import date
d = date(2018, 4, 11)
print(format(d, '%A, %B %d %Y'))
print(f'Today is {d:%a, %b %d %Y}')
```
对于内置类型的格式化，有一些标准的约定。可以参考 `string` 模块文档说明。

### 让对象支持上下文管理协议

问题：你想让你的对象支持上下文管理协议 （with 语句）。

解决方案：为了让一个对象兼容 with 语句，你需要实现一个 `__enter__()` 和 `__exit__()` 方法。例如，考虑如下的一个类，它能为我们车间一个网络连接：
```python {cmd id="20180411222157"}
from socket import socket, AF_INET, SOCK_STREAM

class LazyConnection:
    def __init__(self,address,family=AF_INET,type=SOCK_STREAM):
        self.address = address
        self.family = family
        self.type = type
        self.sock = None
    
    def __enter__(self):
        if self.sock is not None:
            raise RuntimeError('Already connected')
        self.sock = socket(self.family, self.type)
        self.sock.connect(self.address)
        return self.sock
    
    def __exit__(self, exc_ty, exc_val, tb):
        self.sock.close()
        self.sock = None
```

这个类的关键特点在于它表示了一个网络连接，但是初始化的时候并不会做如何事情（比如它并没有建立一个连接）。连接的建立和关闭是使用 with 语句自动完成的，例如：
```python {cmd continue="20180411222157"}
from functools import partial
conn = LazyConnection(('www.python.org', 80))
with conn as s:
    # conn.__enter__() executes : connection open
    s.send(b'GET /index.html HTTP/1.0\r\n')
    s.send(b'Host: wwww.python.org\r\n')
    s.send(b'\r\n')
    resp = b''.join(iter(partial(s.recv, 8192), b''))
print(resp)
```
讨论：编写上下文管理器的主要原理是你的代码会放到 with 语句中执行。当出现 with 语句的时候，对象的 `__enter__()` 会被触发，它返回的值（如果有的话）被赋值给 as 说明的变量。然后，with 语句块中的代码开始执行。最后，`__exit__()` 方法被触发进行清理工作。

不管 with 代码块中发生什么，上面的控制流都会执行完成，就算代码发生了异常也是一样。事实上，`__exit__()` 方法的三个参数中包含了异常类型、异常值、追溯信息（如果有的话）。`__exit__()` 方法能自己决定这样利用这个异常信息，或者忽略它，并返回一个 None 值。如果 `__exit__()` 返回 True，那么异常会被清空，就好像什么都没发生一样，with 语句后面的程序继续在正常执行。

还有一个细节问题就是 `LazyConnection` 类是否允许多个 with 语句来嵌套使用连接。很显然，上面的定义中值允许一个 socket 的连接，如果正在使用一个 socket 的时候，有重复使用 with 语句，就会产生异常。不过你可以像下面这样修改上面的实现来解决这个问题：
```python {cmd}
from socket import socket, AF_INET, SOCK_STREAM

class LazyConnection:
    def __init__(self, address, family=AF_INET, type=SOCK_STREAM):
        self.address = address
        self.family = family
        self.type = type
        self.connections = []
    def __enter__(self):
        sock = socket(self.family, self.family)
        sock.connect(self.address)
        self.connections.append(sock)
        return sock
    def __exit__(self, exc_tp, exc_val, tb):
        self.connections.pop().close()
    
# example use:
from functools import partial

conn = LazyConnection(('www.python.org', 80))
with conn as s1:
    pass
    with conn as s2:
        pass
        # s1 and s2 are independent sockets
```

在需要管理一些资源，比如文件、网络连接和锁的编程环境中，使用上下文管理器是很普遍的。这些资源的一个主要特点是它们必须被手动地关闭或者释放来确保程序的正常运行。例如，如果你请求了一个锁，那么你必须确保之后释放了它，否则就可能产生死锁。通过实现 `__enter__()` 和 `__exit__()` 方法并使用 with 语句可以很容易避免这些问题。因为 `__exit__()` 方法可以让你无需担心这些了。

### 创建大量对象时节省内存的方法

问题：你的程序要创建大量（可能上百万）的对象，导致占用很大的内存。

解决方案：对于主要是用来当成简单的数据结构的类而言，你可以通过给类添加 `__slots__` 属性来极大地减少实例所占用的内存，比如：
```python {cmd}
class Date:
    __slots__ = ['year', 'month', 'day']
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day
```
当你定义 __slots_ 后，Python就会为实例使用一种更加紧凑的内部表示。实例通过一个很小的固定大小的数组来构建，而不是为每个实例定义一个字典，这跟元组或列表很类似。在 `__slots__` 中列出的属性名在内部被映射到这个数组的指定下标上。使用 `__slots__` 一个不好的地方是我们不能再给实例添加新的属性了，只能使用 `__slots__` 中定义的那些属性名。

讨论：使用 slots 后节省的内存会跟存储属性的数量和类型有关。不过，一般来讲，使用到的内存总量和将数据存储在一个元组中差不多。为了给你一个直观地认识，假设你不适用 slots ，直接存储一个 Date 实例，在 64 位的Python上要占用428字节，而如果使用了slots，内存下降到156字节。如果程序中需要创建大量的日期实例，那么这个就能极大地减小内存使用量了。

尽管 slots 看上去是一个很有用的特性，很多时候你还是得减少对它的使用冲动。Python 的很多特性都依赖于普通的基于字典的实现。另外，定义了 slots 后的类不再支持一些普通类的特性了，比如多继承。大多数情况下，你应该只在那些经常被使用到的 **用作数据结构的类** 上定义slots（比如在程序中需要创建某个类的几百万个实例对象）。

关于 `__slots__` 的一个常见误区是他可以作为一个封装工具来防止用户给实例增加新的属性。尽管使用 slots 可以达到这样的目的，但是这个并不是它的初衷，`__slots__` 更多的是用来作为一个内存优化工具。

### 在类中封装属性名

问题：你想封装类的实例上面的“私有”数据，但是 Python 语言没有访问控制。

解决方案：Python 程序员不去依赖语言特性去封装数据，而是通过遵循一定的属性和方法命名规约来达到这个效果。第一个约定是任何以单下划线 _ 开头的名字都应该是内部实现，比如：
```python {cmd}
class A:
    def __init__(self):
        self._internal = 0 # An internal attribute
        self.public = 1 # An public attribute
    
    def public_method(self):
        '''
        A public method
        '''
        pass
    
    def _internal_method(self):
        pass
```
Python 并不会真的阻止别人访问内部名称，但是如果你这么做肯定是不好的，可能会导致脆弱的代码，同时还要注意，使用下划线开头的约定同样适用于模块名和模块级别函数。例如，如果你看到某个模块名以单下划线开头（比如 `_socket`），那它就是内部实现。类似的，模块级别函数，比如 `sys._getframe()` 在使用的时候就需要加倍小心了。

你还可能会遇到在类定义中使用两个下划线（`__`）开头的命名，比如：
```python
class B:
    def __init__(self):
        self.__private = 0
    
    def __private_method(self):
        pass

    def public_method(self):
        pass
        self.__private_method()
```

使用双下划线开始会导致访问名称变成其他形式。比如，在前面的类 B 中，私有属性会被分别重命名为 `_B__private` 和 `_B__private_method`。这时候，你可能会问，这样重命名的目的是什么，答案就是继承——这种属性通过继承是无法被覆盖的。比如：
```python
class C(B):
    def __init__(self):
        super().__init__()
        self.__private = 1 # Does not override B.__private

    # Does not override B.__private_method()
    def __private_method(self):
        pass
```
这里，私有名称 `__private` 和 `__private_method` 被重命名为 `_C__private` 和 `_C__private_method`，这个跟父类B中的名称完全不同。

**注意：双下划线开头且双下划线结尾并不会被重命名，如 `__private_method__` 并不会导致重命名。**

例如：
```python {cmd}
class A:
    def _private_method(self):
        print('A _private_method')
    def foo(self):
        self._private_method()
class B(A):
    def _private_method(self):
        print('B _private_method')
b = B()
b.foo()
```
可以看出，B类中的`_private_method`的同名方法覆盖了，再来看看 `__private_method`的版本：
```python {cmd}
class A:
    def __private_method(self):
        print('A __private_method')
    def foo(self):
        self.__private_method()
class B(A):
    def __private_method(self):
        print('B __private_method')
b = B()
b.foo()
```
可见，A中的 `__private_method` 并没有被B中的 `__private_method` 所覆盖。

讨论：上面提到了两种不同的编码约定来命名私有属性，那么哪种方式好呢？大多数而言，你应该让你的非公共名称以单下划线开头。但是，如果你清楚你的代码会涉及到子类，并且有些内部属性应该在子类中隐藏起来，那么才考虑使用双下划线方案。
例如
```python {cmd}
class A:
    def _private_method(self):
        print('A _private_method')
class B(A):
    def foo(self):
        self._private_method()
b = B()
b.foo()
```
子类可以继承父类的单下划线开始的方法`_private_method`。

```python {cmd}
class A:
    def __private_method(self):
        print('A _private_method')
class B(A):
    def foo(self):
        self.__private_method()
b = B()
b.foo()
```
子类不能继承父类的双下划线开始的方法`__private_method`。

还有一点要注意的是：有时候你定义的一个变量和某个关键字冲突，这时候可能以使用单下划线作为**后缀**，例如：
```python
lambda_ = 2.0 # Trailing _ to avoid clash with lambda keyword
```
这里我们并不使用单下划线前缀的原因是它避免误解使用初衷。

总结：**双下划线打头的属性或方法：父类方法不能被子类方法覆盖，子类不继承父类方法。**

### 创建可管理的属性

问题：你想给某个实例 attribute 添加出访问与修改之外的其他处理逻辑，比如类型检查或合法性验证。

解决方法：自定义某个属性的一种简单方法是将它定义为一个 property。例如下面的代码定义了一个 property，增加对一个属性简单的类型检查：

```python {cmd id="20180412153218"}
class Person:
    def __init__(self, first_name):
        self.first_name = first_name
    # Getter function
    @property
    def first_name(self):
        return self._first_name

    # Setter function
    @first_name.setter
    def first_name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._first_name = value

    # Deleter function (optional)
    @first_name.deleter
    def first_name(self):
        raise AttributeError("Can't delete attribute")

a = Person('Guido')
```
上述代码中有3个相关的方法，这3个方法的名称必须都一样，第一个方法是一个 getter 函数，它使得 first_name 成为一个属性。其他两个方法给 first_name 属性添加了 setter 和 deleter 函数。需要强调的是，只有在 first_name 属性被创建后，后面的两个装饰器 `@first_name.setter` 和 `@first_name.deleter`才能被定义。

property 的一个关键特征是它看上去和不同的 attribute 没有两样，但是访问它的时候回自动触发 getter、setter、deleter方法：
```python {cmd continue='20180412153218'}
print(a.first_name)
```
```python {cmd continue='20180412153218'}
a.first_name = 42
```
```python {cmd continue='20180412153218'}
del a.first_name
```

在实现一个 property 的时候，底层数据（如果有的话）仍然需要存储在某个地方。因此，在 get 和 set 方法中，你会看到对 `_first_name` 属性的操作，这也是实际数据保存的地方。另外，你可能还会问为什么 `__init__()` 方法中设置了 self.first_name 而不是 self._first_name ? 在这个例子中，我们创建了一个 property 的目的就是在设置 attribute 的时候进行检查。因此，你可能想在初始化的时候也进行这种类型检查，通过设置 `self.first_name` 就会自动调用 `setter` 方法，这个方法里面会进行参数的检查，否则就直接访问 `self._first_name` 了。

还能在已存在 get 和 set 方法基础上定义 property。例如：
```python {cmd id="20180412201509"}
class Person:
    def __init__(self, first_name):
        self.set_first_name(first_name)
    # Getter function
    def get_first_name(self):
        return self._first_name
    # Setter function
    def set_first_name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._first_name = value
    # Deleter function (optional)
    def del_first_name(self):
        raise AttributeError("Can't delete attribute")
    # Make a property from existing get/set mothods
    first_name = property(get_first_name, set_first_name, del_first_name)
```
讨论：一个 property 属性其实就是一系列相关绑定方法的集合。如果你去查看拥有 property 的类，就会发现 property 本身的 fget、fset 和 fdel 属性就是累里面的普通方法，比如：
```python
>>> Person.first_name.fget
>>> Person.first_name.fset
>>> Person.first_name.fdel
```
```
<function __main__.Person.get_first_name>
<function __main__.Person.set_first_name>
<function __main__.Person.del_first_name>
```
通常来讲，你不会直接去调用 fget 或 fset，它们会在访问 property 的时候自动被触发。

只有当你确实需要对 attribute 执行额外的操作的时候才应该使用到 property。有时候一些从其他编程语言（比如 Java）过来的程序员总认为所有访问都应该通过 getter 和 setter，所有他们认为代码应该像下面这样写：
```python
class Person:
    def __init__(self, first_name):
        self._first_name = first_name
    @property
    def first_name(self):
        return self._first_name
    @first_name.setter
    def first_name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._first_name = value
```
**不要写这种没有做任何额外操作的 property。** 首先，它会让你的代码变得很臃肿，而且还会迷惑阅读者。其次，它还会让你的程序运行起来变得慢很多。最后，这样的设计并没有带来任何的好处。特别是当你以后想给普通 attribute 访问添加额外的处理逻辑的时候，你可以将他变成一个 property 而无需改变原来的代码。因为访问 attribute 的代码还是保持原样。

Properties 还是一种定义动态计算 attribute 的方法。这种类型的 attributes 并不会被实际地存储，而是在需要的时候计算出来，比如：
```python {cmd}
import math
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        return math.pi * self.radius ** 2
    
    @property
    def diameter(self):
        return self.radius * 2

    @property
    def perimeter(self):
        return 2 * math.pi * self.radius

c = Circle(4.0)
print(c.area)
print(c.diameter)
print(c.perimeter)
```

尽管 properties 可以实现优雅的编程接口，但有时你还是想直接使用 getter 和 setter 函数，例如：
```python {cmd continue="20180412201509"}
p = Person('Guido')
print(p.get_first_name())
p.set_first_name('Kobe')
print(p.get_first_name())
```
这种情况的出现通常是因为 Python 代码被集成到一个大型基础平台架构或程序中。例如，有可能是一个 Python 类准备加入到一个基于远程过程调用的大型分布式系统中。这种情况下，直接使用 get/set 方法（普通方法调用）而不是 property 或许会更加容易兼容。

最后一点，不要像下面这样写大量重复代码的 property 定义：
```python {cmd}
class Person:
    def __init__(self, first_name, last_name):
        self.first_name = first_name
        self.last_name = last_name
    @property
    def first_name(self):
        return self._first_name
    @property
    def last_name(self):
        return self._last_name
    @first_name.setter
    def first_name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._first_name = value
    @last_name.setter
    def last_name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._last_name = value
p = Person('Jim', 'Green')
```
重复代码会导致臃肿、容易出错和丑陋的程序。好消息是，通过使用装饰器或者闭包，有很多种更好的方法来完成同样的事情。

### 调用父类方法

问题：你想在子类中调用父类的某个已经被覆盖的方法。
解决方案：为了调用父类（超类）的一个方法，可以使用 super() 函数，比如：
```python {cmd}
class A:
    def spam(self):
        print('A spam')

class B(A):
    def spam(self):
        print('B spam')
        super().spam() # call parent spam()

b = B()
b.spam()
```
`super()` 函数的一个常见语法是在 `__init__()` 方法中确保父类被正确地初始化：
```python {cmd}
class A:
    def __init__(self):
        self.x = 0

class B(A):
    def __init__(self):
        super().__init__()
        self.y = 1
b = B()
print(b.x)
```

`super()` 的另一个常见用法出现在覆盖 Python 特殊方法的代码中，比如：
```python {cmd}
class Proxy:
    def __init__(self, obj):
        self._obj = obj
    # Delegate attribute lookup to internal obj
    def __getattr__(self, name)
        return getattr(self._obj, name)
    # Delegate attribute assignment
    def __setattr__(self, name, value):
        if name.startswith('_'):
            super().__setattr__(name, value)
        else:
            setattr(self._obj, name, value)
```

讨论：实际上，大家对于在 Python 中如何正确使用 `super()` 函数普遍知之甚少。你有时候会看到像下面这样直接调用父类的一个方法：
```python {cmd}
class Base:
    def __init__(self):
        print('Base.__init__')

class A(Base):
    def __init__(self):
        Base.__init__(self)
        print('A.__init__')

a = A()
```
尽管对大部分代码而言，这样做没有什么问题，但是在更复杂的涉及到多继承的代码中就有可能导致很奇怪的问题发生。比如，考虑如下的情况：
```python {cmd}
class Base:
    def __init__(self):
        print('Base.__init__')
    
class A(Base):
    def __init__(self):
        Base.__init__(self)
        print('A.__init__')

class B(Base):
    def __init__(self):
        Base.__init__(self)
        print('B.__init__')

class C(A, B):
    def __init__(self):
        A.__init__(self)
        B.__init__(self)
        print('C.__init__')

c = C()
```
你会发现 `Base.__init__()` 中被调用了2次。

可能两次调用 `Base.__init__()` 没有什么坏处，但有时候却不是。另一方面，假设你在代码中使用 `super()`，结果就会非常完美：
```python {cmd id="20180413090245"}
class Base:
    def __init__(self):
        print('Base.__init__')

class A(Base):
    def __init__(self):
        super().__init__()
        print('A.__init__')
    
class B(Base):
    def __init__(self):
        super().__init__()
        print('B.__init__')

class C(A, B):
    def __init__(self):
        super().__init__()
        print('C.__init__')
```
``` python {cmd continue="20180413090245"}
c = C()
```
运行这个新版本后，你会发现每个 `__init__()` 方法 只会被调用一次了。

为了弄清它的原理，我们需要花点时间解释一下 Python 是如何实现继承的。对于你定义的每一个类，Python 会计算出一个所谓的方法解析顺序(Method Resolution Order,MRO)列表。这个 MRO 列表就是一个简单的所有基类的线性顺序表。例如：
```python
>>> C.__mro__
```
```
(__main__.C, __main__.A, __main__.B, __main__.Base, object)
```
为了实现继承，Python 会在 MRO 列表上从左到右开始查找基类，直到找到第一个匹配这个属性的类为止。

而这个 MRO 列表的构建是通过一个 C3 线性化算法来实现的。我们不去深究这个算法的数学原理，它实际上就是合并所有父类的 MRO 列表，并遵循如下3条准则：
1. 子类会先于父类被检查
1. 多个父类会根据他们在列表中的顺序被检查
1. 如果对下一个类存在两个合法的选择，选择第一个父类

老实说，你所要知道的就是 MRO 列表中的类顺序会让你定义的任意类层级关系变得有意义。

但你使用 `super()` 函数时，Python会在 MRO 列表上据需搜索下一个类。只要每个重定义的方法统一使用 `super()` 并只调用它一次，那么控制流最终会遍历完整个 MRO列表，每个方法也会只被调用一次。

`super()` 有个令人吃惊的地方是，它并不一定去查找某个类在 MRO 中下一个直接父类，你甚至可以在一个没有直接父类的类中使用它：
```python {cmd id="20180413095127"}
class A:
    def spam(self):
        print('A.spam')
        super().spam()
```
如果你试着直接使用这个类就会出错：
```python {cmd continue="20180413095127"}
a = A()
a.spam()
```
但是，如果你使用多继承的话：
```python {cmd continue="20180413095127"}
class B:
    def spam(self):
        print("B.spam")

class C(A, B):
    pass

c = C()
c.spam()
```
你可以看到在类 A 中使用 `super().spam()` 实际上调用的是和类A毫无关系的类 B 中的 spam() 方法。

在定义混入类的时候，这样使用 `super()` 是很普遍的。

然而，由于 `super()` 可能会调用不是你想要的方法，你应该遵循一些通用原则。首先，确保在继承体系中所有相同名字的方法拥有可兼容的参数标签（比如：相同的参数个数和参数名称）。这样可以确保 `super()` 调用一个非直接父类方法时不会出错。其次，最好确保最顶层的类提供了这个方法的实现，这样的话在 MRO 上面的查找链肯定可以找到某个确定的方法。

### 子类中扩展 property

问题：在子类中，你想要扩展定义在父类中的 property 的功能。
解决方案：考虑如下的代码，它定义了一个 property:
```python {cmd id="20180413100146"}
class Person:
    def __init__(self, name):
        self.name = name
    
    @property
    def name(self):
        return self._name

    @name.setter
    def name(self, value):
        if not isinstance(value, str):
            raise TypeError('Expected a string')
        self._name = value

    @name.deleter
    def name(self):
        raise AttributeError("Can't delete attribute")
```
下面是一个示例类，它继承自 Person 并扩展了 name 属性的功能：
```python {cmd id="20180413100628" continue="20180413100146"}
class SubPerson(Person):
    @property
    def name(self):
        print('    Getting name')
        return super().name
    
    @name.setter
    def name(self, value):
        print('    Setting name to', value)
        super(SubPerson, SubPerson).name.__set__(self, value)

    @name.deleter
    def name(self):
        print('    Deleting name')
        super(SubPerson, SubPerson).name.__delete__(self)
```
接下来使用这个新类：
```python {cmd continue="20180413100628"}
print('Create SubPerson:')
s = SubPerson('Guido')
print('Get s.name:')
print(s.name)
print('Set s.name:')
s.name = 'Larry'
s.name = 42
```
可以单独扩展 property 中的某一个方法，不过定义方法不同：
```python {cmd continue="20180413100146"}
class SubPerson(Person):
    @property
    def name(self):
        print('Getting name')
        return super().name
s = SubPerson('Guido')
```
setter 函数整个消失了，需要修改为：
```python {cmd continue="20180413100146"}
class SubPerson(Person):
    @Person.name.getter
    def name(self):
        print('Getting name')
        return super().name
s = SubPerson('Guido')
print(s.name)
```

讨论：在子类中扩展一个 property 可能会引起很多不易察觉的问题，因为一个 property 其实是 getter、setter 和 deleter 方法的集合。因此，当你扩展一个 property 的时候，你需要先确定你是否要重新定义所有的方法还是只修改其中某个。

### 创建新的类或实例属性

问题：你想创建一个新的拥有一些额外功能的实例属性类型，比如类型检查。

解决方案：如果你想创建一个全新的实例属性，可以通过一个描述器类的形式来定义它的功能：
```python {cmd id="20180413105013"}
# Descriptor attribute for an integer type-checked attribute
class Integer:
    def __init__(self, name):
        self.name = name
    
    def __get__(self, instance, cls):
        if instance is None:
            return self
        else:
            return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, int):
            raise TypeError('Expected an int')
        instance.__dict__[self.name] = value

    def __delete__(self, instance):
        del instance.__dict__[self.name]
```
一个描述器就是一个实现了 3 个核心的属性访问操作（get、set、delete）的类，分别为`__get__()`、`__set__()` 和 `__delete__()` 这 3 个特殊的方法。这些方法接受一个实例作为输入，之后相应地操作实例底层的字典。

为了使用一个描述器，需要将这个描述器的实例作为类属性放到一个类的定义中。例如：
```python {cmd id="20180413105631" continue="20180413105013"}
class Point:
    x = Integer('x')
    y = Integer('y')
    def __init__(self, x, y):
        self.x = x
        self.y = y
```
当这样之后，所有对描述器属性（比如 x 或 y）的访问，都会被 `__get__()`、`__set__()` 和 `__delete__()` 方法捕获到。例如：
```python {cmd id="20180413110333" continue="20180413105631"}
p = Point(2, 3)
```
调用 `__get__` 函数：
```python {cmd continue="20180413110333"}
print(p.x)
```

调用 `__set__` 函数：
```python {cmd continue="20180413110333"}
p.x = -5
```

如果设置为非整数，就会抛出错误：
```python {cmd continue="20180413110333"}
p.y = 5.0
```
作为输入，描述器的每一个方法都会接受一个操作实例，为了实现请求操作，会相应的操作实例底层的字典(`__dict__`属性)。描述器的 self.name 属性存储了实例字典中被设计使用到的key。

讨论：描述器可实现大部分 Python 类特性中的底层魔法，包括 @classmethod、@staticmethod、@property 甚至是 `__slots__` 特性。

通过定义一个描述器，你可以在底层捕获核心的实例操作(get、set、delete)，并且可完全自定义它们的行为。这是一个强大的工具，有了它你可以实现很多高级功能，并且它也是很多高级库和框架中的重要工具之一。

描述器的一个比较困惑的地方是它只能在类级别被定义，而不能为每个实例单独定义，因此下面的代码是无法工作的：
```python {cmd}
class Point:
    def __init__(self, x, y):
        self.x = Integer('x')
        self.y = Integer('y')
        self.x = x
        self.y = y
```
同时，`__get__()` 方法实现起来比看起来要复杂得多：
```python
# Descriptor attribute for an integer type-checked attribute
class Integer:
    def Integer:
        def __get__(self, instance, cls):
            if instance is None:
                return self
            else:
                return instance.__dict__[self.name]
```
`__get__()` 看上去有点复杂的原因归结于实例变量和类变量的不同。如果一个描述器被当做一个类变量来访问，那么 `instance` 参数被设置成 `None`。这种情况下,标准做法是简单的返回这个描述符本身即可（尽管你还可以添加其他的自定义操作）。例如：
```python {cmd continue="20180413105631"}
p = Point(2, 3)
print(p.x)
print(Point.x)
```

描述器通常是那些使用到装饰器或元类的大型框架中的一个组件。同时它们的使用也被隐藏在后面。举个例子，下面是一些更高级的基于描述器的代码，并涉及到一个装饰器：
```python
# Descriptor for a type-checked attribute
class Typed:
    def __init__(self, name, expected_type):
        self.name = name
        self.expected_type = expected_type

    def __get__(self, instance, cls):
        if instance is None:
            return self
        else:
            return instance.__dict__[self.name]

    def __set__(self, instance, value):
        if not isinstance(value, self.expected_type):
            raise TypeError('Expected a', self.expected_type)
        else:
            self.__dict__[self.name] = value
    
    def __delete__(self, instance):
        del instance.__dict__[self.name]

# Class decorator that applies it to selected attributes
def typeasset(**kwargs):
    def decorate(cls):
        for name, expected_type in kwargs.items()
            # Attach a Typeddescriptor to the class
            setattr(cls, name, Typed(name, expected_type))
            return cls
    return decorate

# Example use
@typeasset(name=str, shares=int, price=float)
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```
最后要指出的一点是，如果你只是想简单地自定义某个类的单个属性访问的话就不用去写描述器了。这种情况下，property 技术会更加容易。当进程中有很多重复代码的时候，描述器就很有用了（比如你想在你代码的很多地方使用描述器提供的功能或者将它作为一个函数库的特性）。

### 使用延时计算属性 (unfinish)

问题：你想将一个只读属性定义成一个 property，并且只在访问的时候才会出结果，结果一旦被访问后，你希望结果值被缓存起来，不用每次去计算。

解决方案：定义一个延迟属性的一种高效方法是通过使用一个描述器类，如下所示：
```python {cmd id="20180413141943"}
class lazyproperty:
    def __init__(self, func):
        self.func = func
    def __get__(self, instance, cls):
        if instance is None:
            return self
        else:
            value = self.func(instance)
            setattr(instance, self.func.__name__, value)
```
你需要像下面这样在一个类中使用它：
```python {cmd continue="20180413141943"}
import math

class Circle:
    def __init__(self, radius):
        self.radius = radius

```

## 装饰器和闭包

### 装饰器基础知识

装饰器是可调用的对象，其参数使另一个函数（被装饰的函数）。装饰器可能会处理被装饰的函数，然后把它放回，或者将其替换成另一个函数或可调用对象。

假如有个名为 decorate 的装饰器：
```python
@decorate
def target():
    print('running target()')
```
上面代码的效果与下面写法一样：
```python
def target():
    print('running target()')

target = decorate(target)
```
两种写法的最终结果一样：上述两个代码片段执行完毕后得到的 `target` 不一定是原来那个 `target` 函数，而是 `decorate(target)` 返回的函数。

为了确认被装饰的函数会被替换，请看示例 7-1 中的控制台会话。
```python {cmd}
def deco(fun):
    def inner():
        print('running inner()')
    return inner # deco 返回 inner 函数对象

@deco # 使用 deco 装饰 target
def target():
    print('running target()')

target() # 调用被装饰的 target 其实会运行 inner
```
```python
>>> target # 审查对象，发现 target 现在是 inner 的引用
```
```
<function __main__.deco.<locals>.inner>
```
严格来说，装饰器只是语法糖。如前所示，装饰器可以像常规的可调用对象那样调用，其参数是另一个函数。有时，这样做更加方便，尤其是做元编程（在运行时改变程序的行为）时。
```python {cmd}
def deco(fun):
    def inner():
        print('running inner()')
    return inner

def target():
    print('running target')

target = deco(target)
target()
```
综上，装饰器的一大特点是，能把装饰的函数替换成其他函数；第二个特性是，装饰器在加载模块时立即执行；

### Python何时执行装饰器

装饰器的一个关键特性是，它们在被装饰的函数定义之后立即运行。这通常是在导入时（即 Python 加载模块时），如下：
```python 
registry = []

def register(func):
    print(f'running register{func}')
    registry.append(func)
    return func

@register
def f1():
    print('running f1()')

@register
def f2():
    print('running f2()')

def f3():
    print('running f3()')

def main():
    print('running main()')
    print('register ->', register)
    f1()
    f2()
    f3()
if __name__ == '__main__':
    main()
```
```
running register<function f1 at 0x10391f9d8>
running register<function f2 at 0x10391f8c8>
running main()
register -> <function register at 0x1038e2620>
running f1()
running f2()
running f3()
```
注意：register 在模块中其他函数之前运行（两次）。调用 register 时，传递给它的参数使被修饰的函数。

加载模块后，registry 中有两个被装饰函数的引用：`f1` 和 `f2`。这两个函数，以及 `f3` 只有在 `main` 明确调用它们时才会执行。

该例子是为了说明：**函数装饰器在导入模块时，立即执行，而被修饰的函数只有在明确调用时运行，这突出了 Python 程序员所说的导入时和运行时的区别；**

考虑到装饰器在真实代码中的常用方式，上面的示例有两个不寻常的地方：
- 装饰器函数与被装饰的函数在同一个模块中定义。实际情况是，装饰器通常在一个模块中定义，然后应用到其他模块中的函数上；
- `register` 装饰器返回的函数与通过参数传入的相同。实际上，大多数装饰器会在内部定义一个函数，然后将其返回。

虽然示例中的 `register` 装饰器原封不动的返回被装饰的函数，但是这种技术并非没有用处。很多 Python Web 框架使用这样的装饰器吧函数添加到某种中央注册处，例如把URL模式映射成 HTTP 响应的函数上的注册处。这种注册装饰器可能会也可能不会改变被装饰的函数。

### 使用装饰器改进“策略”模式


## 元编程

软件开发领域中最经典的口头禅是“don't repeat yourself”。也就是说，任何时候，当你的程序中存在高度重复（或者是通过剪切复制）的代码时，都应该想想是否有更加好的解决方案。在Python当中，通常可以通过元变成来解决这类问题。简而言之，元变成就是关于创建操作源代码（比如修改、生成或包装原来的代码）的函数和类。主要技术是使用装饰器、类装饰器和元类。不过还有一些其他的技术，包括签名对象、使用 `exec()` 执行代码以及对内部函数和类的反射技术等。

本章的主要目的是向大家介绍这些元编程技术，并且给出实例来演示它们是怎样定制化你的源代码行为的。

### 在函数上添加包装器

问题：你想在函数上添加一个包装器，增加额外的操作处理（比如日志、计时等）。

解决方案：如果你想使用额外的代码包装一个函数，可以定义一个装饰器函数，例如：
```python {cmd}
import time

from functions import wraps

def timethis(func):
    '''
    Decorator that reports the execution time
    '''
    @wraps(func)
```

```python {cmd}
print('abc')
```


