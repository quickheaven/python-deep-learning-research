# Building Deep Learning Applications with Keras
**Keras** - a high level framework for building neural network
Keras build the backend using TensorFlow or Theano
Keras is a front-end layer \
**Theano** - Created at MILA (Montreal Institute for Learning Algorithms) at the University of Montreal. \
**TensorFlow** - Created at Google.

**TensorFlow Alone** - low level, more control, write more code
* Researching new types of machine learning models
* Building a large-scale system to support many users
* processing and memory efficiency
	
**Keras + TensorFlow** - High levelm Fast experimentation, less code
* Education and experimentation
* Prototyping

## Creating a Neural Network in Keras
### The train-test-evaluation flow
#### Creating Neural Network in Keras
**Supervised Learning** - the process to follow called the model, train test evaluation low. \
Step 1: Choose Model \
Step 2: Training Phase \
Step 3: Testing Phase - load second set of data that never seen by the model. \
Step 4: Evaluation Phase

#### Create a Model object
```
# the model object represents the neural network we are building
model = keras.models.Sequential() 

# we can add layers to NN just by calling model.add and passing the type of layers we want to add
models.add(keras.layers.Dense()) 
#.... add more layers ....

# the final steps in defining the model is to compile it.
# thats when Keras actually builds a tensor flow model
# how to measure accuracy (loss function), which optimizer algorithms
model.compile(loss='mean_squared_error', optimizer='adam')
	
# training the data.
model.fit(training_ata, expected_output)
	
# test phase
error_rate = model.evaluate(testing_data, expected_output)
	
# if we are happy of the accuracy
model.save("trained_model.h5")
	
# evaluation phase.
model = keras.models.load_model('trained_model.h5")
	
predictions = model.predict(new_data)
```


#### Keras Sequential API

A neural network is a ML algorith that made up individual nodes called neurons
This nodes / neuros are arrange in groups called layers
	
**When designing the NN:**
* How many layers should be?
* How many nodes should be in each layer
* How the layers are connected to each other


**Keras Sequential Model API:**
Easiest way to build a NN in Keras
	
Its called Sequential Model because you create an empty model object and then you add layers to it one after another in sequence.

```
model = keras.models.Sequential()

# We are adding Densely connected layer of 32 node to the NN. A densely connected layer is one where every mode,
# input_dim - need to define for the very first layer. 
model.add(Dense(32, input_dim=9) 
model.add(Dense(128))
model.add(Dense(1))
```

**Customizing Layers**
Before values flow from nodes in one layer to the next, they pass through an activation function \
Keras lets us choose which activation function is used for each layer by passing in the name of the activation function \
relu - rectified linear unit 
 
```
model.add(Dense(number_of_neurons, activation="relu"))
```
_(The default settings are good start)_
	
#### Other Types of Layers Supported
**Convolutional layers**
Typically used to process images and special data
Example: 
```
keras.layers.convolutional.Conv2D()
```
	
**Recurrent layers**
Special layers that have a memory built into each neuron.
Previous data points are important understanding the next data point.
Example: 
``` 
keras.layers.recurrent.LSTM()
```

builds the model defined in TensorFlow backend.
optimizer algo is the algo used to train your neural network
loss function measures how right or how wrong your NN predictions

``` model.compile(optimizer='adam', loss='mse') ```
	

	
### Training Models	

#### Training and evaluating the model	
Tell Keras how many training passes we want it to do over the training data during the training process. 	
A single training pass across the training data set is called an epoch.

If we do too few passes, the neural network wont make accurate predictions, but if we do too many it will waste time, and it might also cause over fitting problems.

The best way to tune this is to try training the neural network and stop doing additional training passes when the network stops getting more accurate.
	
Shuffle the training data randomly. Neural network typicall train best when the data is shuffled. So we'll pass in shuffle equals to true.

