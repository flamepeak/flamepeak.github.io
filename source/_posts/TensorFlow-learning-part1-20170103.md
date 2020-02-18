---
title: TensorFlow 学习(一)
date: 2017-01-03 09:43:14
tags: [TensorFlow, Learning]
categories: [人工智能]
mathjax: true
---

### Basic Usage
To use TensorFlow you need to understand how TensorFlow:
+ Represents computations as graphs.
+ Executes graphs in the context of `Sessions`.
+ Represents data as tensors.
+ Maintains state with `Variables`.
+ Uses feeds and fetches to get data into and out of arbitrary operations.

#### Overview
TensorFlow is a programming system in which you represent computations as graphs. Nodes in the graph are called ops (short for operations). An op takes zero or more `Tensors`, performs some computation, and produces zero or more `Tensors`. In TensorFlow terminology, a `Tensor` is a typed multi-dimensional array. For example, you can represent a mini-batch of images as a 4-D array of floating point numbers with dimensions `[batch, height, width, channels]`.

A TensorFlow graph is a description of computations. To compute anything, a graph must be launched in a `Session`. A `Session` places the graph ops onto `Devices`, such as CPUs or GPUs, and provides methods to execute them. These methods return tensors produced by ops as `numpy` `ndarray` objects in `Python`, and as `tensorflow::Tensor` instances in C and C++.

#### The computation graph
TensorFlow programs are usually structured into a construction phase, that assembles a graph, and an execution phase that uses a session to execute ops in the graph.

For example, it is common to create a graph to represent and train a neural network in the construction phase, and then repeatedly execute a set of training ops in the graph in the execution phase.

First Example:
```Python
from tensorflow import tf

matrix1 = tf.constant([[3., 3.]])
tallies into our predicted probabilities y using the "softmax" function:
matrix2 = tf.constant([[2.],[2.]])

product = tf.matmul(matrix1, matrix2)

with tf.Session() as sess:
    result = sess.run([product])
    print (result)
```

#### Interactive Usage
```Python
import tensorflow as tf
sess = tf.InterativeSession()

x = tf.Variable([1.0, 2.0])
a = tf.constant([3.0, 3.0])

# Initialize 'x' using the run() method of its initializer op.
x.initializer.run()

sub = tf.sub(x, a)
print (sub.eval())

sess.close()
```
#### Tensor
TensorFlow programs use a tensor data structure to represent all data -- only tensors are passed between operations in the computation graph. You can think of a TensorFlow tensor as an n-dimensional array or list. A tensor has a static type, a rank, and a shape.

#### Variables
Variables maintain state across executions of the graph.
```Python
#Create a Variable, that will be initialized to the scalar value 0.
state = tf.Variable(0, name="conunter")

# Create an Op to add one to 'state'.
one = tf.constant(1)
new_value = tf.add(state, one)
update = tf.assign(state, new_value)

# Varibale must be initialized by running an 'init' Op after having
# launched the graph. We first have to add the 'init' Op to the graph.
init_op = tf.global_variables_initializer()


# Launch the graph and run the ops.
with tf.Session() as sess:
    # Run the 'init' op
    sess.run(init_op)
    # Print the initial value of 'state'
    print (sess.run(state))
    # Run the op that updates 'state' and print 'state'
    for _ in range(3):
        sess.run(update)
        print (sess.run(state))
```

#### Fetches
To fetch the outputs of operations, execute the graph with a `run()` call on the **Session** object and pass in the tensors to retrieve.
为了取回操作的输出内容, 可以在使用 Session 对象的 run() 调用 执行图时, 传入一些 tensor, 这些 tensor 会帮助你取回结果.
```Python
input1 = tf.constant([3.0])
input2 = tf.constant([2.0])
input3 = tf.constant([5.0])
intermed = tf.add(input2, input3)
mul = tf.mul(input1, intermed)

with tf.Session() as sess:
    result = sess.run([mul, intermed])
    print (result)

# output
# [array([ 21.], dtype=float32), array([ 7.], dtype=float32)]
```

#### Feeds
The examples above introduce tensors into the computation graph by storing them in **Constants** and **Variables**. TensorFlow also provides a feed mechanism for patching a tensor directly into any operation in the graph.

A feed temporarily replaces the output of an operation with a tensor value. You supply feed data as an argument to a run() call. The feed is only used for the run call to which it is passed. The most common use case involves designating specific operations to be "feed" operations by using `tf.placeholder()` to create them:
```Python
input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)
output = tf.mul(input1, input2)

with tf.Session() as sess:
  print(sess.run([output], feed_dict={input1:[7.], input2:[2.]}))

# output:
# [array([ 14.], dtype=float32)]
```


---

### MNIST for ML Beginners
MNIST is a simple computer vision dataset.

#### Softmax Regressions
If you want to assign probabilities to an object being one of several different things, softmax is the thing to do, because softmax gives us a list of values between 0 and 1 that add up to 1. Even later on, when we train more sophisticated models, the final step will be a layer of softmax.

A softmax regression has two steps: first we add up the evidence of our input being in certain classes, and then we convert that evidence into probabilities.

To tally up the evidence that a given image is in a particular class, we do a weighted sum of the pixel intensities. The weight is negative if that pixel having a high intensity is evidence against the image being in that class, and positive if it is evidence in favor.
为了得到一张给定图片属于某个特定数字类的证据(evidence),我们对图片像素值进行加权求和。如果这个像素具有很强的证据说明这张图片不属于该类,那么相应的权值为负数,相反如果这个像素拥有有利的证据支持这张图片属于这个类,那么权值是正数。

