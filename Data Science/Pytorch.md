# PyTorch

## Tensor

### 维度操作

https://zhuanlan.zhihu.com/p/86763381

`tensor.squeeze(dim = None, out = None) or torch.squeeze(input, dim = None, out = None)`

- **作用**：降维

- 将输入张量形状中的1 去除并返回。 如果输入是形如(A×1×B×1×C×1×D)，那么输出形状就为： (A×B×C×D)

  当给定dim时，那么挤压操作只在给定维度上。例如，输入形状为: (A×1×B), `squeeze(input, 0)` 将会保持张量不变，只有用 `squeeze(input, 1)`，形状会变成 (A×B)。

`tensor.unsqueeze(dim, out = None)` or `torch.unsqueeze(x, dim, out = None)`

- **作用**：扩展维度

+ 返回一个新的张量，对输入的既定位置插入维度 1

- **参数:**
- `tensor (Tensor)` – 输入张量
- `dim (int)` – 插入维度的索引
- `out (Tensor, optional)` – 结果张量

### 矩阵操作

`torch.bmm(a, b)`

两个矩阵的乘法。a、b的size分别是 (b, n, m) (b, m, p)，输出(b, n, p)