---
layout: post
title: tensorflow中的padding方式
tags: [cnn, layers, padding, tensorflow]
---

### tensorflow中convolution的padding

首先简单回顾一下卷积计算的基本原理

![conv1](/images/posts/2018-04-12/conv1.PNG)

当channel = 2, output_channel = 1:

![conv1](/images/posts/2018-04-12/conv1.PNG)

- padding='SAME'

先根据输入feature的宽高和步长来计算输出的宽高

{% highlight python %}
out_height = ceil(float(in_height) / float(stride))
out_width = ceil((float(in_width)) / float(stride))
{% endhighlight %}

然后计算需要填充的尺寸

{% highlight python %}
if in_height % stride == 0:
    pad_along_height = max(filter_height - stride, 0)
else:
    pad_along_height = max(filter_height - (in_height % stride), 0)

if in_width % stride == 0:
    pad_along_width = max(filter_width - stride, 0)
else:
    pad_along_width = max(filter_width - (in_width % stride), 0)
{% endhighlight %}

最后计算四个边分别填充的尺寸，填充顺序是优先填充右下，然后填充左上，可以非对称填充

{% highlight python %}
pad_top = pad_along_height // 2
pad_bottom = pad_along_height - pad_top

pad_left = pad_along_width // 2
pad_right = pad_along_width - pad_left
{% endhighlight %}

> 注意，caffe中只支持对称填充，mxnet中填充优先级与tensorflow相反

填充方式举例如下

> in_height = 5, in_width = 5, kernel_size = [3, 3], stride = 1:

![padding_same_1](/images/posts/2018-04-12/padding_same_1.PNG)

- padding = 'VALID'

不做填充，不够卷积的边舍弃

{% highlight python %}
out_height = ceil(float(in_height - filter_height + 1) / float(stride))
out_width = ceil((float(in_width - filter_width + 1)) / float(stride))
{% endhighlight %}

填充方式举例如下

> in_height = 4, in_width = 4, kernel_size = [3, 3], stride = 2:

![padding_valid_1](/images/posts/2018-04-12/padding_valid_1.PNG)

### tensorflow中maxpooling的padding

首先简单回顾一下最大池化计算的基本原理

![maxpooling_1](/images/posts/2018-04-12/maxpooling_1.PNG)

同样，maxpooling也有`SAME`和`VALID`两种填充方式

- padding='SAME'

{% highlight python %}
if padding == 'same':
    output_length = input_length
out_height = (output_length + stride - 1) // stride
{% endhighlight %}

![maxpooling_2](/images/posts/2018-04-12/maxpooling_2.PNG)

- padding='valid'

{% highlight python %}
if padding == 'valid':
    output_length = input_length - kernel_size + 1
out_height = (output_length + stride - 1) // stride
{% endhighlight %}

![maxpooling_3](/images/posts/2018-04-12/maxpooling_3.PNG)
