#DeepLearning 
# Slides

[chapter1](attachment:7ecc824d-5a88-44c9-812d-e4ee26f61bbe:chapter1-5_compressed.pdf)

[chapter2.pdf](attachment:6af6a0f5-d526-4a92-9e6f-7369a691b180:chapter2-5_compressed.pdf)

[chapter3_compressed.pdf](attachment:e4b18358-8ca7-4613-b650-aa2ae99de7dd:chapter3_compressed.pdf)

[chapter4-5_compressed.pdf](attachment:5a032ebe-a009-4f55-bce1-1b5cc7d629f8:chapter4-5_compressed.pdf)

# Notes

## Training robust neural networks

### PyTorch and object-oriented programming

#### PyTorch Dataset

Time to refresh your PyTorch Datasets knowledge!

Before model training can commence, you need to load the data and pass it to the model in the right format. In PyTorch, this is handled by Datasets and DataLoaders. Let's start with building a PyTorch Dataset for our water potability data.

In this exercise, you will define a class called `WaterDataset` to load the data from a CSV file. To do this, you will need to implement the three methods which PyTorch expects a Dataset to have:

- `.__init__()` to load the data,
- `.__len__()` to return data size,
- `.__getitem()__` to extract features and label for a single sample.

```python
class WaterDataset(Dataset):
    def __init__(self, csv_path):
        super().__init__()
        # Load data to pandas DataFrame
        df = pd.read_csv(csv_path)
        # Convert data to a NumPy array and assign to self.data
        self.data = df.to_numpy()
        
    # Implement __len__ to return the number of data samples
    def __len__(self):
        return self.data.shape[0]
    
    def __getitem__(self, idx):
        features = self.data[idx, :-1]
        # Assign last data column to label
        label = self.data[idx, -1]
        return features, label
```

#### PyTorch DataLoader

Good job defining the Dataset class! The `WaterDataset` you just created is now available for you to use.

The next step in preparing the training data is to set up a `DataLoader`. A PyTorch `DataLoader` can be created from a `Dataset` to load data, split it into batches, and perform transformations on the  
data if desired. Then, it yields a data sample ready for training.

In this exercise, you will build a `DataLoader` based on the `WaterDataset`. The `DataLoader` class you will need has already been imported for you from `torch.utils.data`. Let's get to it!

```python
# Create an instance of the WaterDataset
dataset_train = WaterDataset('water_train.csv')

# Create a DataLoader based on dataset_train
dataloader_train = DataLoader(
    dataset_train,
    batch_size=2,
    shuffle=True,
)

# Get a batch of features and labels
features, labels = next(iter(dataloader_train))
print(features, labels)
```

#### PyTorch Model

You will use the OOP approach to define the model architecture. Recall that this requires setting up a model class and defining two methods inside it:

- `.__init__()`, in which you define the layers you want to use;
- `forward()`, in which you define what happens to the  
    model inputs once it receives them; this is where you pass inputs  
    through pre-defined layers.

Let's build a model with three linear layers and ReLU activations. After the last linear layer, you need a sigmoid activation instead, which is well-suited for binary classification tasks like our water  
potability prediction problem. Here's the model defined using `nn.Sequential()`, which you may be more familiar with:

```
net = nn.Sequential(
  nn.Linear(9, 16),
  nn.ReLU(),
  nn.Linear(16, 8),
  nn.ReLU(),
  nn.Linear(8, 1),
  nn.Sigmoid(),
)
```

Let's rewrite this model as a class!

```python
import torch.nn as nn
import torch.nn.functional as F

class Net(nn.Module):
    def __init__(self):
        super().__init__()
        # Define the three linear layers
        self.fc1 = nn.Linear(9, 16)
        self.fc2 = nn.Linear(16, 8)
        self.fc3 = nn.Linear(8, 1)
        
    def forward(self, x):
        # Pass x through linear layers adding activations
        x = nn.functional.relu(self.fc1(x))
        x = nn.functional.relu(self.fc2(x))
        x = nn.functional.sigmoid(self.fc3(x))
        return x
```

### Optimizers, training and evaluation

#### Optimizers

It's time to explore the different optimizers that you can use for training your model.

A custom function called `train_model(optimizer, net, num_epochs)` has been defined for you. It takes the optimizer, the model, and the number of epochs as inputs, runs the training loops, and prints the training loss at the end.

