title: Tensorflow-Java
author: Mentran
tags:
  - pb
  - model
  - java
  - tf
categories:
  - tensorflow
  - java
date: 2019-08-21 11:12:00
---

### Tensorflow模型保存和读取
#### 1. pb模型保存
```python
# save for java
frozen_graph_def = tf.graph_util.convert_variables_to_constants(sess, sess.graph_def, output_node_names=["prob", "accuracy"])
# save as.pb file
tf.train.write_graph(frozen_graph_def, 'path', "model_name.pb", as_text=False)
tf.train.write_graph(frozen_graph_def, 'path', "model_name.txt", as_text=True)
```
* pb 文件已经固化了网络模型结构，因此可恢复训练模型的结构和所有参数，但是必须知道原网络模型的输入和输出的节点名称
* 其中output_node_names中的名称必须与模型中的节点名称一致，例如
    ```python
    (self.prob = tf.nn.softmax(self.outputs, name="prob"))
    ```
* 注意节点名称，应包含name_scope 和 variable_scope命名空间，并用“/”隔开，如"InceptionV3/Logits/SpatialSqueeze"
* 保存模型可以选择pb格式或者txt格式

#### 2. pb模型读取
```python
with tf.Graph().as_default():
    output_graph_def = tf.GraphDef()
    with open(pb_path, "rb") as f:
        output_graph_def.ParseFromString(f.read())
        tf.import_graph_def(output_graph_def, name="")
    with tf.Session() as sess:
        sess.run(tf.global_variables_initializer())
        # 定义输入的张量名称,对应网络结构的输入张量
        # input:0作为输入图像,keep_prob:0作为dropout的参数,测试时值为1,is_training:0训练参数
        input_image_tensor = sess.graph.get_tensor_by_name("input:0")
        input_keep_prob_tensor = sess.graph.get_tensor_by_name("keep_prob:0")
        input_is_training_tensor = sess.graph.get_tensor_by_name("is_training:0")

        # 定义输出的张量名称
        output_tensor_name = sess.graph.get_tensor_by_name("InceptionV3/Logits/SpatialSqueeze:0")

        # 读取测试图片
        im=read_image(image_path,resize_height,resize_width,normalization=True)
        im=im[np.newaxis,:]
        # 测试读出来的模型是否正确，注意这里传入的是输出和输入节点的tensor的名字，不是操作节点的名字
        # out=sess.run("InceptionV3/Logits/SpatialSqueeze:0", feed_dict={'input:0': im,'keep_prob:0':1.0,'is_training:0':False})
        out=sess.run(output_tensor_name, feed_dict={input_image_tensor: im,
                                                    input_keep_prob_tensor:1.0,
                                                    input_is_training_tensor:False})
```
* 模型中节点的名称可以通过保存的 **text 模型文件中进行查看和修改**

#### 3. ckpt模型的保存
```python
import tensorflow as tf
# 声明两个变量
v1 = tf.Variable(tf.random_normal([1, 2]), name="v1")
v2 = tf.Variable(tf.random_normal([2, 3]), name="v2")
init_op = tf.global_variables_initializer() # 初始化全部变量
saver = tf.train.Saver() # 声明tf.train.Saver类用于保存模型
with tf.Session() as sess:
    sess.run(init_op)
    print("v1:", sess.run(v1)) # 打印v1、v2的值一会读取之后对比
    print("v2:", sess.run(v2))
    saver_path = saver.save(sess, "save/model.ckpt")  # 将模型保存到save/model.ckpt文件
    print("Model saved in file:", saver_path)
```
保存后有4个文件：
>* checkpoint 是检查点文件，文件保存了一个目录下所有的模型文件列表
>* model.ckpt.meta 文件保存了TensorFlow计算图的结构，可以理解为神经网络的网络结构，该文件可以被 tf.train.import_meta_graph 加载到当前默认的图来使用。
>* ckpt.data 保存模型中每个变量的取值
>* ckpt.index 索引文件

