# DGL

## 基础操作

### import

```python
import dgl
import numpy as np
```

### 建立DGLGraph

构造函数输入一组表示边的nparray可以一次性添加

```python
dgl.DGLGraph((edges, ...))
```

### 可视化一个图

```python
import networkx as nx
# Since the actual graph is undirected, we convert it for visualization
# purpose.
nx_G = G.to_networkx().to_undirected()
# Kamada-Kawaii layout usually looks pretty for arbitrary graphs
pos = nx.kamada_kawai_layout(nx_G)
nx.draw(nx_G, pos, with_labels=True, node_color=[[.7, .7, .7]])
```

