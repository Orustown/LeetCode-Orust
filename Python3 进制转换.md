# Python3实现进制转换（基准转换）


### 内置函数
|-|2进制|8进制|10进制|16进制|
|:--:|:--:|:--:|:--:|:--:|
|2进制|-|bin(int(x, 8))|bin(int(x, 10))|bin(int(x, 16))|
|8进制|oct(int(x, 2))|-|oct(int(x, 10))|oct(int(x, 16))|
|10进制|int(x, 2)|int(x, 8)|-|int(x, 16)|
|16进制|hex(int(x, 2))|hex(int(x, 8))|hex(int(x, 10))|-|

其中，bin()、oct()、hex()的返回值均为字符串，且分别带有0b、0o、0x前缀


### 代码实现
输入一个**十进制非负整数**num与基数b，返回一个字符串：是b进制（基准）下的num表现形式

即十进制非负整数转换为任意进制（36以内）

一行实现
```python3
# @return a string
def baseN(num, b):
    return ((num == 0) and "0") or (baseN(num // b, b).lstrip("0") + "0123456789abcdefghijklmnopqrstuvwxyz"[num % b])
```

展开形式
```python3
# @return a string
def baseN(num, b):
    if num == 0:
        return "0"
    S = "0123456789abcdefghijklmnopqrstuvwxyz"
    res = baseN(num // b, b) + S[num % b]
    return res.lstrip("0")  # 截掉字符串左边的"0"
```
