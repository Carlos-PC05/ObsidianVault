#DeepLearning 
# Slides

[chapter1-4_compressed.pdf](attachment:fdef3584-99ec-4855-91a7-bd9a05209ea1:chapter1-4_compressed.pdf)

[chapter2-4_compressed.pdf](attachment:fb7d0f99-9f22-424d-9c1a-363b4b50bf96:chapter2-4_compressed.pdf)

[chapter3-1_compressed.pdf](attachment:ecc16491-07c2-42e7-b767-4657b72cc58a:chapter3-1_compressed.pdf)

[chapter4-4.pdf](attachment:ad6445c4-d1f2-4282-9de5-bc8a39b0bc23:chapter4-4.pdf)

# Notes

## Introduction to Pytorch

### Intro to Deep Learning with Pytorch

#### Introducción a los tensores de PyTorch

Los Tensores son la estructura de datos central de PyTorch y la base del aprendizaje profundo. Son similares a las matrices NumPy, pero tienen características únicas.

Aquí tienes una lista Python llamada `temperatures` que contiene lecturas diarias de dos estaciones meteorológicas. ¡Intenta convertir esto en un tensor!

```python
# Import PyTorch
import torch

temperatures = [[72, 75, 78], [70, 73, 76]]

# Create a tensor from temperatures
temp_tensor = torch.tensor(temperatures)

print(temp_tensor)
```

#### Comprobar y sumar tensores

Mientras recoges los datos de temperatura, observas que las lecturas tienen una desviación de dos grados. Añade dos grados al tensor `temperatures` después de verificar su forma y tipo de datos con `torch` para garantizar la compatibilidad con el tensor `adjustment`.

```python
adjustment = torch.tensor([[2, 2, 2], [2, 2, 2]])

# Display the shape of the adjustment tensor
print("Adjustment shape:", adjustment.shape)

# Display the type of the adjustment tensor
print("Adjustment type:", adjustment.dtype)

print("Temperatures shape:", temperatures.shape)
print("Temperatures type:", temperatures.dtype)ç

adjustment = torch.tensor([[2, 2, 2], [2, 2, 2]])

# Add the temperatures and adjustment tensors
corrected_temperatures = temperatures + adjustment
print("Corrected temperatures:", corrected_temperatures)
```

#### Red de capas lineales

Las redes neuronales suelen contener muchas capas, pero la mayoría son **capas lineales**. Comprender una **sola capa lineal** te ayuda a entender cómo funcionan antes de añadirles complejidad.

Aplica una capa lineal a un tensor de entrada y observa la salida.

```python
import torch
import torch.nn as nn

input_tensor = torch.tensor([[0.3471, 0.4547, -0.2356]])

# Create a Linear layer
linear_layer = nn.Linear(
                         in_features=3, 
                         out_features=2
                         )

# Pass input_tensor through the linear layer
output = linear_layer(input_tensor)

print(output)
```

¿Cuál es la función de los **pesos** en una red neuronal?

ANS → Los pesos determinan la influencia de cada entrada en el resultado neuronal.

#### Tu primera red neuronal

Es hora de que pongas en marcha una pequeña red neuronal que contenga dos capas **lineales** en secuencia.

```python
import torch
import torch.nn as nn

input_tensor = torch.Tensor([[2, 3, 6, 7, 9, 3, 2, 1]])

# Create a container for stacking linear layers
model = nn.Sequential(nn.Linear(8, 4),
                nn.Linear(4, 1)
                )

output = model(input_tensor)
print(output)
```

#### Contar el número de parámetros

Los modelos de aprendizaje profundo son famosos por tener **muchos parámetros**. Con más parámetros vienen más complejidad computacional y tiempos de entrenamiento más largos, y un profesional del aprendizaje profundo debe saber cuántos parámetros tiene su modelo.

En este ejercicio, **primero calcularás manualmente el número de parámetros**. A continuación, comprobarás tu resultado utilizando el método `.numel()`.

```python
import torch.nn as nn

model = nn.Sequential(nn.Linear(9, 4),
                      nn.Linear(4, 2),
                      nn.Linear(2, 1))

total = 0

# Calculate the number of parameters in the model
for p in model.parameters():
  total += p.numel()
  
print(f"The number of parameters in the model is {total}")
```

## **Neural Network Architecture and Hyperparameters**

### Discovering activation functions

#### Las funciones sigmoide y softmax

Las funciones **sigmoide** y **softmax** son funciones de activación clave en el aprendizaje profundo, a menudo utilizadas como paso final en una red neuronal.

