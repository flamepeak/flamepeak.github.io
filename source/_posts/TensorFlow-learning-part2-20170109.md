---
title: TensorFlow 学习（二）
date: 2017-01-09 14:55:58
tags: [TensorFlow, ML]
categories: [人工智能]
mathjax: true
---

### Start TensorFlow InteractiveSession
TensorFlow relies on a highly efficient C++ backend to do its computation. The connection to this backend is called a session. The common usage for TensorFlow programs is to first create a graph and then launch it in a session.

Here we instead use the convenient **InteractiveSession** class, which makes TensorFlow more flexible about how you structure your code. It allows you to interleave operations which build a **computation graph** with ones that run the graph. This is particularly convenient when working in interactive contexts like IPython. If you are not using an InteractiveSession, then you should build the entire computation graph before starting a session and launching the graph.

```
import tensorflow as tf
sess = tf.InteractiveSession()
```
Instead of running a single expensive operation independently from Python, TensorFlow lets us describe a graph of interacting operations that run entirely outside Python. This approach is similar to that used in Theano or Torch.

The role of the Python code is therefore to build this external computation graph, and to dictate which parts of the computation graph should be run.

### Build a Softmax Regression Model
#### Placeholders
We start building the computation graph by creating nodes for the input images and target output classes.
```
x = tf.placeholder(tf.float32, shape=[None, 784])
y_ = tf.placeholder(tf.float32, shape=[None, 10])
```
Here x and y_ aren't specific values. Rather, they are each a placeholder -- a value that we'll input when we ask TensorFlow to run a computation.

The **shape** argument to **placeholder** is optional, but it allows TensorFlow to automatically catch bugs stemming from inconsistent tensor shapes.

#### Variables
We now define the weights **W** and biases **b** for our model. We could imagine treating these like additional inputs, but TensorFlow has an even better way to handle them: **Variable**. A **Variable** is a value that lives in TensorFlow's computation graph. It can be used and even modified by the computation. In machine learning applications, one generally has the model parameters(模型参数) be **Variables**.
```
W = tf.Variable(tf.zeros([784,10]))
b = tf.Variable(tf.zeros([10]))
```
Before **Variables** can be used within a session, they must be initialized using that session. This step takes the initial values (in this case tensors full of zeros) that have already been specified, and assigns them to each **Variable**. This can be done for all **Variables** at once:
```
sess.run(tf.global_variables_initializer())
```

### Predicted Class and Loss Function
We can now implement our regression model. It only takes one line! We multiply the vectorized input images **x** by the weight matrix **W**, add the bias **b**.
```
y = tf.matmul(x,W) + b
```
We can specify a loss function just as easily. Loss indicates how bad the model's prediction was on a single example; we try to minimize that while training across all the examples. Here, our loss function is the cross-entropy between the target and the softmax activation function applied to the model's prediction.
```
cross_entropy = tf.reduce_mean(tf.nn.softmax_cross_entropy_with_logits(y, y_))
```

### Train the Model
Now that we have defined our model and training loss function, it is straightforward to train using TensorFlow. Because TensorFlow knows the entire computation graph, it can use automatic differentiation(自动微分法) to find the gradients of the loss with respect to each of the variables. TensorFlow has a variety of built-in optimization algorithms. For this example, we will use steepest gradient descent, with a step length of 0.5, to descend the cross entropy.
```
train_step = tf.train.GradientDescentOptimizer(0.5).minimize(cross_entropy)
```
What TensorFlow actually did in that single line was to add new operations to the computation graph. These operations included ones to compute gradients, compute parameter update steps, and apply update steps to the parameters.

The returned operation **train_step**, when run, will apply the gradient descent(梯度下降) updates to the parameters. Training the model can therefore be accomplished by repeatedly running **train_step**.
```
for i in range(1000):
  batch = mnist.train.next_batch(100)
  train_step.run(feed_dict={x: batch[0], y_: batch[1]})
```
Note that you can replace any tensor in your computation graph using **feed_dict** -- it's not restricted to just **placeholders**.

### Evaluate the Model
First we'll figure out where we predicted the correct label. **tf.argmax** is an extremely useful function which gives you the index of the highest entry in a tensor along some axis. For example, **tf.argmax(y,1)** is the label our model thinks is most likely for each input, while **tf.argmax(y_,1)** is the true label. We can use **tf.equal** to check if our prediction matches the truth.

```
correct_prediction = tf.equal(tf.argmax(y,1), tf.argmax(y_,1))
```
That gives us a list of booleans. To determine what fraction are correct, we cast to floating point numbers and then take the mean. For example, **[True, False, True, True]** would become **[1,0,1,1]** which would become **0.75**.

```
accuracy = tf.reduce_mean(tf.cast(correct_prediction, tf.float32))
```

---

## Build a Multilayer Convolutional Network（多层卷积网络）
Getting 92% accuracy on MNIST is bad. It's almost embarrassingly bad. In this section, we'll fix that, jumping from a very simple model to something moderately sophisticated: a small convolutional neural network（卷积神经网络）.

### Weight Initialization
```
def weight_variable(shape):
  initial = tf.truncated_normal(shape, stddev=0.1)
  return tf.Variable(initial)

def bias_variable(shape):
  initial = tf.constant(0.1, shape=shape)
  return tf.Variable(initial)
```
