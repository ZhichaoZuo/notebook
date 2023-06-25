
调用forward()的时间：**只要在实例化一个对象中传入对应的参数就可以自动调用 forward 函数**

这里定义的MLP类重载了Module类的`__init__`函数和`forward()`函数。它们分别用于创建模型参数和定义前向计算。前向计算也即正向传播。
```python
import torch
from torch import nn
 
class MLP(nn.Module):
    # 声明带有模型参数的层，这里声明了两个全连接层
    def __init__(self, **kwargs):
        # 调用MLP父类Module的构造函数来进行必要的初始化。这样在构造实例时还可以指定其他函数
        # 参数，如“模型参数的访问、初始化和共享”一节将介绍的模型参数params
        super(MLP, self).__init__(**kwargs)
        self.hidden = nn.Linear(784, 256) # 隐藏层
        self.act = nn.ReLU()
        self.output = nn.Linear(256, 10)  # 输出层
 
    # 定义模型的前向计算，即如何根据输入x计算返回所需要的模型输出
    def forward(self, x):
        a = self.act(self.hidden(x))
        return self.output(a)
```

```python
X = torch.rand(2, 784)
net = MLP()
print(net)
net(X)
```
输出：

```python
MLP(
  (hidden): Linear(in_features=784, out_features=256, bias=True)
  (act): ReLU()
  (output): Linear(in_features=256, out_features=10, bias=True)
)

tensor([[ 0.0992, -0.0295,  0.0016, -0.1259, -0.0887,  0.0229,  0.0071, -0.2133,
         -0.1908, -0.0562],
        [ 0.0019,  0.0273, -0.0090, -0.0068, -0.2026, -0.0125,  0.1636, -0.1160,
         -0.1401,  0.0492]], grad_fn=<AddmmBackward0>)
```
- 注意上面输出的net是没有经过`forward()`的，只是简单的网络定义，排列
- 当执行`net(x)`的时候，会自动调用`__call__()`函数，从而调用`forward()`函数(继承的`nn.Module`中包含了`__call__`函数)