- **Sigmoide** es para clasificación binaria
- **Softmax** es para clasificación multiclase

Dado un tensor de salida de preactivación de una red, aplica la función de activación adecuada para obtener el resultado final.

`torch.nn` ya se ha importado como `nn`.

```python
input_tensor = torch.tensor([[2.4]])

# Create a sigmoid function and apply it on input_tensor
sigmoid = nn.Sigmoid()
probability = sigmoid(input_tensor)
print(probability)

# Create a softmax function and apply it on input_tensor
softmax = nn.Softmax()
probabilities = softmax(input_tensor)
print(probabilities)
```

### Running a forward pass

#### Crear un clasificador binario en PyTorch

Recordemos que una pequeña red neuronal con una sola capa lineal seguida de una  
función sigmoide es un clasificador binario. Actúa igual que una regresión logística.

Practica la creación de esta pequeña red y la interpretación de la salida del clasificador.

```python
import torch
import torch.nn as nn

input_tensor = torch.Tensor([[3, 4, 6, 2, 3, 6, 8, 9]])

# Implement a small neural network for binary classification
model = nn.Sequential(
  nn.Linear(8, 1),
  nn.Sigmoid()
)

output = model(input_tensor)
print(output)
```

#### De la regresión a la clasificación multiclase

Los modelos que has visto para la clasificación binaria, la clasificación multiclase y la regresión han sido todos similares, salvo algunos ajustes del modelo.

Empieza construyendo un modelo para la **regresión** y luego ajústalo para realizar una **clasificación multiclase**.

```python
import torch
import torch.nn as nn

input_tensor = torch.Tensor([[3, 4, 6, 7, 10, 12, 2, 3, 6, 8, 9]])

# Update network below to perform a multi-class classification with four labels
model = nn.Sequential(
  nn.Linear(11, 20),
  nn.Linear(20, 12),
  nn.Linear(12, 6),
  nn.Linear(6, 4),
  nn.Softmax()
)

output = model(input_tensor)
print(output)

""" Multiclass """

input_tensor = torch.Tensor([[3, 4, 6, 7, 10, 12, 2, 3, 6, 8, 9]])

# Update network below to perform a multi-class classification with four labels
model = nn.Sequential(
  nn.Linear(11, 20),
  nn.Linear(20, 12),
  nn.Linear(12, 6),
  nn.Linear(6, 4),
  nn.Softmax()
)

output = model(input_tensor)
print(output)
```

### Using loss functions to assess model predictions

#### Creación de etiquetas con programación one-hot

La programación one-hot convierte una sola etiqueta entera en un vector con N elementos, donde N es el número de clases. Este vector contiene ceros y un uno en la posición correcta.

En este ejercicio, crearás manualmente un vector con programación one-hot para y, y luego utilizarás PyTorch para simplificar el proceso. Tu conjunto de datos tiene tres clases (0, 1, 2).

`numpy` (`np`), `torch.nn.functional` (`F`) y `torch` ya están importados para ti.

```python
y = 1
num_classes = 3

# Create the one-hot encoded vector using NumPy
one_hot_numpy = np.array([0, 1, 0])

# Create the one-hot encoded vector using PyTorch
one_hot_pytorch = F.one_hot(torch.tensor(y), num_classes=num_classes)

print("One-hot vector using NumPy:", one_hot_numpy)
print("One-hot vector using PyTorch:", one_hot_pytorch)
```

# Cálculo de crossentropy-loss

La crossentropy-loss es un método muy utilizado para medir la pérdida de clasificación. En este ejercicio, calcularás la pérdida de entropía cruzada en PyTorch utilizando:

- `y`: la etiqueta de verdad fundamental.
- `scores`: un vector de predicciones antes de softmax.

Las funciones de pérdida ayudan a las redes neuronales a aprender midiendo los errores de predicción. Crea un vector codificado con programación one-hot para `y`, define la función de pérdida de entropía cruzada y calcula la pérdida utilizando `scores` y la etiqueta codificada. El resultado será un único valor flotante que representa la pérdida de la muestra.

`torch`, `CrossEntropyLoss` y `torch.nn.functional` como `F` ya se han importado para ti.

```python
import torch
import torch.nn.functional as F
from torch.nn import CrossEntropyLoss

y = [2]
scores = torch.tensor([[0.1, 6.0, -2.0, 3.2]])

# Create a one-hot encoded vector of the label y
one_hot_label = F.one_hot(torch.tensor(y), num_classes=4)

# Create the cross entropy loss function
criterion = CrossEntropyLoss()

# Calculate the cross entropy loss
loss = criterion(scores.double(), one_hot_label.double())
print(loss)
```

