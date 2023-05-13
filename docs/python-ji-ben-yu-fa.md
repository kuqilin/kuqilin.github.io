# Python基本函数
##  print（）函数：
```python
print()#括号内可以是变量，字符串，列表，元组等。
```
#### 例：
```python
print('Hello World!')
```
#### 输出效果：
```
Hello World!
```
## input（）函数
```python
a = input()#input()函数必须与变量一起使用
```
$\color{Red}\mathrm{input()的扩号内可以放提示语，但是字符串必须在引号内。}$  
$\color{Cyan}\mathrm{input()函数得到的变量类型是string（即str）}$
#### 例如：
```python
a = input('请输入一个值：')#输入一个值赋予给a
print(type(a))#输出a的类型
```
#### 输入114得到的结果：
```
请输入一个值：114
<class 'str'>
```
- 要想把字符串类型的数字换为整形数，可以用int（）函数
#### 例如：
```python
a = input('请输入数字')#输入数据
a = int(a)#转换为int类型
print(type(a))#输出a的类型
```
#### 输入数字得到的结果是：
```
请输入数字：114
<class 'int'>
```

