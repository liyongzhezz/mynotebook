Python2.6 开始，新增了一种格式化字符串的函数 `str.format()`，它增强了字符串格式化的功能。基本语法是通过 `{}`  来代替以前的 `% `。



>  format 函数可以接受不限个参数，位置可以不按顺序。



例如：

```python
>>>"{} {}".format("hello", "world")    # 不设置指定位置，按默认顺序
'hello world'
 
>>> "{0} {1}".format("hello", "world")  # 设置指定位置
'hello world'
 
>>> "{1} {0} {1}".format("hello", "world")  # 设置指定位置
'world hello world'
```



也可以设置参数：

```python
print("网站名：{name}, 地址 {url}".format(name="alibaba", url="www.alibaba.com"))
 
# 通过字典设置参数
site = {"name": "alibaba", "url": "www.alibaba.com"}
print("网站名：{name}, 地址 {url}".format(**site))
 
# 通过列表索引设置参数
my_list = ['alibaba', 'www.alibaba.com']
print("网站名：{0[0]}, 地址 {0[1]}".format(my_list))  # "0" 是必须的
```





同是支持对数字的格式化输出，例如：

```python
# 保留小数点后两位
>>> print("{:.2f}".format(3.1415926));
3.14

# 带符号保留小数点后两位
>>> print("{:+.2f}".format(3.1415926));
+3.14

# 不到小数
>>> print("{:.0f}".format(3.1415926));
3

# 百分比格式
>>> print("{:.2%}".format(0.25));
25.00%
```