#### 4. ckpt模型转换成pb模型文件
>* 通过传入 CKPT 模型的路径得到模型的图和变量数据
>* 通过 import_meta_graph 导入模型中的图
>* 通过 saver.restore 从模型中恢复图中各个变量的数据
>* 通过 graph_util.convert_variables_to_constants 将模型持久化
```python
def freeze_graph(input_checkpoint, output_graph):
'''
:param input_checkpoint:
:param output_graph: PB模型保存路径
:return:
'''
# checkpoint = tf.train.get_checkpoint_state(model_folder) #检查目录下ckpt文件状态是否可用
# input_checkpoint = checkpoint.model_checkpoint_path #得ckpt文件路径

# 指定输出的节点名称,该节点名称必须是原模型中存在的节点
output_node_names = "InceptionV3/Logits/SpatialSqueeze"
saver = tf.train.import_meta_graph(input_checkpoint + '.meta', clear_devices=True)

with tf.Session() as sess:
    saver.restore(sess, input_checkpoint) #恢复图并得到数据
    output_graph_def = graph_util.convert_variables_to_constants(  # 模型持久化，将变量值固定
        sess=sess,
        input_graph_def=sess.graph_def,# 等于:sess.graph_def
        output_node_names=output_node_names.split(","))# 如果有多个输出节点，以逗号隔开

    with tf.gfile.GFile(output_graph, "wb") as f: #保存模型
        f.write(output_graph_def.SerializeToString()) #序列化输出
    print("%d ops in the final graph." % len(output_graph_def.node)) #得到当前图有几个操作节点
```

#### 5. pb模型和txt模型的转换
```python
import tensorflow as tf
from google.protobuf import text_format
from tensorflow.python.platform import gfile

def convert_pb_to_pbtxt(filename):
    with gfile.FastGFile(filename, 'rb') as f:
        graph_def = tf.GraphDef()
        graph_def.ParseFromString(f.read())
        tf.import_graph_def(graph_def, name='')
        tf.train.write_graph(graph_def, './', 'protobuf.pbtxt', as_text=True)
    return

def convert_pbtxt_to_pb(filename):
    """Returns a `tf.GraphDef` proto representing the data in the given pbtxt file.
    Args:
    filename: The name of a file containing a GraphDef pbtxt (text-formatted
        `tf.GraphDef` protocol buffer data).
    """
    with tf.gfile.FastGFile(filename, 'r') as f:
        graph_def = tf.GraphDef()

        file_content = f.read()

        # Merges the human-readable string in `file_content` into `graph_def`.
        text_format.Merge(file_content, graph_def)
        tf.train.write_graph(graph_def, './', 'protobuf.pb', as_text=False)
    return
```

### Java 调用 Tensorflow 模型
#### 1. 通过 **Maven** 引用依赖文件
```xml
<dependency>
    <groupId>org.tensorflow</groupId>
    <artifactId>tensorflow</artifactId>
    <version>1.13.1</version>
</dependency>
```

#### 2. 通过 Java 原生接口 (JNI) 与 JDK 配合使用
* 下载 **libtensorflow.jar** 包
* 下载 **tensorflow_jni.dll** 文件
* 下载地址：https://www.tensorflow.org/install/lang_java#download

#### 3. 调用 **pb** 模型
```java
try (Graph graph = new Graph()) {
    //导入图
    byte[] graphBytes = IOUtils.toByteArray(new FileInputStream(model_path));
    graph.importGraphDef(graphBytes);

    //根据图建立Session
    try (Session session = new Session(graph)) {
        //相当于TensorFlow Python中的sess.run(["out_name1", "out_name2"], feed_dict = {"input_name": 10.0})
        // 注意这里的名称必须与模型中的节点名称一致
        // 可通过feed和fetch输入输出多个结果
        List<Tensor<?>> tensorList = session.runner()
                .feed("input_name", Tensor.create(10.0f))
                .fetch("out_name1")
                .fetch("out_name2")
                .run();
        for (Tensor t : tensorList) {
            System.out.println(t.floatValue());
        }
    }
}
```
* 可通过保存的 **txt** 模型查看节点的名称
* 可修改保存的 **txt** 模型中的参数值，并将 **txt** 模型再转换成 **pb** 模型进行使用

#### 示例：修改dropout值
在训练tensorflow模型时，经常需要设置 dropout；但是在测试时需要将dropout值固定；有时会忘记把dropout设置为placeholder进行传值，就可以通过修改保存的 pb 模型中的dropout值来达到目的（这样就不需要重新训练模型和修改模型结构）

>1. 将 **pb** 模型转换成 **txt** 模型
>2. 在 **txt** 模型中查询 **dropout/keep_prob** 将其中的 **float_val** 改为 **1.0** 即可
>3. 再将 **txt** 模型转回 **pb** 模型