### Using derivatives to update model parameters

#### Acceso a los parámetros del modelo

Un modelo PyTorch creado con `nn.Sequential()` es un módulo que contiene las diferentes capas de tu red. Recuerda que se puede acceder a cada parámetro de la capa indexando directamente el  
modelo creado. En este ejercicio, practicarás el acceso a los parámetros de diferentes capas **lineales** de una red neuronal.

```python
model = nn.Sequential(nn.Linear(16, 8),
                      nn.Linear(8, 2)
                     )

# Access the weight of the first linear layer
weight_0 = model[0].weight
print("Weight of the first layer:", weight_0)

# Access the bias of the second linear layer
bias_1 = model[1].bias
print("Bias of the second layer:", bias_1)
```

#### Actualización manual de los pesos

Ahora que sabes cómo acceder a los pesos y sesgos, realizarás manualmente el trabajo del optimizador PyTorch. Aunque PyTorch lo automatiza, practicarlo manualmente te ayuda a adquirir intuición sobre cómo aprenden y se ajustan los modelos. Esta comprensión será valiosa a la  
hora de depurar o ajustar las redes neuronales.

Se ha creado una red neuronal de tres capas y se ha almacenado como variable `model`.  
Esta red se ha utilizado para un paso hacia delante y se han calculado la pérdida y sus derivadas. Se ha elegido una tasa de aprendizaje predeterminada, `lr`, para escalar los gradientes al realizar la actualización

```python
weight0 = model[0].weight
weight1 = model[1].weight
weight2 = model[2].weight

# Access the gradients of the weight of each linear layer
grads0 = weight0.grad
grads1 = weight1.grad
grads2 = weight2.grad

# Update the weights using the learning rate and the gradients
weight0 = weight0 - lr * grads0
weight1 = weight1 - lr * grads1
weight2 = weight2 - lr * grads2
```

#### Uso del optimizador PyTorch

Anteriormente, actualizaste manualmente el peso de una red, comprendiendo cómo  
funciona el entrenamiento entre bastidores. Sin embargo, este método no es escalable para redes profundas con muchas capas.

Afortunadamente, PyTorch proporciona el optimizador SGD, que automatiza este proceso eficazmente en solo unas pocas líneas de código. Ahora, completarás el bucle de entrenamiento actualizando los pesos mediante un optimizador PyTorch.

Se ha creado una red neuronal y se ha proporcionado como variable `model`. Este modelo se utilizó para ejecutar un paso hacia delante y crear el tensor de previsiones `pred`. El tensor con programación one-hot se denomina `target` y la función de pérdida de entropía cruzada se almacena como `criterion`.

`torch.optim` como `optim` y `torch.nn` como `nn` ya se han cargado para ti.

```python
# Create the optimizer
optimizer = optim.SGD(model.parameters(), lr=0.001)

loss = criterion(pred, target)
loss.backward()

# Update the model's parameters using the optimizer
optimizer.step()
```

## Training a Neural Network with Pytorch

### A deeper dive into loading data

#### Utilizar TensorDataset

Estructurar tus datos en un conjunto de datos es uno de los primeros pasos para entrenar una red neuronal PyTorch. `TensorDataset` lo simplifica convirtiendo las matrices NumPy en un formato que PyTorch pueda utilizar.

En este ejercicio, crearás un `TensorDataset` utilizando el conjunto de datos precargado `animals` e inspeccionarás su estructura.

```python
import torch
from torch.utils.data import TensorDataset

X = animals.iloc[:, 1:-1].to_numpy()  
y = animals.iloc[:, -1].to_numpy()

# Create a dataset
dataset = TensorDataset(torch.tensor(X), torch.tensor(y))

# Print the first sample
input_sample, label_sample = dataset[0]
print('Input sample:', input_sample)
print('Label sample:', label_sample)
```

#### Uso de DataLoader

La clase `DataLoader` es esencial para manejar eficazmente grandes conjuntos de datos. Acelera el entrenamiento, optimiza el uso de memoria y estabiliza las actualizaciones de gradiente, haciendo que los modelos de aprendizaje profundo sean más eficaces.

Ahora, crearás un PyTorch `DataLoader` utilizando el `dataset` del ejercicio anterior y lo verás en acción.

