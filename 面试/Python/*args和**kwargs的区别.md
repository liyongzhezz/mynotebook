# *args

*args是用来将参数打包成为元组的，例如：

```python
def args_show(*args):
  print(type(args))
  print(args)
  for index, evl in enumerate(args):
    print(index, evl)
```



上边的函数的输出为：

```python
# args_show(1,2,3)

<type 'tuple'>
(1, 2, 3)
(0, 1)
(1, 2)
(2, 3)
```



如果参数为一个列表，则需要使用```diff - *``` 将列表打散：

```python
args_show(*[1,2,3])
```



# **kwargs

**kwargs会将关键字参数打包成为字典，例如：

```python
def kwargs_show(**kwargs): 	
  print(type(kwargs)) 	
  print(kwargs) 	
  for k, v in kwargs.items(): 		
    print('{}:{}'.format(k, v))
```



上边的函数的输出为：

```python
# kwargs_show(a=1, b=2, c=3) 

<type 'dict'> 
{'a': 1, 'c': 3, 'b': 2} 
a:1 
c:3 
b:2 
```



如果传入的参数是一个字典，则需要使用```diff -  **``` 将元素打散：

```python
kwargs_show(**dict(a=1, b=2, c=3)) 
```



# *args和**kwargs混用的情况

*args和**kwargs混用的情况是一种比较常见得情况，例如：

```python
def print_all(a, *args, **kwargs): 	
  print(a) 	
  if args: 		
    print(args) 	
  if kwargs: 		
    print(kwargs)  

print_all(1, 2, 3, a=4, b=5) 
```

