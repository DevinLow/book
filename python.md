## **Python 笔记**
### **基础使用**
1. **类型转换**

```python
print 'hello python!' #hello python!

x=int('6')
print type(x) #<type 'int'>

y=str(6)
print type(y) #<type 'str'>
```
---
2. **字符串使用及range的使用**
```python
foo='abc'
print len(foo) #3
print range(len(foo)) #[0, 1, 2]
print foo #abc
for i in range(len(foo)):
    print foo[i]
#a
#b
#c
print range(10) # [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print range(1,10,2) #[1, 3, 5, 7, 9]
```
---
3. **dict(字典)及enumerate 函数的使用**
```python
aDict = {'host': 'earth'} #字典 'host' :键值 ‘earth’
aDict['port'] = 80 #添加元素 'port' : 键值 80
for i, key in enumerate(aDict):
     print i, key, aDict[key]
#遍历所有元素及下标
# 下标  元素   键值
#  0    host   earth
#  1    port   80
```
---
4. **类型比较**
```python
# type()获得对象类型
# print type(aDict)
# 使用isinstance来判断对象的类型与类型是否一样
print isinstance(6, int)  # True
print isinstance(aDict, dict)  # True
print isinstance(aDict, list)  # False
```
---
5. **in 操作**
```python
# in 操作
print 'bc' in 'abcd' #True
print 'n' in 'abcd' #False
print 'nm' not in 'abcd' #True
```
---
6.**类型转换**
<table>
    <tr>
        <td bgcolor=#7FFFD4><center>函数</center></td><td td bgcolor=#7FFFD4><center>描述</center></td>
    </tr>
    <tr>
        <td bgcolor=#FAEBD7><center>int(x [,base])</center></td><td bgcolor=#FAEBD7><center>将x转换为一个整数</center></td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>long(x [,base] )</center></td><td bgcolor=#FAEBD7><center>将x转换为一个长整数</center></td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>float(x)</center></td><td bgcolor=#FAEBD7><center>将x转换到一个浮点数</center></td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>complex(real [,imag])</center></td><td bgcolor=#FAEBD7><center>创建一个复数</center></td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>str(x)</td><td bgcolor=#FAEBD7><center>将对象 x 转换为字符串</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>repr(x)</td><td bgcolor=#FAEBD7><center>将对象 x 转换为表达式字符串</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>eval(str)</td><td bgcolor=#FAEBD7><center>用来计算在字符串中的有效Python表达式,并返回一个对象</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>tuple(s)</td><td bgcolor=#FAEBD7><center>将序列 s 转换为一个元组</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>list(s)</td><td bgcolor=#FAEBD7><center>将序列 s 转换为一个列表</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>set(s)</td><td bgcolor=#FAEBD7><center>转换为可变集合</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>dict(d)</td><td bgcolor=#FAEBD7><center>创建一个字典。d 必须是一个序列 (key,value)元组。</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>frozenset(s)</td><td bgcolor=#FAEBD7><center>转换为不可变集合</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>chr(x)</td><td bgcolor=#FAEBD7><center>将一个整数转换为一个字符</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>unichr(x)</td><td bgcolor=#FAEBD7><center>将一个整数转换为Unicode字符</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>ord(x)</td><td bgcolor=#FAEBD7><center>将一个字符转换为它的整数值</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>hex(x)</td><td bgcolor=#FAEBD7><center>将一个整数转换为一个十六进制字符串</td>
    </tr>
    <tr>
    <td bgcolor=#FAEBD7><center>oct(x)</td><td bgcolor=#FAEBD7><center>将一个整数转换为一个八进制字符串</td>
    </tr>
</table>

---

7. **Python逻辑运算符之 and or not**  

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

a = 10
b = 20

if ( a and b ):
   print "1 - 变量 a 和 b 都为 true"
else:
   print "1 - 变量 a 和 b 有一个不为 true"

if ( a or b ):
   print "2 - 变量 a 和 b 都为 true，或其中一个变量为 true"
else:
   print "2 - 变量 a 和 b 都不为 true"

# 修改变量 a 的值
a = 0
if ( a and b ):
   print "3 - 变量 a 和 b 都为 true"
else:
   print "3 - 变量 a 和 b 有一个不为 true"

if ( a or b ):
   print "4 - 变量 a 和 b 都为 true，或其中一个变量为 true"
else:
   print "4 - 变量 a 和 b 都不为 true"

if not( a and b ):
   print "5 - 变量 a 和 b 都为 false，或其中一个变量为 false"
else:
   print "5 - 变量 a 和 b 都为 true"
```
---

8. **Python成员运算符之 in not in**

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

a = 10
b = 20
list = [1, 2, 3, 4, 5 ];

if ( a in list ):
   print "1 - 变量 a 在给定的列表中 list 中"
else:
   print "1 - 变量 a 不在给定的列表中 list 中"

if ( b not in list ):
   print "2 - 变量 b 不在给定的列表中 list 中"
else:
   print "2 - 变量 b 在给定的列表中 list 中"

# 修改变量 a 的值
a = 2
if ( a in list ):
   print "3 - 变量 a 在给定的列表中 list 中"
else:
   print "3 - 变量 a 不在给定的列表中 list 中"
```
---

9.**Python身份运算符之 is is not**

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

a = 20
b = 20

if ( a is b ):
   print "1 - a 和 b 有相同的标识"
else:
   print "1 - a 和 b 没有相同的标识"

if ( id(a) == id(b) ):
   print "2 - a 和 b 有相同的标识"
else:
   print "2 - a 和 b 没有相同的标识"

# 修改变量 b 的值
b = 30
if ( a is b ):
   print "3 - a 和 b 有相同的标识"
else:
   print "3 - a 和 b 没有相同的标识"

if ( a is not b ):
   print "4 - a 和 b 没有相同的标识"
else:
   print "4 - a 和 b 有相同的标识"
```
---

10. **for 循环**

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
for letter in 'Python':     # 第一个实例
   print '当前字母 :', letter
 
fruits = ['banana', 'apple',  'mango']
for fruit in fruits:        # 第二个实例
   print '当前字母 :', fruit
 
print "Good bye!"
```

**通过序列索引迭代**

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
fruits = ['banana', 'apple',  'mango']
for index in range(len(fruits)):
   print '当前水果 :', fruits[index]
 
print "Good bye!"
```
**循环使用 else 语句**
```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-
 
for num in range(10,20):  # 迭代 10 到 20 之间的数字
   for i in range(2,num): # 根据因子迭代
      if num%i == 0:      # 确定第一个因子
         j=num/i          # 计算第二个因子
         print '%d 等于 %d * %d' % (num,i,j)
         break            # 跳出当前循环
   else:                  # 循环的 else 部分
      print num, '是一个质数'
```

**实例：以下实例使用了嵌套循环输出2~100之间的素数：**

```python
#!/usr/bin/python
# -*- coding: UTF-8 -*-

i = 2
while(i < 100):
   j = 2
   while(j <= (i/j)):
      if not(i%j): break
      j = j + 1
   if (j > i/j) : print i, " 是素数"
   i = i + 1

print "Good bye!"
```
---
