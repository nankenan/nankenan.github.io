---
layout:     post
title:      "Python导入dll"
subtitle:   "初学Python"
date:       2018-05-02 14:31:00
author:     "Nankenan"
header-img: "img/home-bg.jpg"
tags:
    - Python
---

# Python导入dll

首先，我们制作一个dll

```c
	struct my_date
	{
		int year;
		int month;
		int day;
	};

	int Add(int a, int b)
	{
		return a + b;
	}

	int Minus(int a, int b)
	{
		return a - b;
	}

	const char* Show()
	{
		return "Felix Liang is SB";
	}

	std::string Hello(std::string pram)
	{
		return  "Hello!" + pram;
	}

	int ShowYear(my_date date)
	{
		return date.year;
	}

```



在python工程下面，引入 ctypes 库

```
from ctypes import *
```



接下来，导入我们的动态链接库

```
mydll = CDLL("FLISSB.dll")
print(mydll.Add(3,5))
```



这样就可以使用了，是不是so easy



但是我们直接使用 mydll.Show 时 会有如下情况

```
myShow = mydll.Show
print(myShow())
```

结果为：

```
-325713416
```

为什么与函数中的不一样呢？

因为python并不知道函数返回值的类型，我们可以先声明一下



```
#myShow.argtypes = [ctypes.c_char_p]   #参数
myShow.restype = c_char_p              #返回值
print(myShow())
```

结果为

```
Felix Liang is SB
```



接下来我们来处理c中的结构体类型

```python
class Date(Structure):
    _fields_ = [ ("year", c_int),
              ("month", c_int),
              ("day", c_int)]

my_date = Date()
my_date.year = 2018
my_date.month = 5
my_date.day = 2
print(mydll.ShowYear(my_date))
```

输出为

```
2018
```

