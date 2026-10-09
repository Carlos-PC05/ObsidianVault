#DeepLearning 
# Diapositivas

[chapter1-1_compressed.pdf](attachment:c9bb57b4-b9e7-4f52-8665-01f170189ce3:chapter1-1_compressed.pdf)

[chapter2-1.pdf](attachment:88ad9e1f-a1bd-4d2a-a5aa-bd575b76f109:chapter2-1.pdf)

[chapter3-1.pdf](attachment:86dc1ad3-7b7c-46dd-b7c5-246d4405309d:chapter3-1.pdf)

[chapter4-1_compressed.pdf](attachment:ebe26d16-19a6-4870-b9e1-c7a8e7af2e70:chapter4-1_compressed.pdf)

## Procesamiento de imágenes con redes neuronales

### Las imágenes como datos: visualizaciones

Para mostrar datos de imágenes, utilizarás la biblioteca [Matplotlib](https://matplotlib.org/) de Python y, en concreto, el submódulo `pyplot` de matplotlib, que contiene muchos comandos para gráficos. Algunos de  
estos comandos te permiten mostrar el contenido de imágenes almacenadas en arrays.

### Las imágenes como datos: imágenes cambiantes

Para modificar una imagen, puedes modificar los números existentes en el array. En una imagen en color, puedes cambiar los valores de uno de los canales de color sin afectar a los demás colores, indexando en la última dimensión del arreglo.

```python
"""Ejemplo de poner un cuadrado rojo de 10x10px en la esquina superior izquierda"""
# Set the red channel in this part of the image to 1
data[:10, :10, 0] = 1

# Set the green channel in this part of the image to 0
data[:10, :10, 1] = 0

# Set the blue channel in this part of the image to 0
data[:10, :10, 2] = 0

# Visualize the result
plt.imshow(data)
plt.show()
```

### Uso de la codificación one-hot para representar imágenes

Las redes neuronales esperan que las etiquetas de las clases de un conjunto de datos estén organizadas con codificación one-hot: cada fila del array contiene ceros en todas las columnas, excepto en la columna correspondiente a una etiqueta única, que se establece en 1.

### Evaluación de un clasificador

Para evaluar un clasificador, necesitamos probarlo con imágenes que no se hayan utilizado durante el entrenamiento. Esto se denomina «validación cruzada»: se realiza una predicción de la clase (por ejemplo, camiseta, vestido o zapato) a partir de cada una de las imágenes de prueba, y  
estas predicciones se comparan con las etiquetas reales de estas imágenes.

```python
# The number of image categories
n_categories = 3

# The unique values of categories in the data
categories = np.array(["shirt", "dress", "shoe"])

# Initialize ohe_labels as all zeros
ohe_labels = np.zeros((len(labels), n_categories))

# Loop over the labels
for ii in range(len(labels)):
    # Find the location of this label in the categories variable
    jj = np.where(categories == labels[ii])
    # Set the corresponding zero to one
    ohe_labels[ii,jj] = 1

# Calculate the number of correct predictions
number_correct = (test_labels * predictions).sum()
print(number_correct)

# Calculate the proportion of correct predictions
proportion_correct = number_correct / len(predictions)
print(proportion_correct)
```

### Construye una red neuronal

Utilizaremos la biblioteca `Keras` para crear redes neuronales y entrenarlas para clasificar imágenes. Todos estos modelos serán del tipo « `Sequential` », lo que significa que las salidas de una capa se proporcionan como entradas únicamente a la siguiente capa.

```python
"""Construir una red neuronal con Keras"""
# Imports components from Keras
from keras.models import Sequential
from keras.layers import Dense

# Initializes a sequential model
model = Sequential()

# First layer
model.add(Dense(10, activation="relu", input_shape=(784,)))

# Second layer
model.add(Dense(10, activation="relu"))

# Output layer
model.add(Dense(3, activation="softmax"))
```

### Compilar una red neuronal

Una vez que hayas construido un modelo en `Keras`, es necesario compilarlo antes de poder ajustarlo a los datos. Esto significa que debes especificar el optimizador que se utilizará para  
ajustar el modelo y la función de pérdida que se utilizará en la optimización. Opcionalmente, también puedes especificar una lista de métricas que el modelo mantendrá en el programa. Por ejemplo, si deseas conocer la precisión de la clasificación, proporcionarás la lista `['accuracy']` al argumento de palabra clave `metrics`.

```python
# Compile the model
model.compile(optimizer="adam", 
           loss='categorical_crossentropy', 
           metrics=['accuracy'])
```

### Ajuste de un modelo de red neuronal a datos sobre prendas de vestir

En este ejercicio, ajustarás la red neuronal totalmente conectada que construiste en el ejercicio anterior a los datos de la imagen.  
Los datos de entrenamiento se proporcionan como dos variables: `train_data`, que contiene los datos de píxeles de 50 imágenes de las tres clases de ropa, y `train_labels`, que contiene representaciones codificadas en one-hot de las etiquetas de cada una de estas 50 imágenes. Transforma los datos en la entrada esperada por la red y, a continuación, ajusta el modelo a los datos de entrenamiento y las etiquetas de entrenamiento.

```python
# Reshape the data to two-dimensional array
train_data = train_data.reshape(50, 784)

# Fit the model
model.fit(train_data, train_labels, validation_split=0.2, epochs=3)
```

### Validación cruzada para la evaluación de redes neuronales

Para evaluar el modelo, utilizamos un conjunto de datos de prueba independiente. Al igual que en los datos del tren, las imágenes de los datos de prueba también deben modificarse antes de poder proporcionarse a la red totalmente conectada, ya que la red espera una columna por píxel en la entrada.

```python
# Reshape test data
test_data = test_data.reshape(10, 784)

# Evaluate the model
model.evaluate(test_data, test_labels)
```

## Uso de Convoluciones

### Convoluciones unidimensionales

Una convolución de un array unidimensional con un kernel consiste en tomar el kernel, deslizarlo a lo largo del array, multiplicarlo por los elementos del array que se superponen con el kernel en esa ubicación y sumar este producto.

```python
array = np.array([1, 0, 1, 0, 1, 0, 1, 0, 1, 0])
kernel = np.array([1, -1, 0])
conv = np.array([0, 0, 0, 0, 0, 0, 0, 0, 0, 0])

# Output array
for ii in range(8):
    conv[ii] = (kernel * array[ii:ii+3]).sum()

# Print conv
print(conv)
```

### Convoluciones de imágenes

La convolución de una imagen con un núcleo resume una parte de la imagen como la suma de la multiplicación de esa parte de la imagen con el núcleo. En este ejercicio, escribirás el código que ejecuta una convolución de una imagen con un núcleo utilizando Numpy. Dada una imagen en blanco y negro almacenada en la variable « `im` », escribe las operaciones dentro del bucle que ejecutarían la convolución con el núcleo proporcionado.

```python
kernel = np.array([[0, 1, 0], [1, 1, 1], [0, 1, 0]])
result = np.zeros(im.shape)

# Output array
for ii in range(im.shape[0] - 3):
    for jj in range(im.shape[1] - 3):
        result[ii, jj] = (im[ii:ii+3, jj:jj+3] * kernel).sum()

# Print result
print(result)
```

### Definición de núcleos de convolución de imágenes

En el ejercicio anterior, escribiste código que realiza una convolución dada una imagen y un núcleo. Este código ahora está almacenado en una función llamada « `convolution()` » que toma dos entradas: « `image` » y « `kernel` » y produce la imagen convolucionada. En este ejercicio, se te pedirá que definas el núcleo que encuentra una característica concreta en la imagen.

Por ejemplo, el siguiente kernel encuentra una línea vertical en las imágenes:

```
np.array([[-1, 1, -1],

          [-1, 1, -1],

          [-1, 1, -1]])
```

### Red convolucional para la clasificación de imágenes

Las redes convolucionales para la clasificación se construyen a partir de una secuencia de capas convolucionales (para el procesamiento de imágenes) y capas totalmente conectadas (`Dense`) (para la lectura). En este ejercicio, construirás una pequeña red convolucional para clasificar los datos del conjunto de datos de moda.

```python
# Import the necessary components from Keras
from keras.models import Sequential
from keras.layers import Dense, Conv2D, Flatten

# Initialize the model object
model = Sequential()

# Add a convolutional layer
model.add(Conv2D(10, kernel_size=3, activation='relu', 
               input_shape=(img_rows, img_cols, 1)))

# Flatten the output of the convolutional layer
model.add(Flatten())
# Add an output layer for the 3 categories
model.add(Dense(3, activation='softmax'))
```

### Entrenamiento de una CNN para clasificar tipos de ropa

Antes de entrenar una red neuronal, es necesario compilarla con la función de coste adecuada, utilizando el optimizador adecuado. Durante la compilación, también puedes definir métricas que la red calcula y reporta en cada época. El ajuste del modelo requiere un conjunto de datos de entrenamiento, junto con las etiquetas de entrenamiento para la red.

```python
# Compile the model 
model.compile(optimizer="adam", 
              loss="categorical_crossentropy", 
              metrics=['accuracy'])

# Fit the model on a training set
model.fit(train_data, train_labels, 
          validation_split=0.20, 
          epochs=3, batch_size=10)
```

### Evaluación de una CNN con datos de prueba

Para evaluar una red neuronal entrenada, debes proporcionar un conjunto de datos de prueba independiente con imágenes etiquetadas. El modelo de prueba « `model` » que has ajustado en el ejercicio anterior está disponible en tu espacio de trabajo.

```python
# Evaluate the model on separate test data
model.evaluate(test_data, test_labels, batch_size=10)
```

### Añadir relleno a una CNN

El relleno permite que una capa convolucional conserve la resolución de la entrada en esta capa. Esto se hace añadiendo ceros alrededor de los bordes de la imagen de entrada, de modo que el núcleo de convolución pueda superponerse con los píxeles del borde de la imagen.

```python
# Initialize the model
model = Sequential()

# Add the convolutional layer
model.add(Conv2D(10, kernel_size=3, activation='relu', 
                 input_shape=(img_rows, img_cols, 1), 
                 padding = "same"))

# Feed into output layer
model.add(Flatten())
model.add(Dense(3, activation='softmax'))
```

### Añade pasos a una red convolucional

El tamaño de los pasos del núcleo de convolución determina si el núcleo omitirá algunos de los píxeles a medida que se desliza por la imagen.  
Esto afecta al tamaño de la salida porque, cuando los pasos son mayores que uno, el núcleo se centrará solo en algunos de los píxeles.

`strides = x` → este parámetro del modelo Conv2D hará que salte de ‘x’ en ‘x’ el kernel

## Profundizando

### Creación de una red de aprendizaje profundo

Una red neuronal convolucional profunda es una red que tiene más de una capa. Cada capa de una red profunda recibe su entrada de la capa anterior, y la primera capa recibe su entrada de las imágenes utilizadas como datos de entrenamiento o prueba.

```python
from keras.models import Sequential
from keras.layers import Dense, Conv2D, Flatten

model = Sequential()

# Add a convolutional layer (15 units)
model.add(Conv2D(15, kernel_size = 2, activation = 'relu', input_shape = (img_rows, img_cols, 1)))

# Add another convolutional layer (5 units)
model.add(Conv2D(5, kernel_size = 2, activation = 'relu'))

# Flatten and feed to output layer
model.add(Flatten())
model.add(Dense(3, activation='softmax'))
```

### Entrena una CNN profunda para clasificar imágenes de ropa.

El entrenamiento de un modelo de _deep learning_ es muy similar al entrenamiento de una red de una sola capa. Una vez construido el modelo (como has hecho en el ejercicio anterior), es necesario compilarlo con el conjunto de parámetros adecuado. A continuación, el modelo se ajusta proporcionándole datos de entrenamiento, así como etiquetas de entrenamiento. Una vez finalizado el entrenamiento, el modelo puede evaluarse con datos de prueba.

```python
# Compile model
model.compile(optimizer='adam', 
              loss='categorical_crossentropy', 
              metrics=['accuracy'])

# Fit the model to training data 
model.fit(train_data, train_labels, 
          validation_split=0.2, 
          epochs=3, batch_size=10)

# Evaluate the model on test data
model.evaluate(test_data, test_labels, batch_size=10)
```

### Escribe tu propia operación de agrupación

Como hemos visto anteriormente, las CNN pueden tener muchos parámetros. Las capas de agrupación se añaden a menudo entre las capas convolucionales de una red neuronal para resumir sus resultados de forma condensada y reducir el número de parámetros en la siguiente capa de la red. Esto puede sernos útil si queremos entrenar la red más rápidamente o si no disponemos de datos suficientes para aprender un gran número de parámetros.

Una capa de agrupamiento puede describirse como un tipo particular de convolución. Para cada ventana de la entrada, encuentra el valor máximo de píxeles y solo pasa este píxel

```python
# Result placeholder
result = np.zeros((im.shape[0]//2, im.shape[1]//2))

# Pooling operation
for ii in range(result.shape[0]):
    for jj in range(result.shape[1]):
        result[ii, jj] = np.max(im[ii*2:ii*2+2, jj*2:jj*2+2]
)
```

### Capas de agrupación de Keras

Keras implementa una operación de agrupación como una capa que se puede añadir a las CNN entre otras capas. En este ejercicio, construirás una red neuronal convolucional similar a la que has construido anteriormente:

**Convolución => Convolución => Aplanar => Denso**

Sin embargo, también añadirás una capa de agrupación. La arquitectura añadirá una única capa de max-pooling entre la capa convolucional y la capa densa con un pooling de 2x2:

**Convolución => Agrupamiento máximo => Convolución => Aplanamiento => Denso**

```python
# Add a convolutional layer
model.add(Conv2D(15, kernel_size=2, activation='relu', 
                 input_shape=(img_rows, img_cols, 1)))

# Add a pooling operation
model.add(MaxPool2D(2))

# Add another convolutional layer
model.add(Conv2D(5, kernel_size=2, activation='relu'))

# Flatten and feed to output layer
model.add(Flatten())
model.add(Dense(3, activation='softmax'))
model.summary()
```

### Entrena una CNN profunda con agrupación para clasificar imágenes.

El entrenamiento de una CNN con capas de agrupación es muy similar al entrenamiento de las redes profundas que has visto anteriormente. Una vez construida la red (como hiciste en el ejercicio anterior), es necesario compilar el modelo adecuadamente y, a continuación, proporcionar los datos de entrenamiento, junto con los demás argumentos que controlan el procedimiento de ajuste.

```python
# Compile the model
model.compile(optimizer = 'adam', loss = 'categorical_crossentropy', metrics = ['accuracy'])

# Fit to training data
model.fit(train_data, train_labels, validation_split = 0.2, epochs = 3, batch_size = 10)

# Evaluate on test data 
model.evaluate(test_data, test_labels, 10)
```

## **Comprensión y mejora de las redes convolucionales profundas**

### Gráfico de las curvas de aprendizaje

Durante el aprendizaje, el modelo almacenará la función de pérdida evaluada en cada época. Observar las curvas de aprendizaje nos puede dar mucha información sobre el proceso de aprendizaje.

```python
import matplotlib.pyplot as plt

# Train the model and store the training object
training = model.fit(train_data, train_labels, validation_split = 0.2, epochs = 3, batch_size = 10)

# Extract the history from the training object
history = training.history

# Plot the training loss 
plt.plot(history['loss'])
# Plot the validation loss
plt.plot(history['val_loss'])

# Show the figure
plt.show()
```

### Uso de pesos almacenados para realizar predicciones en un conjunto de prueba

Los pesos del modelo almacenados en un archivo « `hdf5` » se pueden reutilizar para rellenar un modelo sin entrenar. Una vez que los pesos se cargan en este modelo, se comporta igual que un modelo que ha sido entrenado para alcanzar estos pesos. Por ejemplo, puedes utilizar este modelo para realizar predicciones a partir de un conjunto de datos desconocido (por ejemplo, `test_data`).

```python
# Load the weights from file
model.load_weights('weights.hdf5')

# Predict from the first three images in the test data
np.argmax(model.predict(test_data[:3]), axis=1)
```

### Añadir dropout a tu red

El dropout es una forma de regularización que elimina un subconjunto aleatorio diferente de las unidades de una capa en cada ronda de entrenamiento.

```python
# Add a convolutional layer
model.add(Conv2D(15, kernel_size=2, activation='relu', 
                 input_shape=(img_rows, img_cols, 1)))

# Add a dropout layer
model.add(Dropout(0.20))

# Add another convolutional layer
model.add(Conv2D(5, kernel_size=2, activation='relu'))

# Flatten and feed to output layer
model.add(Flatten())
model.add(Dense(3, activation='softmax'))
```

### Añade normalización por lotes a tu red

La normalización por lotes es otra forma de regularización que reescala las salidas de una capa para garantizar que tengan una media de 0 y una desviación estándar de 1

```python
# Add a convolutional layer
model.add(Conv2D(15, kernel_size=2, activation="relu", input_shape=(img_rows, img_cols, 1)))

# Add batch normalization layer
model.add(BatchNormalization())

# Add another convolutional layer
model.add(Conv2D(5, kernel_size=2, activation='relu'))

# Flatten and feed to output layer
model.add(Flatten())
model.add(Dense(3, activation='softmax'))
```

### Extracción de un núcleo de una red entrenada

Una forma de interpretar los modelos es examinar las propiedades de los núcleos en las capas convolucionales

```python
# Load the weights into the model
model.load_weights('weights.hdf5')

# Get the first convolutional layer from the model
c1 = model.layers[0]

# Get the weights of the first convolutional layer
weights1 = c1.get_weights()

# Pull out the first channel of the first kernel in the first layer
kernel = weights1[0][:, :,0, 0]
print(kernel)
```

### Forma de los pesos

Una red neuronal de tipo «`Keras`» almacena sus capas en una lista denominada «`model.layers`». Para las capas convolucionales, puedes obtener los pesos utilizando el método «`.get_weights()`». Esto devuelve una lista, y el primer elemento de esta lista es un array que representa los pesos de los núcleos convolucionales. Si la forma de este arreglo es `(2, 2, 1, 5)`, ¿qué representa el primer número (`2`)?

Ans → El tamaño del núcleo es de 2x2

### Visualización de las respuestas del núcleo

Una de las formas de interpretar los pesos de una red neuronal es ver cómo los núcleos almacenados en estos pesos «ven» el mundo. Es decir, qué propiedades de una imagen se enfatizan con este núcleo.

```python
import matplotlib.pyplot as plt

# Convolve with the fourth image in test_data
out = convolution(test_data[3, :, :, 0], kernel)

# Visualize the result
plt.imshow(out)
plt.show()
```