Verbose=2 - this simply tells Keras to print more detailed information during training so we can watch what's going on.
```
model.fit(X, Y, epochs=50, shuffle=True, verbose=2)
```

To measure the error rate of the testing data, we'll model.evaluate
```
test_error_rate = model.evaluate(X_test, Y_test, verbose=0)	
```

#### Making predictions

```	
# Make a prediction with the neural network
prediction = model.predict(X)

# Grab just the first element of the first prediction (since that's the only have one)
prediction = prediction[0][0]

# Re-scale the data from the 0-to-1 range back to dollars
# These constants are from when the data was originally scaled down to the 0-to-1 range
prediction = prediction + 0.1159
prediction = prediction / 0.0000036968
```	



#### Saving and loading models	
To save the Keras model, we call model.save	and pass the file name. 
\
When we save the model, it save both the structure of the neural network and the trained weights that determine how the neural network works.
\
The reason we use the h5 extension is because data will be stored in the HDF Five format. \
HDF Five format is a binary file format designed for storing Python array data. \
The convention is to use h5 as the filename extension but it's not required. 

```
# Save the model to disk
model.save("trained_model.h5")
print("Model saved to disk.")
```

```
from keras.models import load_model

model = load_model("trained_model.h5")
```

### Pre-Trained Models in Keras
#### Pre-trained models


#### Recognized images with RestNet50 model
ImageNet
A dataset of millions of labelled pictures
Used to train image recognition models

ILSVR - ImageNet Large Scale Visual Recognition Challenge
Yearly image recognition competition

There are four types of pre-trained image recognition models included with Keras
* VGG (Visual Geometry Group at University of Oxford) - VGG is a Deep Neural network, with 16 or 19 layers. State of art from 2014 and still widely used today, but and takes a lot of memory to run.
* ResNet50 (Microsoft Research) - State of the from 2015. Its a 50-layer neural network that manages to be more accurate with less memory but still use less memory that the VGG design.
* Inception-v3 (Google) - is another design from 2015 that also performs very well.
* Xception (Francois Chollet, author or Keras) - Xception, is an improve version of Incention-v3. More accurate than v3 while using the same amount of memory.

```
import numpy as np
from keras.preprocessing import image
from keras.applications import resnet50

# Load Keras' ResNet50 model that was pre-trained against the ImageNet database
model = resnet50.ResNet50()

# Load the image file, resizing it to 224x224 pixels (required by this model)
img = image.load_img("bay.jpg", target_size=(224, 224))

# Convert the image to a numpy array
x = image.img_to_array(img)

# Add a forth dimension since Keras expects a list of images
x = np.expand_dims(x, axis=0)

# Scale the input image to the range used in the trained network
x = resnet50.preprocess_input(x)

# Run the image through the deep neural network to make a prediction
predictions = model.predict(x)

# Look up the names of the predicted classes. Index zero is the results for the first image.
predicted_classes = resnet50.decode_predictions(predictions, top=9)

print("This is an image of:")

for imagenet_id, name, likelihood in predicted_classes[0]:
    print(" - {}: {:2f} likelihood".format(name, likelihood))
```

### Monitoring a Keras model with TensorBoard

#### Export Keras logs in TensorFlow format
```
# Define the model
model = Sequential()
model.add(Dense(50, input_dim=9, activation='relu', name='layer_1'))
model.add(Dense(100, activation='relu', name='layer_2'))
model.add(Dense(50, activation='relu', name='layer_3'))
model.add(Dense(1, activation='linear', name='output_layer'))
model.compile(loss='mean_squared_error', optimizer='adam')

# Create a TensorBoard logger
logger = keras.callbacks.TensorBoard(log_dir="logs", write_graph=True, histogram_freq=5)

# Train the model
model.fit(
    X,
    Y,
    epochs=50,
    shuffle=True,
    verbose=2,
    callbacks=[logger]
)
```

#### Visualize the computational graph
```
tensorboard --loadir=06\logs
```

