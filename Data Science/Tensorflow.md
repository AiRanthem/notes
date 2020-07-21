# Tensorflow

## 兼容性

+ tf2.x兼容1.x的方法：

  ​	把 `import tensorflow as tf` 改为 `import tensorflow.compat.v1 as tf`

+ `tf.placeholder() is not compatible with eager execution.`

  ​	在import语句后加上 `tf.compat.v1.disable_eager_execution()`