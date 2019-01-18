---
layout: post
category: MachineLearning 
title: Tensorflow Keras API 
tagline: by Snail
tags: 
  - Tensorflow
  - Deep learning
published: false
---

# TensorFlow Keras API 

## Construct a model with keras API

An example of Sequential model.

```python
import tensorflow as tf

model = tf.keras.Sequential()

model.add(tf.keras.Layers.Dense(units=64, activation='relu'))
model.add(tf.keras.Layers.Dense(units=64, activation='relu'))
model.add(tf.keras.Layers.Dense(units=10, activation='softmax))
```

Apart from `acivation`,  there are many other parameters free to configure in `tf.keras.Layer` :

```python
import tensorflow.keras.Layers as layers

# A linear layer with L1 regularization of factor 0.01 applied to the kernel matrix:
layers.Dense(64, kernel_regularizer=tf.keras.regularizers.l1(0.01))
# A linear layer with L2 regularization of factor 0.01 applied to the bias vector:
layers.Dense(64, bias_regularizer=tf.keras.regularizers.l2(0.01))
# A linear layer with a kernel initialized to a random orthogonal matrix:
layers.Dense(64, kernel_initializer='orthogonal')
# A linear layer with a bias vector initialized to 2.0s:
layers.Dense(64, bias_initializer=tf.keras.initializers.constant(2.0))
```

## Model training and evaluation

### Setup training

After the definition of the model, we need to specify the calculation of loss, optimizer and etc.
We can do these all in once with `tf.keras.Model.compile`:

```python

# Definition of a model 
model = tf.keras.Sequential([
# Adds a densely-connected layer with 64 units to the model:
layers.Dense(64, activation='relu'),
# Add another:
layers.Dense(64, activation='relu'),
# Add a softmax layer with 10 output units:
layers.Dense(10, activation='softmax')])

# Compile the model by specifing the optimizer, loss calculation and metrics
model.compile(optimizer=tf.train.AdamOptimizer(0.001),
              loss='categorical_crossentropy',
              metrics=['accuracy'])

```

We could have more examples of model compilation. As you might notice, we define two methods in `loss` and `metrics` respectively.
The difference is that the methods defined in `metric` will not be used in the training phrase. 

```python
# Configure a model for mean-squared error regression.
model.compile(optimizer=tf.train.AdamOptimizer(0.01),
              loss='mse',       # mean squared error
              metrics=['mae'])  # mean absolute error

# Configure a model for categorical classification.
model.compile(optimizer=tf.train.RMSPropOptimizer(0.01),
              loss=tf.keras.losses.categorical_crossentropy,
              metrics=[tf.keras.metrics.categorical_accuracy])
```

### Input data 

For small data set, it's very easy to feed the data directly to the `tf.kears.Model.fit()` with `Numpy` .
Here is an example of `fit()`.

```python
import tensorflow as tf
import numpy as np 


def create_model():
    model = tf.keras.Sequential()

    model.add(tf.keras.layers.Dense(units=64, activation='relu'))
    model.add(tf.keras.layers.Dense(units=64, activation='relu'))
    model.add(tf.keras.layers.Dense(units=10, activation='softmax'))

    model.compile(
        optimizer=tf.train.AdamOptimizer(.001),
        loss='categorical_crossentropy',
        metrics=['accuracy']
    )
    return model

def generate_data(N):
    data = np.random.random((N, 32))   
    labels = np.random.random((N, 10))
    return data, labels

if __name__ == "__main__":
    X_train, y_train = generate_data(1000)
    X_eval, y_eval = generate_data(100)
    model = create_model()
    model.fit(
        X_train, y_train, epochs=10, batch_size=32,
        validation_data=(X_eval, y_eval)
    )
```

When the volume is large, `tf.Data.Dataset` would be a better choice.

```python 
# Instantiates a toy dataset instance:
dataset = tf.data.Dataset.from_tensor_slices((data, labels))
dataset = dataset.batch(32)
dataset = dataset.repeat()

# Don't forget to specify `steps_per_epoch` when calling `fit` on a dataset.
model.fit(dataset, epochs=10, steps_per_epoch=30)
```
