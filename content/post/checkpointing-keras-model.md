---
title: Saving a Keras model to persistent storage
subtitle: A tutorial on how to checkpoint a keras model
date: 2019-06-24
# bigimg: [{src: "/img/duskplayer.png", desc: "Screenshot"}]
tags: [ "Keras", "Python", "neural network", "deep learning", "colab" ]
---

Learn how to save Keras models to persistent storage or your Google drive and resume training it from where you left off.

<!--more-->

# Introduction

A lot of the time deep learning models can take several hours, days or weeks to train and if the machine that it's running on shuts down unexpectedly before training is finished, it can lead to all that work going to waste. This introduces the need for a way to save and load models so that training can be continued from a certain checkpoint.

Luckily Keras has got us covered with the [`ModelCheckpoint`](https://keras.io/callbacks/#modelcheckpoint) callback class.

# Saving a model

Lets say you have a simple neural network

{{< highlight python >}}
from keras import Sequential
from keras.layers import Dense

classifier = Sequential()
classifier.add(Dense(10, activation='relu', input_dim=56))
classifier.add(Dense(10, activation='relu'))
classifier.add(Dense(1, activation='sigmoid'))

classifier.compile(optimizer ='adam',
                loss='binary_crossentropy', metrics =['accuracy'])
{{< / highlight >}}

Before fitting the model, create a ModelCheckpoint object, we'll go over what each of the parameters do in a minute, but for now, in this example after each epoch the model will be saved to a `hdf5` file in the current working directory.

{{< highlight python >}}
from keras.callbacks import ModelCheckpoint

filepath="./weights-{epoch:02d}-{val_acc:.3f}.hdf5"
checkpoint = ModelCheckpoint(filepath, monitor='val_acc',
                               verbose=1, mode='max')

callbacks_list = [checkpoint]
{{< / highlight >}}

Now pass the callback list while fitting the model

{{< highlight python >}}

classifier.fit(X_train, Y_train, batch_size=100, epochs=20,
                           validation_data=(X_validation, Y_validation),
                          callbacks=callbacks_list)

{{< / highlight >}}

Once the model finishes running, you'll be able to see 20 `hdf5` files labeled by their epoch number and validation accuracy in your working directory.

# Parameters
There are several parameters that can be passed to ModelCheckpoint, we'll go over them here

- **filepath**: a string, the path where you want to save the model file.
- **monitor**: quantity to monitor. Eg: val_acc, acc, val_loss, loss, etc.
- **verbose**: verbosity mode, 0 or 1.
- **save_best_only**: if it's True, then the model will only be saved if the new model improves over the last saved model in the monitored quantity.
- **save_weights_only**: if True, then only the model's weights will be saved, else the full model is saved (including the optimizer state).
- **mode**: one of {auto, min, max}. If =True, the decision to overwrite the current save file is made based on either the maximization or the minimization of the monitored quantity. For val_acc, this should be max, for val_loss this should be min, etc. In auto mode, the direction is automatically inferred from the name of the monitored quantity.
- **period**: number of epochs between checkpoints.


# Save model to Google Drive on Colab notebooks

Google's Colab notebooks are a great way to start prototyping and creating neural networks and machine learning models. If you choose a GPU runtime, you are given a free GPU that can greatly reduce your model training time.

As great is Colab is, it does have some caveats

1. If you are disconnected from the internet or close the window for more than 90 minutes, the runtime automatically shuts down or gets recycled.
* You are given a maximum of 12 hours at a time on the VM instance.

In both these cases you may be able to reconnect to the instance but all your local variables will be lost.

Saving the model to Google Drive after every epoch makes it easy to just restart training from the last epoch that was saved.

Google makes it super easy to do this on Colab. First we have to allow Colab to access Google Drive.

{{< highlight python >}}
from google.colab import drive
drive.mount('/content/gdrive')
{{< / highlight >}}

When you run this, it generates a link, click on it. Select the google account whose drive you want to mount. Then it takes you to a new tab that says `Google Drive File Stream wants to access your Google Account`. On clicking `allow` it generates an authorization code that you have to paste in a text box that appears bellow the code you just ran. Paste the code and hit `enter`.

On refreshing your file browser, you should see a folder called `gdrive`, that's the mounted drive folder.

To save it to your drive, the code is almost the same as the example in the previous section, but the filename should be changed, so that the model is stored in drive.

{{< highlight python >}}
# Create a folder in your drive called model before running this
filepath="/content/gdrive/My Drive/model/weights-{epoch:02d}-{val_acc:.3f}.hdf5"
{{< / highlight >}}

# Resume training from a saved model

If you saved the entire model (not just the weights) then the model can continue training from wherever it stopped.

{{< highlight python >}}
from keras.models import load_model

classifier = load_model("/content/gdrive/My Drive/model/weights-15-0.815.hdf5")

classifier.fit(X_train, Y_train, batch_size=100, epochs=20,
                           validation_data=(X_validation, Y_validation),
                          callbacks=callbacks_list,
                          initial_epoch = 15)
{{< / highlight >}}


<!-- {{< highlight python >}}
{{< / highlight >}} -->
<br>