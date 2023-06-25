# F.relu() 与 nn.ReLU() 的区别

在pytorch中，激活函数的使用方法有两种，分别是：
* 第一种
```python
import torch.nn.functional as F

out =  F.relu(input)
```

* 第二种
```python
import torch.nn as nn

nn.ReLU()
```

## 区别

1. F.relu()是函数调用，一般是用在forward()函数里；nn.ReLU()是模块调用，一般在定义网络层时使用。
2. 当用print(net)输出时，会有nn.ReLU()层，而没有F.relu()

```python
import torch.nn as nn
import torch.nn.functional as F

class NET1(nn.Module):
    def __init__(self):
        super(NET1, self).__init__()
        self.conv = nn.Conv2d(3, 16, 3, 1, 1)
        self.bn = nn.BatchNorm2d(16)
        self.relu = nn.ReLU()  # 模块的激活函数

    def forward(self, x):
        out = self.conv(x)
        x = self.bn(x)
        out = self.relu()
        return out


class NET2(nn.Module):
    def __init__(self):
        super(NET2, self).__init__()
        self.conv = nn.Conv2d(3, 16, 3, 1, 1)
        self.bn = nn.BatchNorm2d(16)

    def forward(self, x):
        x = self.conv(x)
        x = self.bn(x)
        out = F.relu(x)  # 函数的激活函数
        return out

net1 = NET1()
net2 = NET2()
print(net1)
print(net2)
```
![](./img/F.relu%20%26%26%20nn.ReLU.jpg)


