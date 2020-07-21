# PyG

使用PyG创建GNN的标准过程

## 数据预处理

按照需求处理df即可

## Data对象

Data对象就是一个图。

```python
from torch_geometric.data import Data
```

Data构造函数有三个参数：

`x`  二维Tensor，列数是节点数，每一列是一个节点的特征

`y`  一维Tensor，每个节点的标签

`edge_index`   二维Tensor，两行，每一列是一条边。

## 构建Dataset

https://blog.csdn.net/TwT520Ly/article/details/105633847

`InMemoryDataset`需要实现四个方法：

`raw_file_names()` 返回一个包含所有未处理过的数据文件的文件名的列表。

起始也可以返回一个空列表，然后在后面要说的 process() 函数里再定义。

`processed_file_names()` 返回一个包含所有处理过的数据文件的文件名的列表。

`download()` 如果在数据加载前需要先下载，则在这里定义下载过程，下载到 self.raw_dir 中定义的文件夹位置。

如果不需要下载，返回 pass 即可。

`process()` 这是最重要的一个函数，我们需要在这个函数里把数据处理成一个 Data 对象。

标准套路如下：

```python
class MyOwnDataset(InMemoryDataset):
    def __init__(self, root, transform=None, pre_transform=None):
        super(MyOwnDataset, self).__init__(root, transform, pre_transform)
        self.data, self.slices = torch.load(self.processed_paths[0])
 
    @property
    def raw_file_names(self):
        return ['some_file_1', 'some_file_2', ...]
 
    @property
    def processed_file_names(self):
        return ['data.pt']
 
    def download(self):
        # Download to `self.raw_dir`.
        pass
 
    def process(self):
        # Read data into huge `Data` list.
        data_list = [...]
 
        if self.pre_filter is not None:
            data_list = [data for data in data_list if self.pre_filter(data)]
 
        if self.pre_transform is not None:
            data_list = [self.pre_transform(data) for data in data_list]
            
        # 总之就是生成一组Data图对象放在data_list列表中，然后执行下面两行代码
        # slices是一个字典，类似元数据。
        data, slices = self.collate(data_list)
        torch.save((data, slices), self.processed_paths[0])
```

`Dataset`则需要向标准的dataset一样实现`len`和`getitem`方法。注意：不是魔术方法。