Let's use `train_model()` to run a few short trainings with different optimizers and compare the results!

```python
import torch.optim as optim

net = Net()

# Define the Adam optimizer
optimizer = optim.Adam(net.parameters(), lr=0.001)

train_model(
    optimizer=optimizer,
    net=net,
    num_epochs=10,
)
```

#### Model evaluation

With the training loop sorted out, you have trained the model for 1000 epochs, and it is available to you as `net`. You have also set up a `test_dataloader` in exactly the same way as you did with `train_dataloader` before—just reading the data from the test rather than the train directory.

You can now evaluate the model on test data. To do this, you will need to write the evaluation loop to iterate over the batches of test data, get the model's predictions for each batch, and calculate the  
accuracy score for it. Let's do it!

```python
import torch
from torchmetrics import Accuracy

# Set up binary accuracy metric
acc = Accuracy(task = 'binary')

net.eval()
with torch.no_grad():
    for features, labels in dataloader_test:
        # Get predicted probabilities for test data batch
        outputs = net(features)
        preds = (outputs >= 0.5).float()
        acc(preds, labels.view(-1, 1))

# Compute total test accuracy
test_accuracy = acc.compute()
print(f"Test accuracy: {test_accuracy}")
```

### Vanishing and exploding gradients

#### Initialization and activation

The problems of unstable (vanishing or exploding) gradients are a challenge that often arises in training deep neural networks. In this and the following exercises, you will expand the model architecture that you built for the water potability classification task to make it more immune to those problems.

As a first step, you'll improve the weights initialization by using He (Kaiming) initialization strategy. To do so, you will need to call the proper initializer from the `torch.nn.init` module, which has been imported for you as `init`. Next, you will update the activations functions from the default ReLU to the often better ELU.

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(9, 16)
        self.fc2 = nn.Linear(16, 8)
        self.fc3 = nn.Linear(8, 1)
        
        # Apply He initialization
        init.kaiming_uniform_(self.fc1.weight)
        init.kaiming_uniform_(self.fc2.weight)
        init.kaiming_uniform_(self.fc3.weight, nonlinearity = 'sigmoid')

    def forward(self, x):
        # Update ReLU activation to ELU
        x = nn.functional.elu(self.fc1(x))
        x = nn.functional.elu(self.fc2(x))
        x = nn.functional.sigmoid(self.fc3(x))
        return x
```

#### Activations: ReLU vs. ELU

The choice of the activation functions used in the model (combined with the corresponding  
weight initialization) can have a strong impact on the training process. In particular, the proper activation can prevent the network from experiencing unstable gradients problems.

#### Batch Normalization

As a final improvement to the model architecture, let's add the batch normalization layer after each of the two linear layers. The batch norm trick tends to accelerate training convergence and protects the model from vanishing and exploding gradients issues.

Both `torch.nn` and `torch.nn.init` have already been imported for you as `nn` and `init`, respectively. Once you implement the change in the model architecture, be ready to answer a short question on how batch normalization works!

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.fc1 = nn.Linear(9, 16)
        # Add two batch normalization layers
        self.bn1 = nn.BatchNorm1d(16)
        self.fc2 = nn.Linear(16, 8)
        self.bn2 = nn.BatchNorm1d(8)
        self.fc3 = nn.Linear(8, 1)
        
        init.kaiming_uniform_(self.fc1.weight)
        init.kaiming_uniform_(self.fc2.weight)
        init.kaiming_uniform_(self.fc3.weight, nonlinearity="sigmoid")
    
    def forward(self, x):
        x = self.fc1(x)
        x = self.bn1(x)
        x = nn.functional.elu(x)

        # Pass x through the second set of layers
        x = self.fc2(x)
        x = self.bn2(x)
        x = nn.functional.elu(x)

        x = nn.functional.sigmoid(self.fc3(x))
        return x
```

## **Images & Convolutional Neural Networks**

### Handling images with PyTorch

#### Image dataset

Let's start with building a Torch Dataset of images. You'll use it to explore the data and, later, to feed it into a model.

The training data for the cloud classification task is stored in the following directory structure:

```
clouds_train
  - cirriform clouds
    - 539cd1c356e9c14749988a12fdf6c515.jpg
    - ...
  - clear sky
  - cumulonimbus clouds
  - cumulus clouds
  - high cumuliform clouds
  - stratiform clouds
  - stratocumulus clouds
```

