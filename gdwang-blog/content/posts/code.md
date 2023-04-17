---
title: "Code"
date: 2023-03-06T11:05:06+08:00
draft: false
---

# python
## 类型注解
### 变量注解
a:int = 5  
b: float = 3.14  
c: str = 'stereo'  

### 函数注解
def add(a:int =10, b:float = 3.14)->float:  
    return a+ b
从注解中清晰知道接收int 和 float类型， 输出float

### 容器类型
from typing import, List Dict, Tuple   
def find（scores:List[int], ages:Dict[str, int]）->tuple[int, int]:
    return (0, 0)

### 返回类型
from typing import NoReturn, Optional, Union
def foo() ->NoReturn:  

def foo() ->Optional[str]:

def foo() -> Union[str, int, float]:

def foo() -> Any:

def foo(a:Callable):

## kaiming均匀分布 
b = 3   
isinstance (b, str)  return False  

model 可以是fan_in或者fan_out。fan_in 表示使正向传播时，方差一致； fan_out使反向传播时， 方差一致

__file__ file and path
os.path.dirname(__file__) path