#### Visualize training progress

```
RUN_NAME = "run 1 with 50 nodes"

# Create a TensorBoard logger
logger = keras.callbacks.TensorBoard(
    log_dir='logs {}'.format(RUN_NAME),
    histogram_freq=5,
    write_graph=True
)
```	


# Deep Learning: Model Optimization and Tuning

## Introduction to Deep Learning Optimization

#### What is an Artificial Neural Network(ANN)?
* Artificial Neural Network is a network of perceptrons modeled after the human brain
* Percentrons are called *nodes* in the neural network
* Nodes are organized into layers
* A deep neural neural network usually have 3 or more layers
* Each nodes has its own weights, biases, and activation functions
* Each node is connected to all nodes in the next year forming a Dense network

#### ANN Model
**Parameters**
* Weights
* Biases

**Hyperparameters**
* Number of layers, nodes in each layer, activation functions
* Cost functions, learning rate, optimizers
* Batch size, epoch

#### Optimizing for Inference
**Better accuracy**
* Higher model metrics (like F1)
* Avoid variance and biases

**Lower costs**
* Smaller model sizes
* Minimal inference latency
* Lower CPU, memory, and disk requirements

*Balance accuracy and cost
Note requirements for better accuracy and lower cost conflict each other.
Better accuracy would usually mean higher cost. So a balance needs to be achieved.*

#### Optimizing for Training
**Time to train**
* Lower iterations and experiments

**Avoid training pitfalls**
* Vanishing gradients
* Exploding gradients
* Overfitting

#### The Tuning process
* Preparing for Tuning
* Set clear goals (Accuracy vs. efficiency)
* Select and prepare training data

#### Tuning Layers
**Network architecture**
* Layers, nodes, weights
* Activation functions

**Training Parameters**
* Epochs and batches
* Normalization and regularization
* Optimizers

#### Tuning Experiments
Choose one lever at a time
* Use your understanding and experience to determime the set of values to experiment with
* Run experiments with the same data / environment and log results
* Compare and select the best option /values
Combine multiple levers and select best options \
Validate with multiple independent datasets for stability

#### Things to Remember
* No one size fits all
* The best value for a hyper parameter depends on your specific use case and input data
* Experiment, experiment, experiment

## Tuning the Deep Learning Network
### Epoch and batch size tuning

#### Batch Size
**A set of samples sent through ANN in a single pass**
**Higher the batch size**
* Better GPU utilization
* Lower number of training iterations
* Instability during training progress
* **Recommendation**
  Experiment for the right size, batch size of 32 found most optimal for most use cases.


#### Epochs
* The number of times the entire training set is passed through the network
* Controls only training, not inference
* As epochs increase, the gains would taper off as the model gains accuracy. An increase in accuracy can trigger instability beyond certain point.
* **Recommendation**
  Choose the earliest value when accuracy stabilizers.

#### Recommendation is to figure out the right number of batches and epochs first, and then use that for further experimentation.


### Epoch and batch size experiment

* Looking on the Epochs, we see that for lower Epochs the accuracy starts at low value, but increase and stabilizes after a few Epochs. The value which the stabilization happens will vary based on the specific use case. 
* Batch size, for lower batch size the accuracy starts with at a much higher range. This is because there is more iterations within the Epoch for smaller Batch sizes. The accuracy stabilizes as the Epochs progress.* 

```
model_config["EPOCH"]=20
model_config["BATCH_SIZE"] =  batch_size
```
 
### Hidden layers tuning
One of the key model architecture hyperparameters is the number of hidden layers.
As the number of layers increases, it increases the possibility of learning complex relationships between features and target variables, but it will also increase the cost and time needed for both training and inference.  It is also has the risk of overfitting the training set.

**Recommendation**
* A value of two has been sufficient for simple problems.
* It is recommended to increase the number of layers only based on experimentation if the set accuracy levels are not achieved. Otherwise, additional layers will take resources and time without providing any additional value.
*Also look for stability using plot when comparing the number of hidden layers.*