There are seven folders inside `clouds_train`, each representing one cloud type (or a clear sky). Inside each of these folders sit corresponding image files.

```python
# Compose transformations
train_transforms = transforms.Compose([
    transforms.ToTensor(),
    transforms.Resize((128, 128))
])

# Create Dataset using ImageFolder
dataset_train = ImageFolder(
    "clouds_train",
    transform=train_transforms,
)
```

#### Data augmentation in PyTorch

Let's include data augmentation in your Dataset and inspect some images visually to make sure the desired transformations are applied.

First, you'll add the augmenting transformations to `train_transforms`. Let's use a random horizontal flip and a rotation by a random angle between 0 and 45 degrees. The code that follows to create the Dataset and the DataLoader is exactly the same as before. Finally, you'll reshape the image and display it to see if the new augmenting transformations are visible.

```python
train_transforms = transforms.Compose([
    # Add horizontal flip and rotation
    transforms.RandomHorizontalFlip(),
    transforms.RandomRotation(45),
    transforms.ToTensor(),
    transforms.Resize((128, 128)),
])

dataset_train = ImageFolder(
  "clouds_train",
  transform=train_transforms,
)

dataloader_train = DataLoader(
  dataset_train, shuffle=True, batch_size=1
)

image, label = next(iter(dataloader_train))
# Reshape the image tensor
image = image.squeeze().permute(1, 2, 0) 
# Display the image
plt.imshow(image)
plt.show()
```

### Convolutional Neural Networks

#### The convolutional layer

Convolutional layers are the basic building block of most computer vision architectures. Understanding why they're used and how they work is crucial for building image-processing neural networks.

- Convolutional layers preserve spatial information between their inputs and outputs.
- Adding zero-padding around the convolutional layer's input ensures that the pixels at the border receive as much attention as those located elsewhere in the feature map.
- Convolutional layers in general use fewer parameters than linear layers.

#### Building convolutional networks

You are on a team building a weather forecasting system. As part of the system, cameras will be installed at various locations to take pictures of the sky. Your task is to build a model to classify different cloud types in these pictures, which will help spot approaching weather  
fronts.

You decide to build a convolutional image classifier. The model will consist of two parts:

- A feature extractor that learns a vector of features from the input image,
- A classifier that predicts the image's class based on the learned features.

Both `torch` and `torch.nn as nn` have already been imported for you, so let's get to it!

```python
class Net(nn.Module):
    def __init__(self, num_classes):
        super().__init__()
        # Define feature extractor
        self.feature_extractor = nn.Sequential(
            nn.Conv2d(3, 32, kernel_size=3, padding=1),
            nn.ELU(),
            nn.MaxPool2d(kernel_size=2),
            nn.Conv2d(32, 64, kernel_size=3, padding=1),
            nn.ELU(),
            nn.MaxPool2d(kernel_size=2),
            nn.Flatten(),
        )
        # Define classifier
        self.classifier = nn.Linear(64*16*16, num_classes)
    
    def forward(self, x):  
        # Pass input through feature extractor and classifier
        x = self.feature_extractor(x)
        x = self.classifier(x)
        return x
```

#### Choosing augmentations

You are building a model to recognize different flower species. You consider three augmentations to use: color shift, rotation, and texture change. The graphic below shows the examples of these three augmentations.