We also add some extra evidence called a bias. Basically, we want to be able to say that some things are more likely independent of the input. The result is that the evidence for a class $i$ given an input $x$ is:
$$
\text{evidence}_i = \sum_j W_{i,~ j} x_j + b_i
$$
where $W_i$ is the weights and $b_i$ is the bias for class $i$, and $j$ is an index for summing over the pixels in our input image $x$.

We then convert the evidence tallies into our predicted probabilities $y$ using the "softmax" function:
$$
y = \text{softmax}(\text{evidence})
$$
Here softmax is serving as an "activation"(激励函数) or "link"（链接函数） function, shaping the output of our linear function into the form we want.
You can think of it as converting tallies of evidence into probabilities of our input being in each class. It's defined as:
$$
\text{softmax}(x) = \text{normalize}(\exp(x))
$$
If you expand that equation out, you get:
$$
\text{softmax}(x)_i = \frac{\exp(x_i)}{\sum_j \exp(x_j)}
$$
But it's often more helpful to think of softmax the first way: exponentiating its inputs and then normalizing them. The exponentiation means that one more unit of evidence increases the weight given to any hypothesis multiplicatively. And conversely, having one less unit of evidence means that a hypothesis gets a fraction of its earlier weight. No hypothesis ever has zero or negative weight. Softmax then normalizes these weights, so that they add up to one, forming a valid probability distribution.

You can picture our softmax regression as looking something like the following, although with a lot more $x$s. For each output, we compute a weighted sum of the $x$s, add a bias, and then apply softmax.
![softmax-regression-scalargraph.png](/sourcepictures/2017/01/06/softmax-regression-scalargraph.png)

If we write that out as equations, we get:
![softmax-regression-scalarequation.png](/sourcepictures/2017/01/06/softmax-regression-scalarequation.png)

We can "vectorize" this procedure, turning it into a matrix multiplication and vector addition. This is helpful for computational efficiency.
![softmax-regression-vectorequation.png](/sourcepictures/2017/01/06/softmax-regression-vectorequation.png)

More compactly, we can just write:
$$
y = \text{softmax}(Wx + b)
$$

#### Implementing the Regression
Instead of running a single expensive operation independently from Python, TensorFlow lets us describe a graph of interacting operations that run entirely outside Python.

We describe these interacting operations by manipulating symbolic variables. Let's create one:
```
x = tf.placeholder(tf.float32, [None, 784])
```
x isn't a specific value. It's a **placeholder**, a value that we'll input when we ask TensorFlow to run a computation.

We represent this as a 2-D tensor of floating-point numbers, with a shape [**None**, 784]. (Here **None** means that a dimension can be of any length.)

A **Variable** is a modifiable tensor that lives in TensorFlow's graph of interacting operations. It can be used and even modified by the computation. For machine learning applications, one generally has the model parameters be **Variables**.
```
W = tf.Variable(tf.zeros([784, 10]))
b = tf.Variable(tf.zeros([10]))
```

We can now implement our model. It only takes one line to define it!
```
y = tf.nn.softmax(tf.matmul(x, W)+b)
```

#### Training
In order to train our model, we need to define what it means for the model to be good. Well, actually, in machine learning we typically define what it means for a model to be bad. We call this the cost, or the loss, and it represents how far off our model is from our desired outcome. We try to minimize that error, and the smaller the error margin, the better our model is.

One very common, very nice function to determine the loss of a model is called "cross-entropy." Cross-entropy arises from thinking about information compressing codes in information theory but it winds up being an important idea in lots of areas, from gambling to machine learning. It's defined as:

$$
H_{y'}(y) = -\sum_i y'_i \log(y_i)
$$

Where $y$ is our predicted probability distribution, and $y′$ is the true distribution (the one-hot vector with the digit labels). In some rough sense, the cross-entropy is measuring how inefficient our predictions are for describing the truth.


mplement cross-entropy we need to first add a new placeholder to input the correct answers:
```
y_ = tf.placeholder(tf.float32, [None, 10])
```

Then we can implement the cross-entroy function,  $-\sum(y'*\log(y))$
```
cross_entroy = tf.reduce_mean(-tf.reduce_sum(y_*tf.log(y),reduction_indices=[1]))
```

Now that we know what we want our model to do, it's very easy to have TensorFlow train it to do so. Because TensorFlow knows the entire graph of your computations, it can automatically use the backpropagation algorithm to efficiently determine how your variables affect the loss you ask it to minimize. Then it can apply your choice of optimization algorithm to modify the variables and reduce the loss.

```
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
```
Now we have our model set up to train. One last thing before we launch it, we have to create an operation to initialize the variables we created. Note that this defines the operation but does not run it yet:
```
init = tf.global_variables_initializer()
```

We can now launch the model in a Session, and now we run the operation that initializes the variables:
```
sess = tf.Session()
sess.run(init)
```
Let's train -- we'll run the training step 1000 times!
```
for i in range(1000):
  batch_xs, batch_ys = mnist.train.next_batch(100)
  sess.run(train_step, feed_dict={x: batch_xs, y_: batch_ys})
```

Each step of the loop, we get a "batch" of one hundred random data points from our training set. We run `train_step` feeding in the batches data to replace the `placeholders`.

Using small batches of random data is called stochastic training -- in this case, stochastic gradient descent.

#### Evaluating Our Model
Well, first let's figure out where we predicted the correct label. **tf.argmax** is an extremely useful function which gives you the index of the highest entry in a tensor along some axis.
For example, **tf.argmax(y,1)** is the label our model thinks is most likely for each input, while **tf.argmax(y_,1)** is the correct label. We can use tf.equal to check if our prediction matches the truth.

```
correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))
```

That gives us a list of booleans. To determine what fraction are correct, we cast to floating point numbers and then take the mean.
```
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```
Finally, we ask for our accuracy on our test data.
```
print(sess.run(accuracy, feed_dict={x: mnist.test.images, y_: mnist.test.labels}))
```