```python
from torch.utils.data import DataLoader

# Create a DataLoader
dataloader = DataLoader(dataset, batch_size = 2, shuffle = True)

# Iterate over the dataloader
for batch_inputs, batch_labels in dataloader:
    print('batch_inputs:', batch_inputs)
    print('batch_labels:', batch_labels)
```

### Writing our first training loop

#### Using the MSELoss

For regression problems, you often use **Mean Squared Error (MSE)** as a loss function instead of cross-entropy. MSE calculates the squared difference between predicted values (`y_pred`) and actual values (`y`). Now, you'll compute MSE loss using both NumPy and PyTorch.

`torch`, `numpy` (as `np`), and `torch.nn` (as `nn`) packages are already imported.

```python
y_pred = np.array([3, 5.0, 2.5, 7.0])  
y = np.array([3.0, 4.5, 2.0, 8.0])     

# Calculate MSE using NumPy
mse_numpy = np.mean((y_pred - y) ** 2)

# Create the MSELoss function in PyTorch
criterion = nn.MSELoss()

# Calculate MSE using PyTorch
mse_pytorch = criterion(torch.tensor(y_pred), torch.tensor(y))

print("MSE (NumPy):", mse_numpy)
print("MSE (PyTorch):", mse_pytorch)
```

#### Writing a training loop

In `scikit-learn`, the training loop is wrapped in the `.fit()` method, while in PyTorch, it's set up manually. While this adds flexibility, it requires a custom implementation.

In this exercise, you'll create a loop to train a model for salary prediction.

The `show_results()` function is provided to help you visualize some sample predictions.

The package imports provided are: pandas as `pd`, `torch`, `torch.nn` as `nn`, `torch.optim` as `optim`, as well as `DataLoader` and `TensorDataset` from `torch.utils.data`.

The following variables have been created: `num_epochs`, containing the number of epochs (set to 5); `dataloader`, containing the dataloader; `model`, containing the neural network; `criterion`, containing the loss function, `nn.MSELoss()`; `optimizer`, containing the SGD optimizer.

```python
# Loop over the number of epochs and the dataloader
for i in range(num_epochs):
  for data in dataloader:
    # Set the gradients to zero
    optimizer.zero_grad()
    # Run a forward pass
    feature, target = data
    prediction = model(feature)    
    # Compute the loss
    loss = criterion(prediction, target)    
    # Compute the gradients
    loss.backward()
    # Update the model's parameters
    optimizer.step()
show_results(model, dataloader)
```

### ReLU activation functions

#### Implementing ReLU

The Rectified Linear Unit (ReLU) is a widely-used activation function in deep learning, solving challenges like the vanishing gradients problem.

In this exercise, you'll implement ReLU in PyTorch, apply it to **both positive and negative values**, and observe the results.

`torch.nn` package has already been imported for you as `nn`.

```python
# Create a ReLU function with PyTorch
relu_pytorch = nn.ReLU()

x_pos = torch.tensor(2.0)
x_neg = torch.tensor(-3.0)

# Apply the ReLU function to the tensors
output_pos = relu_pytorch(x_pos)
output_neg = relu_pytorch(x_neg)

print("ReLU applied to positive value:", output_pos)
print("ReLU applied to negative value:", output_neg)
```

#### Implementing leaky ReLU

While ReLU is widely used, it sets negative inputs to 0, resulting in **null gradients** for those values. This can prevent parts of the model from learning.

**Leaky ReLU** overcomes this by allowing small gradients for negative inputs, controlled by the `negative_slope` parameter. Instead of 0, negative inputs are scaled by this small value, keeping the model's learning active.

In this exercise, you will implement the leaky ReLU function in PyTorch and practice using it. `torch` package as well as the `torch.nn` as `nn` have already been imported.

```python
# Create a leaky relu function in PyTorch
leaky_relu_pytorch = nn.LeakyReLU(negative_slope = 0.05)

x = torch.tensor(-2.0)
# Call the above function on the tensor x
output = leaky_relu_pytorch(x)
print(output)
```

## Evaluating and Improving Models

### Layer initialization and transfer learning

#### Fine-tuning process

You are training a model on a new dataset and you think you can use a fine-tuning approach instead  
of training from scratch (i.e., training from randomly initialized weights).

To fine-tune a model on a new task, you need to follow a certain number of steps that are listed here.

![[Pasted image 20260806094738.png]]

#### Freeze layers of a model

You are about to fine-tune a model on a new task after loading pre-trained weights. The model contains three linear layers. However, because your dataset is small, you only want to train the last linear layer of this model and freeze the first two linear layers.

