# Tensorflow

## 兼容性

+ tf2.x兼容1.x的方法：

  ​	把 `import tensorflow as tf` 改为 `import tensorflow.compat.v1 as tf`

+ `tf.placeholder() is not compatible with eager execution.`

  ​	在import语句后加上 `tf.compat.v1.disable_eager_execution()`

## tf.app

`tf.app.flags` 表示一组命令行参数，通过 `tf.app.flags.DEFINE_<type>`来创建默认值，命令行传参来覆盖修改，通过`tf.app.flags.FLAGS`来访问。

https://blog.csdn.net/lyc_yongcai/article/details/73456960

`tf.app.run()` 首先通过命令行参数覆盖FLAGS的内容，然后调用`main`函数。