### Determining nodes in a layers
**More Nodes**
* Possibility to learn complex relationships
* More training and inference cost and time
* Overfitting training set

**Recommendation**
* Between number of input and output nodes
* Increase based on experimentation. Start with a low value like 32 and increase based on experimentation.
As the number of nodes increase, there is a general tendency to increase in accuracy owing to more patterns being captured by additional nodes.
*(Choose the number of nodes with high accuracy)*


### Choosing activation functions
Activation functions are useful for both hidden layers and output layers.
The activation function for hidden layers depends upon the choice of problem and network architecture. 
The choice can also impact the stability and speed of gradient descent.

**Recommendation**
* ReLU (Rectified Linear Unit) works best for ANN and CNN;
* sigmoid for RNN.

**Activation: Output Layer**
The activation function choice depends upon the type of machine learning problem. 
* Binary classification - sigmoid
* Multi-class classification - softmax
* Regression - linear
In model summary, the changing of activation functions do not change the number of parameters. It actually impacts the values that are created inside them.

*(Choose activation function with high accuracy)*

### Initialize Weights
The initial values of weights play a huge role in the speed of learning and finaly accuracy.
Multiple initialization techniques exist.
| Initialization Technique | Initialization To |
| --- | --- |
| Random Normal  | Random values from a standard normal distribution |
| Zeros | All zeros |
| Ones | All ones |
| Random uniform | Random uniform distribution |

**Recommendation:** Random normal works best for most cases
*(Choose weight with high accuracy)*

## Tuning Back Propagation
### Vanishing and exploding gradients
The deltas computed should be of the right size for gradual descent.

**Delta is Too smaller**
* Decay, no changes in weight. (vanishing gradient)

**Delta is Too big**
* Significant change in weight. Choppy learning with no descent (exploding gradients)

**Solutions**
* Weight initialization
* Activation functions
* Batch Normalization

### Batch Normalization
* Normalize the inputs before each hidden layers
* Center and scale (StandardScaler)
* Normalizes the inputs to be of the same scale
* Helps attain higher accuracies with lower epochs
* Additional computations and increased inference times

Add batch normalization as a layer between hidden layers.

```models.add(keras.layers.BatchNormalization())```

### Optimizers

Help speed up the training process
Introduced during back propagation, it will adjust the values each step, such as that it prevents vanishing and exploding descent while inching the parameter adjustments closer to desired states.

### Learning Rate
Learning rate is the rate at which the weights will change in response to the estimated error.
Works in conjunction with the optimizer

#### Learning Rate Selection
**Larger value**
* Faster learning with fewer epoch. - It will adjust the values too fast.
* Risk of exploding gradients. - The big adjustment to the weights, will cause the gradient to be choppy

**Lower value**
* Slower but steady learning
* Risk of vanishing gradients

### Learning Rate Experiment


## Overfitting Management

A model will perform with very high accuracy when predicting against the training samples but when predicting against with independent data samples, the accuracy levels would be low.

Neural Network have a tendency to overfit when the models are too deep and have more than the required number of nodes.

### Overfitting in ANN

#### Overfitting: Solutions
* Simple models - reduce layers and nodes in a layer
* Smaller epochs and batches during training
* Increase training data size and variety
* Balance Data
* Regularization
* Dropouts

### Regularization
* Controls overfitting during model training
* An adjustment to the model parameters to prevent them from overfitting.
* Reduces variance in the model
* Multiple options available L1, L2 and (L1 and L2 combined)

### Dropouts
* Dropouts work during forward propagation
* A given percent of modes are dropped randomly
* Dropping nodes randomly tends to result in focusing on feature values that have high influence on the outcomes.
* Dropouts can also negatively impact the model if it drop relevant nodes that model important features.
* Try dropouts if there is symptoms of over fitting.

	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
	