The model has already been created and exists under the variable `model`. You will be using the `named_parameters` method of the model to list the parameters of the model. Each parameter  
is described by a name. This name is a string with the following naming convention: `x.name` where `x` is the index of the layer.

Remember that a linear layer has two parameters: the `weight` and the `bias`.

```python
for name, param in model.named_parameters():
  
    # Check for first layer's weight
    if name == '0.weight':
   
        # Freeze this weight
        param.requires_grad = False
        
    # Check for second layer's weight
    if name == '1.weight':
      
        # Freeze this weight
        param.requires_grad = False
```

#### Layer initialization

The initialization of the weights of a neural network has been the focus of researchers for many years. When training a network, the method used to initialize the weights has a direct impact on the final performance of the network.

As a machine learning practitioner, you should be able to experiment with different initialization strategies. In this exercise, you are creating a small neural network made of two layers and you are deciding to initialize each layer's weights with the uniform method.

```python
layer0 = nn.Linear(16, 32)
layer1 = nn.Linear(32, 64)

# Use uniform initialization for layer0 and layer1 weights
nn.init.uniform_(layer0.weight)
nn.init.uniform_(layer1.weight)

model = nn.Sequential(layer0, layer1)
```

### Evaluating model performance

#### Writing the evaluation loop

In this exercise, you will write an evaluation loop to compute validation loss. The evaluation loop follows a similar structure to the training loop but **without gradient calculations or weight updates**.

`model`, `validationloader`, and loss function `criterion` have already been defined to handle predictions, data loading, and loss calculation.

```python
# Set the model to evaluation mode
model.eval()
validation_loss = 0.0

with torch.no_grad():
  for features, labels in validationloader:
      outputs = model(features)
      loss = criterion(outputs, labels)
      # Sum the current loss to the validation_loss variable
      validation_loss += loss.item()
      
# Calculate the mean loss value
validation_loss_epoch = validation_loss / len(validationloader)
print(validation_loss_epoch)

# Set the model back to training mode
model.train()
```

#### Calculating accuracy using torchmetrics

Tracking accuracy during training helps identify the best-performing epoch.

In this exercise, you'll use `torchmetrics` to calculate accuracy on a **facemask dataset** with three classes. The `plot_errors` function will highlight misclassified samples, helping you analyze model errors.

`torchmetrics` package is already imported. Model `outputs` are softmax probabilities, and `labels` are one-hot encoded vectors.

```python
# Create accuracy metric
metric = torchmetrics.Accuracy(task="multiclass", num_classes=3)
for features, labels in dataloader:
    outputs = model(features)
    
    # Calculate accuracy over the batch
    metric.update(outputs, labels.argmax(dim=-1))
    
# Calculate accuracy over the whole epoch
accuracy = metric.compute()
print(f"Accuracy on all data: {accuracy}")

# Reset metric for the next epoch
metric.reset()
plot_errors(model, dataloader)
```

### Fighting overfitting

#### Experimenting with dropout

Dropout helps prevent overfitting by randomly setting some output values to zero during training. In this exercise, you'll build a simple neural network with dropout and observe how it behaves in training and evaluation modes.

`torch.nn` package is preloaded as `nn`, and `features` is already defined for you.

```python
model = nn.Sequential(
    nn.Linear(8, 6),
    nn.Linear(6, 4),
    nn.Dropout(p=0.5))

model.train()
output_train = model(features)

# Forward pass in evaluation mode (Dropout disabled)
model.eval()
output_eval = torch.randn(1, 8)

# Print results
print("Output in train mode:", output_train)
print("Output in eval mode:", output_eval)
```

### Improving model performance

#### Implementing random search

Hyperparameter search is a computationally costly approach to experiment with different hyperparameter values. However, it can lead to performance improvements. In this exercise, you will implement a random search algorithm.

You will randomly sample 10 values of the learning rate and momentum from the uniform distribution. To do so, you will use the `np.random.uniform()` function.

`numpy` package has already been imported as `np`, and a `plot_hyperparameter_search()` function has been created to visualize your results.

```python
values = []
for idx in range(10):
    # Randomly sample a learning rate factor between 2 and 4
    factor = np.random.uniform(2, 4)
    lr = 10 ** -factor
    
    # Randomly select a momentum between 0.85 and 0.99
    momentum = np.random.uniform(0.85, 0.99)
    
    values.append((lr, momentum))
       
plot_hyperparameter_search(values)
```