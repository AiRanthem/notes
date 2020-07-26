# GNN的简单介绍

## GCN

最普通的GNN

对于每一个节点u，先做一波传播（copy、sum），然后将传播的features走LinearLayer即可完成一个GNN Layer.

```python
class GCNLayer(nn.Module):
    def __init__(self, in_feats, out_feats):
        super(GCNLayer, self).__init__()
        self.linear = nn.Linear(in_feats, out_feats)

    def forward(self, g, feature):
        # Creating a local scope so that all the stored ndata and edata
        # (such as the `'h'` ndata below) are automatically popped out
        # when the scope exits.
        with g.local_scope():
            g.ndata['h'] = feature
            g.update_all(gcn_msg, gcn_reduce)
            h = g.ndata['h']
            return self.linear(h)
```

## Relational GCN

 R-GCN用于处理异构图（边、节点具有类型），这种图有三元组组成一条边（联系）：`(src, relation, dest)`。

R-GCN的任务：

+ 实体分类：确定节点属于什么实体类
+ 联系预测：图中可能有丢失的联系（边，三元组），预测还可能存在哪些三元组

两种任务上游工作都是通过GCN完成，不过下游工作会添加其他的神经网络层。

### 问题1：权重矩阵如何构建

本来应该每一种Relation就是一个W权值矩阵，size是 [R, in_fet, out_fet]，但是如果Relation太多的话Parameter就会太多，会过拟合。所以设定一个Basis值B (B<=R)，用B个权值矩阵W。把这些权值矩阵堆叠起来就是我们最终使用的W[B, in_fet, out_fet]

如何将B个关系映射到R个关系？

使用一个size为 [R, B] 的Parameter矩阵w_comp（这个矩阵也是可学习的，就是说把R个关系映射到B个关系是通过Linear来完成的，它的权值就是w_comp）

接下来就是使用线性代数的方法把 [B, in_fet, out_fet] 的size通过矩阵运算变成 [R, in_fet, out_fet]

1. reshape W to [in_fet, B, out_fet]
2. matmul( comp_w, W ) (通过broadcast机制可以变成 [in_fet, R, B] · [in_fet, B, out_fet] -> [in_fet, R, out_fet])
3. reshape W to [in_fet, R, out_fet]

### 问题2：R-GCN Layer的工作流程

R-GCN由于特殊性，简单的builtin的一些图传播函数不能用，需要自己写。每一层R-GCN的工作如下：

1. 解压W到 [R, in_fet, out_fet]
2. 定义 message_func(edges)：
   1. 根据edges的rel_type（联系类别号）从W中取出相应的权值矩阵 Wr[in_fet, out_fet]，Wr = W[edges['rel_type']]
   2. edges的src[n_nodes, in_fet]左乘Wr[in_fet, out_fet]得到msg[n_nodes, out_fet]
   3. 标准化msg，msg = msg*edges['norm']
3. reduce_func直接用builtin的sum保存到属性 'h' 即可，不强求
4. apply_func(nodes)如果有bias和activation，可以这里用上。
5. `g.update_all(message_func, fn.sum(msg='msg', out='h', apply_func = apply_func))`

### 问题3：输入层的优化

为了节省空间，输入层也使用R-GCN Layer。具体做法是：

1. 输入层的W其实不是权值，而是初始的feature，其实W的维度解释为[R, n_nodes, in_fet_to_next_layer]
2. 输入层的message_func直接将W展开为[-1, in_fet_to_next_layer]，然后通过rel_type和src的node_id属性直接获得对应node在对应Relation下的[1, in_fet_to_next_layer]特征。这个特征就是自动初始化好的输入特征了。

```python
import torch
import torch.nn as nn
import torch.nn.functional as F
from dgl import DGLGraph
import dgl.function as fn
from functools import partial

class RGCNLayer(nn.Module):
    def __init__(self, in_feat, out_feat, num_rels, num_bases=-1, bias=None,
                 activation=None, is_input_layer=False):
        super(RGCNLayer, self).__init__()
        self.in_feat = in_feat
        self.out_feat = out_feat
        self.num_rels = num_rels
        self.num_bases = num_bases
        self.bias = bias
        self.activation = activation
        self.is_input_layer = is_input_layer

        # sanity check
        if self.num_bases <= 0 or self.num_bases > self.num_rels:
            self.num_bases = self.num_rels

        # weight bases in equation (3)
        self.weight = nn.Parameter(torch.Tensor(self.num_bases, self.in_feat,
                                                self.out_feat))
        if self.num_bases < self.num_rels:
            # linear combination coefficients in equation (3)
            self.w_comp = nn.Parameter(torch.Tensor(self.num_rels, self.num_bases))

        # add bias
        if self.bias:
            self.bias = nn.Parameter(torch.Tensor(out_feat))

        # init trainable parameters
        nn.init.xavier_uniform_(self.weight,
                                gain=nn.init.calculate_gain('relu'))
        if self.num_bases < self.num_rels:
            nn.init.xavier_uniform_(self.w_comp,
                                    gain=nn.init.calculate_gain('relu'))
        if self.bias:
            nn.init.xavier_uniform_(self.bias,
                                    gain=nn.init.calculate_gain('relu'))

    def forward(self, g):
        if self.num_bases < self.num_rels:
            # generate all weights from bases (equation (3))
            weight = self.weight.view(self.in_feat, self.num_bases, self.out_feat)
            weight = torch.matmul(self.w_comp, weight).view(self.num_rels,
                                                        self.in_feat, self.out_feat)
        else:
            weight = self.weight

        if self.is_input_layer:
            def message_func(edges):
                # for input layer, matrix multiply can be converted to be
                # an embedding lookup using source node id
                embed = weight.view(-1, self.out_feat)
                index = edges.data['rel_type'] * self.in_feat + edges.src['id']
                return {'msg': embed[index] * edges.data['norm']}
        else:
            def message_func(edges):
                w = weight[edges.data['rel_type']]
                msg = torch.bmm(edges.src['h'].unsqueeze(1), w).squeeze()
                msg = msg * edges.data['norm']
                return {'msg': msg}

        def apply_func(nodes):
            h = nodes.data['h']
            if self.bias:
                h = h + self.bias
            if self.activation:
                h = self.activation(h)
            return {'h': h}

        g.update_all(message_func, fn.sum(msg='msg', out='h'), apply_func)
```

