# zer0n/deepframeworks

[Original URL](https://github.com/zer0n/deepframeworks/)

> Abstract. In this study, I evaluate some popular deep learning toolkits. The candidates are listed in alphabetical order: Caffe, CNTK, TensorFlow, Theano, and Torch. This is a dynamic document and...

**Abstract.** In this study, I evaluate some popular deep learning toolkits. The candidates are listed in alphabetical order: [Caffe](https://github.com/BVLC/caffe), [CNTK](https://cntk.codeplex.com/), [TensorFlow](https://github.com/tensorflow/tensorflow), [Theano](https://github.com/Theano/Theano), and [Torch](https://github.com/torch/torch7). This is a dynamic document and the evaluation, to the best of my knowledge, is based on the current state of their code.

I also provide ratings in some areas because for a lot of people, ratings are useful. However, keep in mind that ratings are inherently subjective [1].

If you find something wrong or inadequate, please help improve by filing an issue.

**Table of contents**

## [](https://github.com/zer0n/deepframeworks/#modeling-capability)Modeling Capability

In this section, we evaluate each toolkit's ability to train common and state-of-the-art networks without writing too much code. Some of these networks are:

- ConvNets: AlexNet, OxfordNet, GoogleNet
- RecurrentNets: plain RNN, LSTM/GRU, bidirectional RNN
- Sequential modeling with attention.

In addition, we also evaluate the flexibility to create a new type of model.

### [](https://github.com/zer0n/deepframeworks/#caffe-)Caffe [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

Caffe is perhaps the first mainstream industry-grade deep learning toolkit, started in late 2013, due to its excellent convnet implementation (at the time). It is still the most popular toolkit within the computer vision community, with many extensions being actively added.

However, its support for recurrent networks and language modeling in general is poor, due to its legacy architecture, which's limitations are detailed in the [architecture section](https://github.com/zer0n/deepframeworks/#architecture).

### [](https://github.com/zer0n/deepframeworks/#cntk)CNTK

CNTK is a deep learning system started by the speech people who [started the deep learning craze](http://citeseerx.ist.psu.edu/viewdoc/download?doi=10.1.1.185.1908&rep=rep1&type=pdf) and grown into a more general platform-independent deep learning system. It is better known in the speech community than in the general deep learning community.

In CNTK (as in TensorFlow and Theano), a network is specified as a symbolic graph of vector operations, such as matrix add/multiply or convolution. A layer is just a composition of those operations. The fine granularity of the building blocks (operations) allows users to invent new complex layer types without implementing them in a low-level language (as in Caffe).

More to be written ...

### [](https://github.com/zer0n/deepframeworks/#tensorflow-)TensorFlow [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_stars.png)](https://camo.githubusercontent.com/4f8f2790b8f2abab48a64329fd5fc2b70cf63185/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f73746172732e706e67)

**State-of-the-art models**

- RNN API and implementation are suboptimal. The team also commented about it [here](https://github.com/tensorflow/tensorflow/issues/7) and [here](https://groups.google.com/a/tensorflow.org/forum/?utm_medium=email&utm_source=footer#!msg/discuss/B8HyI0tVtPY/aR43OIuUAwAJ).
- Bidirectional RNN [not available yet](https://groups.google.com/a/tensorflow.org/forum/?utm_medium=email&utm_source=footer#!msg/discuss/lwgaL7WEuW4/UXaL4bYkAgAJ)
- No 3D convolution, which is useful for video recognition

**New models** Since TF uses symbolic graph of vector operations approach, specifying a new network is fairly easy.

The public release of TF doesn't yet support loop and condition controls in the graph definition. This makes RNN implementations less ideal because they have to use Python loops and no graph compiler optimization can be made.

Google claimed to have this in their [white paper](http://download.tensorflow.org/paper/whitepaper2015.pdf) and [details are still being worked out](https://github.com/tensorflow/tensorflow/issues/208).

### [](https://github.com/zer0n/deepframeworks/#theano-)Theano [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

**State-of-the-art models.** Theano has implementation for most state-of-the-art networks, either in the form of a higher-level framework (e.g. [Blocks](https://github.com/mila-udem/blocks), [Keras](https://github.com/fchollet/keras), etc.) or in pure Theano. In fact, many recent research ideas (e.g. attentional model) started here.

**New models.** Theano pioneered the trend of using symbolic graph for programming a network. Theano's symbolic API supports looping control, so-called [scan](http://deeplearning.net/software/theano/tutorial/loop.html), which makes implementing RNNs easy and efficient. Users don't always have to define a new model at the tensor operations level. There are a few higher-level frameworks, mentioned above, which make model definition and training simpler.

### [](https://github.com/zer0n/deepframeworks/#torch-)Torch [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

**State-of-the-art models**

- Excellent for conv nets. It's worth noting that temporal convolution can be done in TensorFlow/Theano via `conv2d` but that's a trick. The native interface for temporal convolution in Torch makes it slightly more intuitive to use.
- Rich set of RNNs available through a [non-official extension](https://github.com/Element-Research/rnn) [2]

**New models.** In Torch, there are multiple ways (stack of layers or graph of layers) to define a network but essentially, a network is defined as a graph of layers. Because of this coarser granularity, Torch is sometimes considered less flexible because for new layer types, users have to implement the full forward, backward, and gradient input update.

However, unlike Caffe, defining a new layer in Torch is much easier because you don't have to program in C++. Plus, in Torch, the difference between new layer definition and network definition is minimal. In Caffe, layers are defined in C++ while networks are defined via `Protobuf`.

Hence, in my opinion, Torch is as flexible as Theano.

[![](http://i.snag.gy/0loNv.jpg)](https://camo.githubusercontent.com/8dfce50523935a928019352342dc649494cafb8c/687474703a2f2f692e736e61672e67792f306c6f4e762e6a7067) [![](https://camo.githubusercontent.com/49ac7d0f42e99d979c80a10d0ffd125f4b3df0ea/68747470733a2f2f7261772e6769746875622e636f6d2f6b6f7261796b762f746f7263682d6e6e67726170682f6d61737465722f646f632f6d6c70335f666f72776172642e706e67)](https://camo.githubusercontent.com/49ac7d0f42e99d979c80a10d0ffd125f4b3df0ea/68747470733a2f2f7261772e6769746875622e636f6d2f6b6f7261796b762f746f7263682d6e6e67726170682f6d61737465722f646f632f6d6c70335f666f72776172642e706e67)<br>
_Left: graph model of CNTK/Theano/TensorFlow; Right: graph model of Caffe/Torch_

## [](https://github.com/zer0n/deepframeworks/#interfaces)Interfaces

### [](https://github.com/zer0n/deepframeworks/#caffe--1)Caffe [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

Caffe has `pycaffe` interface but that's a mere secondary alternative to the command line interface. The model has to be defined in protobuf (usually with a plain text editor), even if you use `pycaffe`.

### [](https://github.com/zer0n/deepframeworks/#cntk-)CNTK [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_2_and_a_half_stars.png)](https://camo.githubusercontent.com/7e2662e3157af40e8b118f0e0e9ed0faf214d218/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f325f616e645f615f68616c665f73746172732e706e67)

The way to use CNTK, similar to Caffe, is to specify a config file and run command line. CNTK is slightly worse than Caffe because there's no Python or any other high-level language interface.

### [](https://github.com/zer0n/deepframeworks/#tensorflow--1)TensorFlow [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_and_a_half_stars.png)](https://camo.githubusercontent.com/4f9b74f73d5b83b604e7a6c3f778a0a0c5860b96/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f616e645f615f68616c665f73746172732e706e67)

TF supports two interfaces: Python and C++. This means that you can do experiments in a rich, high-level environment and deploy your model in an environment that requires native code or low latency.

It would be perfect if TF supports `F#` or `TypeScript`. The lack of static type in Python is just ... painful :).

### [](https://github.com/zer0n/deepframeworks/#theano--1)Theano [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_stars.png)](https://camo.githubusercontent.com/4f8f2790b8f2abab48a64329fd5fc2b70cf63185/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f73746172732e706e67)

Python

### [](https://github.com/zer0n/deepframeworks/#torch--1)Torch [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_stars.png)](https://camo.githubusercontent.com/4f8f2790b8f2abab48a64329fd5fc2b70cf63185/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f73746172732e706e67)

Torch runs on LuaJIT, which is amazingly fast (comparable with industrial languages such as C++/C#/Java). Hence developers don't have to think about symbolic programming, which can be limited. They can just write all kinds of computations without worrying about performance penalty.

However, let's face it, Lua is not yet a mainstream language.

## [](https://github.com/zer0n/deepframeworks/#model-deployment)Model Deployment

How easy to deploy a new model?

### [](https://github.com/zer0n/deepframeworks/#caffe--2)Caffe [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

Caffe is C++ based, which can be compiled on a variety of devices. It is cross-platform (windows port is available and maintained [here](https://github.com/MSRDL/caffe)). Which makes Caffe the best choice with respect deployment.

### [](https://github.com/zer0n/deepframeworks/#cntk--1)CNTK [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_and_a_half_stars.png)](https://camo.githubusercontent.com/4f9b74f73d5b83b604e7a6c3f778a0a0c5860b96/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f616e645f615f68616c665f73746172732e706e67)

Like Caffe, CNTK is also C++ based and is cross-platform. Hence, deployment should be easy in most cases. However, to my understanding, it doesn't work on ARM architecture, which limits its its capability on mobile devices.

### [](https://github.com/zer0n/deepframeworks/#tensorflow--2)TensorFlow [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_4_and_a_half_stars.png)](https://camo.githubusercontent.com/4f9b74f73d5b83b604e7a6c3f778a0a0c5860b96/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f345f616e645f615f68616c665f73746172732e706e67)

TF supports C++ interface and the library can be compiled/optimized on ARM architectures because it uses [Eigen](https://github.com/zer0n/deepframeworks/blob/master/eigen.tuxfamily.org) (instead of a BLAS library). This means that you can deploy your trained models on a variety of devices (servers or mobile devices) without having to implement a separate model decoder or load Python/LuaJIT interpreter [3].

TF doesn't work on Windows yet so TF models can't be deployed on Windows devices though.

### [](https://github.com/zer0n/deepframeworks/#theano--2)Theano [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

The lack of low-level interface and the inefficiency of Python interpreter makes Theano less attractive for industrial users. For a large model, the overhead of Python isn't too bad but the dogma is still there.

The cross-platform nature (mentioned below) enables a Theano model to be deployed in a Windows environment. Which helps it gain some points.

### [](https://github.com/zer0n/deepframeworks/#torch--2)Torch [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

Torch require LuaJIT to run models. This makes it less attractive than bare bone C++ support of Caffe/CNTK/TF. It's not just the performance overhead, which is minimal. The bigger problem is integration, at API level, with a larger production pipeline.

## [](https://github.com/zer0n/deepframeworks/#performance)Performance

### [](https://github.com/zer0n/deepframeworks/#single-gpu)Single-GPU

All of these toolkits call cuDNN so as long as there's no major computations or memory allocations at the outer level, they should perform similarly.

Soumith@FB has done some [benchmarking for ConvNets](https://github.com/soumith/convnet-benchmarks). Deep Learning is not just about feedforward convnets, not just about ImageNet, and certainly not just about a few passes over the network. However, Soumith's benchmark is the only notable one as of today. So we will base the Single-GPU performance rating based on his benchmark.

#### [](https://github.com/zer0n/deepframeworks/#caffe--3)Caffe [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

For what it can do, Caffe is simply fast.

#### [](https://github.com/zer0n/deepframeworks/#cntk--2)CNTK [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

For what it can do, CNTK is simply fast.

#### [](https://github.com/zer0n/deepframeworks/#tensorflow--3)TensorFlow [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

TF only uses cuDNN v2 and even so, its performance is ~1.5x slower than Torch with cuDNN v2\. It also runs out of memory when training GoogleNet with batch size 128\. More details [here](https://github.com/soumith/convnet-benchmarks/issues/66).

A few issues have been identified in that thread: excessive memory allocation, different tensor layout from cuDNN's, no in-place op, etc.

#### [](https://github.com/zer0n/deepframeworks/#theano--3)Theano [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

On big networks, Theano's performance is on par with Torch7, according to [this benchmark](http://arxiv.org/pdf/1211.5590v1.pdf). The main issue of Theano is startup time, which is terrible, because Theano has to compile C/CUDA code to binary. We don't always train big models. In fact, DL researchers often spend more time debugging than training big models. TensorFlow doesn't have this problem. It simply maps the symbolic tensor operations to the already-compiled corresponding function calls.

Even `import theano` takes time because this `import` apparently does a lot of stuffs. Also, after `import Theano`, you are stuck with a pre-configured device (e.g. `GPU0`).

#### [](https://github.com/zer0n/deepframeworks/#torch--3)Torch [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

Simply awesome without the *bugs* that TensorFlow and Theano have.

### [](https://github.com/zer0n/deepframeworks/#multi-gpu)Multi-GPU

According to a recent benchmarking study by the CNTK team (see figure below), CNTK simply leads the pack in the multi-GPU and distributed deep learning regime. CNTK implements [1-bit SGD and adaptive minibatching in Seide et al.](http://research.microsoft.com/apps/pubs/?id=230137), which are truly gems for distributed training. This capability is unique among all deep learning toolkits.

[![](https://camo.githubusercontent.com/d77e9775f8e312d01dfda12ac82489fb51743ef5/687474703a2f2f646f776e6c6f61642d636f6465706c65782e7365632e732d6d7366742e636f6d2f446f776e6c6f61643f50726f6a6563744e616d653d636e746b26446f776e6c6f616449643d31353236313636)](https://camo.githubusercontent.com/d77e9775f8e312d01dfda12ac82489fb51743ef5/687474703a2f2f646f776e6c6f61642d636f6465706c65782e7365632e732d6d7366742e636f6d2f446f776e6c6f61643f50726f6a6563744e616d653d636e746b26446f776e6c6f616449643d31353236313636)<br>
_Source: <https://cntk.codeplex.com>_ **Disclaimer**: per CNTK team's request, I helped providing [Torch](https://gist.github.com/zer0n/2f87060a054c09999812) and [Theano (via Keras)](https://gist.github.com/zer0n/2f87060a054c09999812) measurements.

## [](https://github.com/zer0n/deepframeworks/#architecture)Architecture

Developer Zone

### [](https://github.com/zer0n/deepframeworks/#caffe--4)Caffe [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

Caffe's architecture was considered excellent when it was born but in the modern standard, it is considered average. The main pain points of Caffe are its layer-wise design in C++ and the protobuf interface for model definition.

**Layer-wise design.** The building block of a network in Caffe is layer.

- For new layer types, you have to define the full forward, backward, and gradient update. You can see an already [long-list of layers implemented in (official) caffe](https://github.com/BVLC/caffe/tree/master/src/caffe/layers).
- What's worse is that if you want to support both CPU and GPU, you need to implement extra functions, e.g. [`Forward_gpu` and `Backward_gpu`](https://github.com/BVLC/caffe/blob/master/src/caffe/layers/cudnn_conv_layer.cu).
- Worse, you need to assign an int id to your layer type and add that to the [proto file](https://github.com/BVLC/caffe/blob/master/src/caffe/proto/caffe.proto#L1046). If your pull request is not merged early, you may need to change the id because someone else already claims that.

**Protobuf.** Caffe has `pycaffe` interface but that's a mere replacement of the command line interface. The model has to be defined in protobuf (usually with a plain text editor), even if you use `pycaffe`.

[_Copied from [my own answer on Quora](https://www.quora.com/How-is-TensorFlow-architected-differently-from-Caffe)_]

### [](https://github.com/zer0n/deepframeworks/#cntk-1)CNTK

To be updated ...

#### [](https://github.com/zer0n/deepframeworks/#tensorflow--4)TensorFlow [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

TF has a clean, modular architecture with multiple frontends and execution platforms. Details are in the [white paper](http://download.tensorflow.org/paper/whitepaper2015.pdf).

[![](http://i.snag.gy/sJlZe.jpg)](https://camo.githubusercontent.com/7e6ab40dc873dae2b0bdde0a89ab61b92f307f07/687474703a2f2f692e736e61672e67792f734a6c5a652e6a7067)

#### [](https://github.com/zer0n/deepframeworks/#theano--4)Theano [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_3_stars.png)](https://camo.githubusercontent.com/d0eb347e927a07a5d5b153e2da782a09038c2007/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f335f73746172732e706e67)

The architecture is fairly hacky: the whole code base is Python where C/CUDA code is packaged as Python string. This makes it hard to navigate, debug, refactor, and hence contribute as developers.

#### [](https://github.com/zer0n/deepframeworks/#torch--4)Torch [![](http://www.wpclipart.com/signs_symbol/stars/5_star_rating_system/.cache/5_Star_Rating_System_5_stars.png)](https://camo.githubusercontent.com/407fe396c0c76a0e4e77a4b2efa8acca55cf4c77/687474703a2f2f7777772e7770636c69706172742e636f6d2f7369676e735f73796d626f6c2f73746172732f355f737461725f726174696e675f73797374656d2f2e63616368652f355f537461725f526174696e675f53797374656d5f355f73746172732e706e67)

Torch7 and nn libraries are also well-designed with clean, modular interfaces.

## [](https://github.com/zer0n/deepframeworks/#ecosystem)Ecosystem

- Caffe and CNTK: C++
- TensorFlow: Python and C++
- Theano: Python
- Torch: Lua is not a mainstream language and hence libraries built for it are not as rich as ones built for Python.

## [](https://github.com/zer0n/deepframeworks/#cross-platform)Cross-platform

Caffe, CNTK, and Theano work on all OSes. TensorFlow and Torch do not work on Windows and there's no known plan to port from either camp.

**Footnotes**

[1] Note that I don't aggregate ratings because different users/developers have different priorities.

[2] Disclaimer: I haven't analyzed this extension carefully.

[3] See my [blog post](http://www.kentran.net/2014/12/challenges-in-machine-learning-practice.html) for why this is desirable.
