https://mp.weixin.qq.com/s?__biz=MzI3MTA0MTk1MA==&mid=2652028993&idx=6&sn=042f3aee6c12c8917b6084e5fe64b176&chksm=f121b4b0c6563da63edeb1827bfaee11a7a07e1445892e56cbd6b56a805427c34614872a16a4&mpshare=1&scene=1&srcid=1018THC88JfeiiDjnPIhrSHl#rd

今天，DeepMind开源了一个内部强化学习库TRFL，用于在TensorFlow中编写强化学习智能体。这个库包含DeepMind开发的大量成功的agent的关键算法组件，对于强化学习智能体的再现研究很有用。
# TRFL

TRFL (pronounced "truffle") is a library built on top of TensorFlow that exposes
several useful building blocks for implementing Reinforcement Learning agents.


## Installation

TRFL can be installed from pip directly from github, with the following command:
`pip install git+git://github.com/deepmind/trfl.git`

TRFL will work with both the CPU and GPU version of tensorflow, but to allow
for that it does not list Tensorflow as a requirement, so you need to install
Tensorflow and Tensorflow-probability separately if you haven't already done so.

## Usage Example

```python
import tensorflow as tf
import trfl

# Q-values for the previous and next timesteps, shape [batch_size, num_actions].
q_tm1 = tf.constant([[1, 1, 0], [1, 2, 0]], dtype=tf.float32)
q_t = tf.constant([[0, 1, 0], [1, 2, 0]], dtype=tf.float32)

# Action indices, pcontinue and rewards, shape [batch_size].
a_tm1 = tf.constant([0, 1], dtype=tf.int32)
pcont_t = tf.constant([0, 1], dtype=tf.float32)
r_t = tf.constant([1, 1], dtype=tf.float32)

loss, q_learning = trfl.qlearning(q_tm1, a_tm1, r_t, pcont_t, q_t)
```

`loss` is the tensor representing the loss. For Q-learning, it is half the
squared difference between the predicted Q-values and the TD targets, shape
`[batch_size]`.

Extra information is in the `q_learning` namedtuple, including
`q_learning.td_error` and `q_learning.target`.

Most of the time, you may only be interested in the loss:

```python
loss, _ = trfl.qlearning(q_tm1, a_tm1, r_t, pcont_t, q_t)

# You can also do this, which returns the identical `loss` tensor:
loss = trfl.qlearning(q_tm1, a_tm1, r_t, pcont_t, q_t).loss

reduced_loss = tf.reduce_mean(loss)

optimizer = tf.train.AdamOptimizer(learning_rate=0.1)
train_op = optimizer.minimize(reduced_loss)
```

All loss functions in this module return the loss tensor and extra information
using the above convention.

Different functions may have different extra fields. Check the documentation of
each function for more information.

# Documentation

Check out the full documentation page
[here](https://github.com/deepmind/trfl/blob/master/docs/index.md).
