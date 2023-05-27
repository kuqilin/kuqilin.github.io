# 栈、队列和双端队列的Python实现
## 栈的Python实现
栈是元素的有序集合，添加操作与移除操作都发生在其顶端。栈的操作顺序是**LIFO**（ _Last In First Out_ )。~~这种东西小学生都会。~~如果不会请点击[链接](https://baike.baidu.com/item/%E6%A0%88/12808149)查看。   
栈支持以下操作：   
- _Stack()_ 创建一个空栈。
- _push(item)_ 讲一个元素添加到栈的顶端。需要参数 _item_ ，且无返回值。
-  _pop()_ 将栈顶端的元素移除。不需要参数，但会返回顶端的元素，并且修改栈的内容。
-  _peek()_ 返回栈顶端的元素，但是并不移除该元素。不需要参数，也不会修改栈的内容。
-  _isEmpty()_ 检查栈是否为空。不需要参数，且返回布尔值。
-  _size()_ 返回栈中元素的数目。不需要参数，且返回一个整数。  
- 栈的方法：   

|栈操作|栈内容|返回值|
|:--:|:--:|:--:|
|`s.isEmpty()`|`[]`|`True`|
|`s.push(4)`|`[4]`|`None`|
|`s.push('dog')`|`[4, 'dog']`|`None`|
|`s.peek()`|`[4, 'dog']`|`'dog'`|
|`s.push(True)`|`[4, 'dog', True]`|`None`|
|`s.size()`|`[4, 'dog', True]`|`3`|
|`s.isEmpty()`|`[4, 'dog', True]`|`False`|
|`s.pop()`|`[4, 'dog']`|`True`|
|`s.size()`|`[4, 'dog']`|`2`|
#### 代码如下：   
```python
class Stack:
    '''这是一个栈'''
    def __init__(self):
        self.items = []
    def isEmpty(self):
        return self.items == []
    def push(self, item):
        self.items.append(item)
    def pop(self):
        return self.items.pop()
    def peek(self):
        return self.items[len(self.items)-1]
    def size(self):
        return len(self.items)
    def __str__(self):
        return str(self.items)
    def __repr__(self):
        return str(self.items)
```   
#### 以上代码的栈操作及其返回值：
```
>>> s = Stack()
>>> s.isEmpty()
True
>>> s.push(4)
>>> s.push('dog')
>>> s.peek()
'dog'
>>> s.push(True)
>>> s.size()
3
>>> s.isEmpty()
False
>>> s.pop()
True
>>> s.size()
2
>>>print(s)
[4, 'dog']
```
## 队列的Python实现
队列是有序集合，添加操作发生在“尾部”，移除操作发生在“头部”。操作顺序是**FIFO**（ _First In First Out_ ）~~这东西小学二年级就学了（确信）。~~一年级请点击[链接](https://baike.baidu.com/item/%E9%98%9F%E5%88%97/14580481)学习。   
队列支持以下操作：
-  _Queue()_ 创建一个空队列。
- _enqueue(item)_ 在队尾添加 _item_ 元素，没有返回值。
- _dequeue()_ 在队头返回并移除一个元素。
- _isEmpty()_ 检查队列是否为空，不需要参数，返回布尔值。
- _size()_ 返回队列中元素的数目，整数型。   
队列的方法：   

| 队列操作 | 队列内容 | 返回值 |
| :----------- | :----------- | :----------- |
| q.isEmpty() | [] | True |
| q.enqueue(4) | [4] | None |
| q.enqueue('dog') | ['dog', 4] | None |
| q.enqueue(True) | [True, 'dog', 4] | None |
| q.size() | [True, 'dog', 4] | 3 |
| q.isEmpty() | [True, 'dog', 4] | False |
| q.dequeue() | [True, 'dog'] | 4 |
| q.size() | [True, 'dog'] | 2 |
#### 用Python实现队列:   
```python
class Queue:
    '''这是一个队列'''
    def __init__(self):
        self.items = []
    def isEmpty(self):
        return self.item == []
    def enqueue(self, item):
        self.items.append(item)
    def dequeue(self):
        self.items.pop(0)
    def size(self):
        return len(self.items)
    def __repr__(self):
        return str(self.items)
```
#### 以上代码的队列操作及其返回值：   
```
>>> q = Queue()
>>> q.isEmpty()
True
>>> q.enqueue(4)
>>> q.enqueue('dog')
>>> q.enqueue(True)
>>> q.size()
3
>>> q.isEmpty()
False
>>> q.dequeue()
4
>>> q.size()
2
>>> print(q)
['dog', True]
```