![Image of a flower transformed in three ways: using rotation, color shift, and texture change.](https://assets.datacamp.com/production/repositories/6312/datasets/448674d41421320f523b5c3022f533e0148a6212/flower_classification_augmentations.png)

Only rotation

#### Dataset with augmentations

You have already built the image dataset from cloud pictures and the convolutional model to classify different cloud types. Before you train it, let's adapt the dataset by adding the augmentations that could improve the model's cloud classification performance.

The code to set up the Dataset and DataLoader is already prepared for you and should look familiar. Your task is to define the composition of transforms that will be applied to the input images as they are loaded.

Note that before you were resizing images to 128 by 128 to display them nicely, but now you will use smaller ones to speed up training. As you will see later, 64 by 64 will be large enough for the model to learn.

`from torchvision import transforms` has been already executed for you, so let's get to it!

```python
# Define transforms
train_transforms = transforms.Compose([
    transforms.RandomHorizontalFlip(),
    transforms.RandomRotation(45),
    transforms.RandomAutocontrast(),
    transforms.ToTensor(),
    transforms.Resize((64, 64)),
])

dataset_train = ImageFolder(
  "clouds_train",
  transform=train_transforms,
)
dataloader_train = DataLoader(
  dataset_train, shuffle=True, batch_size=16
)
```

#### Image classifier training loop

It's time to train the image classifier! You will use the `Net` you defined earlier and train it to distinguish between seven cloud types.

To define the loss and optimizer, you will need to use functions from `torch.nn` and `torch.optim`, imported for you as `nn` and `optim`,  
respectively. You don't need to change anything in the training loop itself: it's exactly like the ones you wrote before, with some additional logic to print the loss during training.

```python
# Define the model
net = Net(num_classes = 7)
# Define the loss function
criterion = nn.CrossEntropyLoss()
# Define the optimizer
optimizer = optim.Adam(net.parameters(), lr=0.001)

for epoch in range(3):
    running_loss = 0.0
    # Iterate over training batches
    for images, labels in dataloader_train:
        optimizer.zero_grad()
        outputs = net(images)
        loss = criterion(outputs, labels)
        loss.backward()
        optimizer.step()
        running_loss += loss.item()
    
    epoch_loss = running_loss / len(dataloader_train)
    print(f"Epoch {epoch+1}, Loss: {epoch_loss:.4f}")
```

### Evaluating image classifiers

#### Averaging multi-class metrics

```python
from torchmetrics import Recall

recall_per_class = Recall(task='multiclass', num_classes=7, average=None)
recall_micro = Recall(task='multiclass', num_classes=7, average='micro')
recall_macro = Recall(task='multiclass', num_classes=7, average='macro')
recall_weighted = Recall(task='multiclass', num_classes=7, average='weighted')
```

When to use each:

- Micro → Imbalanced datasets
- Macro → Care about performance on small clases
- Weighted → Consider errors in larger classes as more important

#### Multi-class model evaluation

Let's evaluate our cloud classifier with precision and recall to see how well it can classify the _seven_  
cloud types. In this multi-class classification task it is important how you average the scores over classes. Recall that there are four approaches:

- Not averaging, and analyzing the results per class;
- Micro-averaging, ignoring the classes and computing the metrics globally;

- Macro-averaging, computing metrics per class and averaging them;
- Weighted-averaging, just like macro but with the average weighted by class size.

Both `Precision` and `Recall` are already imported from `torchmetrics`. It's time to see how well our model is doing!

```python
# Define metrics
metric_precision = Precision(task="multiclass", num_classes=7, average="macro")
metric_recall = Recall(task="multiclass", num_classes=7, average="macro")

net.eval()
with torch.no_grad():
    for images, labels in dataloader_test:
        outputs = net(images)
        _, preds = torch.max(outputs, 1)
        metric_precision(preds, labels)
        metric_recall(preds, labels)

precision = metric_precision.compute()
recall = metric_recall.compute()
print(f"Precision: {precision}")
print(f"Recall: {recall}")
```

#### Analyzing metrics per class

While aggregated metrics are useful indicators of the model's performance, it is often informative to look at the metrics per class. This could reveal classes for which the model underperforms.

In this exercise, you will run the evaluation loop again to get our cloud classifier's precision, but this time per-class. Then, you will map these score to the class names to interpret them. As usual, `Precision` has already been imported for you. Good luck!

```python
# Define precision metric
metric_precision = Precision(
    task='multiclass', num_classes = 7, average=None
)

net.eval()
with torch.no_grad():
    for images, labels in dataloader_test:
        outputs = net(images)
        _, preds = torch.max(outputs, 1)
        metric_precision(preds, labels)
precision = metric_precision.compute()

# Get precision per class
precision_per_class = {
    k: precision[v].item()
    for k, v 
    in dataset_test.class_to_idx.items()
}
print(precision_per_class)
```

## **Sequences & Recurrent Neural Networks**

### Handling sequences with Pytorch

#### Generating sequences

To be able to train neural networks on sequential data, you need to pre-process it first. You'll chunk the data into inputs-target pairs, where the inputs are some number of consecutive data points and the target is the next data point.

Your task is to define a function to do this called `create_sequences()`. As inputs, it will receive data stored in a DataFrame, `df` and `seq_length`, the length of the inputs. As outputs, it should return two NumPy arrays, one with input sequences and the other one with the corresponding targets.

As a reminder, here is how the DataFrame `df` looks like:

```
                 timestamp  consumption
0      2011-01-01 00:15:00    -0.704319
...                    ...          ...
140255 2015-01-01 00:00:00    -0.095751
```

```python
import numpy as np

def create_sequences(df, seq_length):
    xs, ys = [], []
    # Iterate over data indices
    for i in range(len(df) - seq_length):
      	# Define inputs
        x = df.iloc[i:(i + seq_length), 1]
        # Define target
        y = df.iloc[i + seq_length, 1]
        xs.append(x)
        ys.append(y)
    return np.array(xs), np.array(ys)
```

#### Sequential Dataset

Good job building the `create_sequences()` function! It's time to use it to create a training dataset for your model.

Just like tabular and image data, sequential data is easiest passed to a model through a torch Dataset and DataLoader. To build a sequential Dataset, you will call `create_sequences()` to get the NumPy arrays with inputs and targets, and inspect their shape. Next, you will pass them to a `TensorDataset` to create a proper torch Dataset, and inspect its length.

Your implementation of `create_sequences()` and a DataFrame with the training data called `train_data` are available.

```python
import torch
from torch.utils.data import TensorDataset

# Use create_sequences to create inputs and targets
X_train, y_train = create_sequences(train_data, 24*4)
print(X_train.shape, y_train.shape)

# Create TensorDataset
dataset_train = TensorDataset(
    torch.from_numpy(X_train).float(),
    torch.from_numpy(y_train).float(),
)
print(len(dataset_train)) 
```

### Recurrent Neural Networks (RRNs)

#### Building a forecasting RNN

It's time to build your first recurrent network! It will be a sequence-to-vector model consisting of an RNN layer with two layers and a `hidden_size` of `32`. After the RNN layer, a simple linear layer will map the outputs to a single value to be predicted.

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        # Define RNN layer
        self.rnn = nn.RNN(
            input_size=1,
            hidden_size=32,
            num_layers=2,
            batch_first=True,
        )
        self.fc = nn.Linear(32, 1)

    def forward(self, x):
        # Initialize first hidden state with zeros
        h0 = torch.zeros(2, x.size(0), 32)
        # Pass x and h0 through recurrent layer
        out, _ = self.rnn(x, h0)  
        # Pass recurrent layer's last output through linear layer
        out = self.fc(out[:, -1, :])
        return out
```

### LSTM and GRU cells

#### RNN vs. LSTM vs. GRU

Understanding the differences between a plain RNN, LSTM, and GRU networks, including  
their complexity and computation needs, allows you to choose the appropriate one for your task.

![[Pasted image 20260806094931.png]]

- LSTM cells keep two hidden states: one for short-term memory and one for long-term memory.
- In both LSTM and GRU cells, the hidden state is not only passed as input to the next time step, but also returned at the current time step (that is, "y" and "h" are the same).

#### LSTM network

As you already know, plain RNN cells are not used that much in practice. A more frequently used alternative that ensures a much better handling of long sequences are Long Short-Term Memory cells, or LSTMs. In this exercise, you will be build an LSTM network yourself!

The most important implementation difference from the RNN network you have built previously comes from the fact that LSTMs have two rather than one hidden states. This means you will need to initialize this additional hidden state and pass it to the LSTM cell.

```python
class Net(nn.Module):
    def __init__(self, input_size):
        super().__init__()
        # Define lstm layer
        self.lstm = nn.LSTM(
            input_size=1,
            hidden_size=32,
            num_layers=2,
            batch_first=True,
        )
        self.fc = nn.Linear(32, 1)

    def forward(self, x):
        h0 = torch.zeros(2, x.size(0), 32)
        # Initialize long-term memory
        c0 = torch.zeros(2, x.size(0), 32)
        # Pass all inputs to lstm layer
        out, _ = self.lstm(x, (h0, c0))
        out = self.fc(out[:, -1, :])
        return out
```

#### GRU network

Next to LSTMs, another popular recurrent neural network variant is the Gated Recurrent Unit, or GRU. It's appeal is in its simplicity: GRU cells require less computation than LSTM cells while often matching them in performance.

The code you are provided with is the RNN model definition that you coded previously. Your task is to adapt it such that it produces a GRU network instead. `torch` and `torch.nn as nn` have already been imported for you.

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        # Define RNN layer
        self.gru = nn.GRU(
            input_size=1,
            hidden_size=32,
            num_layers=2,
            batch_first=True,
        )
        self.fc = nn.Linear(32, 1)

    def forward(self, x):
        h0 = torch.zeros(2, x.size(0), 32)
        out, _ = self.gru(x, h0)  
        out = self.fc(out[:, -1, :])
        return out
```

### Training and evaluating RNNs

#### RNN training loop

It's time to train the electricity consumption forecasting model!

You will use the LSTM network you have defined previously, which has been instantiated and assigned to `net`, as is the `dataloader_train` you built before. You will also need to use `torch.nn` which has already been imported as `nn`.

In this exercise, you will train the model for only three epochs to make sure the training progresses as expected. Let's get to it!

```python
net = Net()
# Set up MSE loss
criterion = nn.MSELoss()
optimizer = optim.Adam(
  net.parameters(), lr=0.0001
)

for epoch in range(3):
    for seqs, labels in dataloader_train:
        # Reshape model inputs
        seqs = seqs.view(32, 96, 1)
        # Get model outputs
        outputs = net(seqs)
        # Compute loss
        loss = criterion(outputs, labels)
        optimizer.zero_grad()
        loss.backward()
        optimizer.step()
    print(f"Epoch {epoch+1}, Loss: {loss.item()}")
```

#### Evaluating forecasting models

It's evaluation time! The same LSTM network that you have trained in the previous exercise has been trained for you for a few more epochs and is available as `net`.

Your task is to evaluate it on a test dataset using the Mean Squared Error metric (`torchmetrics` has already been imported for you). Let's see how well the model is doing!

```python
# Define MSE metric
mse = torchmetrics.MeanSquaredError()

net.eval()
with torch.no_grad():
    for seqs, labels in dataloader_test:
        seqs = seqs.view(32, 96, 1)
        # Pass seqs to net and squeeze the result
        outputs = net(seqs).squeeze()
        mse(outputs, labels)

# Compute final metric value
test_mse = mse.compute()
print(f"Test MSE: {test_mse}")
```

## **Multi-Input & Multi-Output Architectures**

### Multi-input models

#### Two-input dataset

Building a multi-input model starts with crafting a custom dataset that can supply all the inputs to the model. In this exercise, you will build the Omniglot dataset that serves triplets consisting of:

- The image of a character to be classified,
- The one-hot encoded alphabet vector of length 30, with zeros  
    everywhere but for a single one denoting the ID of the alphabet the  
    character comes from,
- The target label, an integer between 0 and 963.

You are provided with `samples`, a list of 3-tuples comprising an image's file path, its alphabet vector, and the target label.

```python
class OmniglotDataset(Dataset):
    def __init__(self, transform, samples):
		# Assign transform and samples to class attributes
        self.transform = transform
        self.samples = samples
                    
    def __len__(self):
		# Return number of samples
        return len(self.samples)

    def __getitem__(self, idx):
      	# Unpack the sample at index idx
        img_path, alphabet, label = self.samples[idx]
        img = Image.open(img_path).convert('L')
        # Transform the image 
        img_transformed = self.transform(img)
        return img_transformed, alphabet, label
```

#### Two-input model

With the data ready, it's time to build the two-input model architecture! To do so, you will set up a model class with the following methods:

- `.__init__()`, in which you will define sub-networks  
    by grouping layers; this is where you define the two layers for  
    processing the two inputs, and the classifier that returns a  
    classification score for each class.
- `forward()`, in which you will pass both inputs  
    through corresponding pre-defined sub-networks, concatenate the outputs, and pass them to the classifier.

`torch.nn` is already imported for you as `nn`. Let's do it!

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        # Define sub-networks as sequential models
        self.image_layer = nn.Sequential(
            nn.Conv2d(1, 16, kernel_size=3, padding=1),
            nn.MaxPool2d(kernel_size=2),
            nn.ELU(),
            nn.Flatten(),
            nn.Linear(16*32*32, 128)
        )
        self.alphabet_layer = nn.Sequential(
            nn.Linear(30, 8),
            nn.ELU(), 
        )
        self.classifier = nn.Sequential(
            nn.Linear(128 + 8, 964), 
        )
        
    def forward(self, x_image, x_alphabet):
		# Pass the x_image and x_alphabet through appropriate layers
        x_image = self.image_layer(x_image)
        x_alphabet = self.alphabet_layer(x_alphabet)
        # Concatenate x_image and x_alphabet
        x = torch.cat((x_image, x_alphabet), dim=1)
        return self.classifier(x)
```

#### Training two-input model

The training loop for your two-input model will be a typical PyTorch training loop. The only change compared to the training loops you have written before is that now you have two inputs instead of one.

What is the correct way of iterating through the `dataloader` and passing the inputs to the model?

`for img, alpha, labels in dataloader_train: outputs = net(img, alpha)`

### Multi-output models

#### Two-output Dataset and DataLoader

In this and the following exercises, you will build a two-output model to predict both the character and the alphabet it comes from based on the character's image. As always, you will start with getting the data ready.

The `OmniglotDataset` class you have created before is available for you to use along with updated `samples`. Let's use it to build the Dataset and the DataLoader.

```python
# Print the sample at index 100
print(samples[100])

# Create dataset_train
dataset_train = OmniglotDataset(
    transform=transforms.Compose([
        transforms.ToTensor(),
      	transforms.Resize((64, 64)),
    ]),
    samples=samples,
)

# Create dataloader_train
dataloader_train = DataLoader(
    dataset_train, batch_size=32, shuffle=True,
)
```

#### Two-output model architecture

In this exercise, you will construct a multi-output neural network architecture capable of predicting the character and the alphabet.

Recall the general structure: in the `.__init__()` method, you define layers to be used in the forward pass later. In the `forward()` method, you will first pass the input image through a couple of layers  
to obtain its embedding, which in turn is fed into two separate classifier layers, one for each output.

```python
class Net(nn.Module):
    def __init__(self):
        super().__init__()
        self.image_layer = nn.Sequential(
            nn.Conv2d(1, 16, kernel_size=3, padding=1),
            nn.MaxPool2d(kernel_size=2),
            nn.ELU(),
            nn.Flatten(),
            nn.Linear(16*32*32, 128)
        )
        # Define the two classifier layers
        self.classifier_alpha = nn.Linear(128, 30)
        self.classifier_char = nn.Linear(128, 964)
        
    def forward(self, x):
        x_image = self.image_layer(x)
        # Pass x_image through the classifiers and return both results
        output_alpha = self.classifier_alpha(x_image)
        output_char = self.classifier_char(x_image)
        return output_alpha, output_char
```

#### Training multi-output models

When training models with multiple outputs, it is crucial to ensure that the loss function is defined correctly.

In this case, the model produces two outputs: predictions for the alphabet and the character. For each of these, there are corresponding ground truth labels, which will allow you to calculate two separate losses: one incurred from incorrect alphabet classifications, and the other from incorrect character classification. Since in both cases you are dealing with a multi-label classification task, the Cross-Entropy loss can be applied each time.

Gradient descent can optimize only one loss function, however. You will thus define the total loss as the sum of alphabet and character losses.

```python
net = Net()
criterion = nn.CrossEntropyLoss()
optimizer = optim.SGD(net.parameters(), lr=0.05)

for epoch in range(1):
    for images, labels_alpha, labels_char in dataloader_train:
        optimizer.zero_grad()
        outputs_alpha, outputs_char = net(images)
        # Compute alphabet classification loss
        loss_alpha = criterion(
            outputs_alpha, labels_alpha
        )
        # Compute character classification loss
        loss_char = criterion(
            outputs_char, labels_char
        )
        # Compute total loss
        loss = loss_alpha + loss_char
        loss.backward()
        optimizer.step()
```

### Evaluation of multi-outputs models and loss weighting

#### Multi-output model evaluation

In this exercise, you will practice model evaluation for multi-output models. Your task is to write a function called `evaluate_model()` that takes an alphabet-and-character-predicting model as input, runs the evaluation loop, and prints the model's accuracy in the two tasks.

```python
def evaluate_model(model):
    # Define accuracy metrics
    acc_alpha = Accuracy(task="multiclass", num_classes=30)
    acc_char = Accuracy(task="multiclass", num_classes=964)

    model.eval()
    with torch.no_grad():
        for images, labels_alpha, labels_char in dataloader_test:
            # Obtain model outputs
            outputs_alpha, outputs_char = model(images)
            _, pred_alpha = torch.max(outputs_alpha, 1)
            _, pred_char = torch.max(outputs_char, 1)
			# Update both accuracy metrics
            acc_alpha(pred_alpha, labels_alpha)
            acc_char(pred_char, labels_char)
    
    print(f"Alphabet: {acc_alpha.compute()}")
    print(f"Character: {acc_char.compute()}")
```