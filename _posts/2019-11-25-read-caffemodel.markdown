---
layout: post
title: caffemodel的增删查改
tags: [cnn, caffe, caffemodel]
---

> 本文脚本使用python

先说一下如何读取caffemodel，caffemodel包含两个文件，一个是prototxt网络描述文件，存储了网络图，一个是caffemodel权重文件，存储了模型权重的相关参数和具体信息，读取caffemodel使用`caffe`包提供的接口

{% highlight python %}
import caffe
import cv2

deploy = './deploy.prototxt'
model = 'model.caffemodel'
img = np.transpose(cv2.imread('img.jpg'), (2, 0, 1))
net = caffe.Net(deploy, model, caffe.TEST)
net.blobs['data'].data[...] = img.reshape(1, 3, img.shape[1], img.shape[2])
res = net.forward()
{% endhighlight %}

res即为模型的输出结果，若模型有多个输出结果，则res为一个包含了所有输出结果的列表

### 查

caffemodel查询某一层的输出结果或者权重，使用要查询层的`name`作为键在`net`类索引对应层的权重和输出

{% highlight python %}
net = caffe.Net(deploy, model, caffe.TEST)
res = net.forward()

cnt_layer_weights = net.params['dst_layer_name'][0].data
cnt_layer_output = net.blobs['dst_layer_name'].data
{% endhighlight %}

### 增

- 添加的层无权重

如果添加的层没有权重，比如relu，reshape等层，则直接在prototxt文件的对应位置添加该层的layer描述内容保存即可，记得更改`bottom`和`top`，是上下文连贯

- 添加的层有权重

如果添加的层有权重，先将要添加的层对应的layer描述文件写入prototxt文件对应的位置，要添加的权重转为numpy数组类型，`net`类参数改为`caffe.TRAIN`，此模式下可以重写caffemodel

{% highlight python %}
net = caffe.Net(deploy, model, caffe.TRAIN)

add_weights = np.array(your_weights)
add_bias = np.array(your_bias)
net.params['add_layer_name'][0].data.flat = add_weights.flat
net.params['add_layer_name'][1].data.flat = add_bias.flat

res = net.forward()
net.save('dst.caffemodel')
{% endhighlight %}

### 删

- 删除无权重的层

如果要删除的层没权重，在prototxt文件中删除对应层，更改上下文的`bottom`和`top`即可，当然，此层的删除要合理，不能让模型变得没意义

- 删除有权重的层

如果要删除的层有权重，在prototxt文件中删除对应层，加载模型运行一次forward重新保存模型即可

{% highlight python %}
net = caffe.Net(deploy, model, caffe.TRAIN)
res = net.forward()
net.save('dst.caffemodel')
{% endhighlight %}

### 改

如果要改的层不含权重，则只在prototxt文件中更改对应层即可，如果要更改的层包含权重，并且要更改权重，则加载模型，对要更改的层的权重重新赋值，运行一次forward保存模型即可

{% highlight python %}
net = caffe.Net(deploy, model, caffe.TRAIN)

change_weights = np.array(your_weights)
change_bias = np.array(your_bias)
net.params['change_layer_name'][0].data.flat = change_weights.flat
net.params['change_layer_name'][1].data.flat = change_bias.flat

res = net.forward()
net.save('dst.caffemodel')
{% endhighlight %}
