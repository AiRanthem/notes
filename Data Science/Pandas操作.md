# Pandas 操作

## set_index 和 reset_index

https://blog.csdn.net/jingyi130705008/article/details/78162758

+ set_index 将某列设置为索引
+ reset_index 去掉索引

## loc

一些定位的用法

`df.loc[condition, [cols]]`

## 排序

`sort_values(cols)` 相当于 `ORDER BY` 按照某些列排序

## DataFrame的存储属性

`.values`：对应的二维NumPy值数组。
`.columns`：列索引：列名称。
`.index`：行的索引：行号或行名

