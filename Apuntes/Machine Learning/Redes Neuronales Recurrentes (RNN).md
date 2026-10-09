#DeepLearning 
# Diapositivas

[chapter1-3.pdf](attachment:9ac5874f-2712-4e72-bfa2-96d21516d35d:chapter1-3.pdf)

[chapter2-3.pdf](attachment:e65c63c3-0f46-47c1-8bd6-c0cd677e9eb8:chapter2-3.pdf)

[chapter3.pdf](attachment:ab3c28a0-3b03-44e8-9b51-2493dbb4cab4:chapter3.pdf)

[chapter4-3.pdf](attachment:f10634cb-3a5a-448c-834c-a111a864dc91:chapter4-3.pdf)

# Apuntes

## Recurrent Neural Networks and Keras

### Introduction to the course

#### Análisis de sentimiento

En el ejercicio del vídeo, has visto las diversas aplicaciones de los modelos de secuencia a secuencia. En este ejercicio verás cómo utilizar un modelo preentrenado para el análisis de sentimientos.

El modelo está precargado en el entorno en la variable `model`. Además, también están disponibles las variables del conjunto de pruebas tokenizadas `X_test` y `y_test`, así como los datos de texto originales preprocesados `sentences` de _IMDb_. Más adelante en el curso, aprenderás a reprocesar los datos de texto y a crear y entrenar el modelo utilizando _Keras_.

Utilizarás el modelo preentrenado para obtener predicciones de sentimiento. El modelo devuelve un número entre cero y uno que representa la probabilidad de que la frase tenga un sentimiento  
positivo. Por lo tanto, crearás una regla de decisión para establecer la predicción como positiva o negativa.

```python
# Inspect the first sentence on `X_test`
print(X_test[0])

# Get the predicion for all the sentences
pred = model.predict(X_test)

# Transform the predition into positive (> 0.5) or negative (<= 0.5)
pred_sentiment = ["positive" if x > 0.5 else "negative" for x in pred]

# Create a data frame with sentences, predictions and true values
result = pd.DataFrame({'sentence': sentences, 'y_pred': pred_sentiment, 'y_true': y_test})

# Print the first lines of the data frame
print(result.head())
```

#### Modelos secuencia a secuencia

En el ejercicio del vídeo, aprendiste cuatro tipos de modelos de secuencia a secuencia: muchos a uno (clasificación) y muchos a muchos (generación de texto, traducción automática neuronal y modelos de lenguaje). En ste ejercicio, debes elegir el tipo de modelo correcto dada la siguiente descripción del problema:

Estás ayudando a tu amigo, que es especialista en reconocimiento de voz. Tu amigo ha creado un modelo que puede reconocer diferentes acentos del inglés, pero el modelo no distingue _entre homófonos_, es decir, palabras que se pronuncian igual pero tienen significados diferentes, como «sea» y «see» o «write» y «right».

Proponés utilizar un modelo que utilice el contexto que rodea a las palabras para identificar su significado semántico. Al aprender el significado de las palabras, el nuevo modelo evitaría resultados como «¿Has visto ese coche?», ya que identificaría que, en este caso, la  
palabra correcta sería «ver».

¿Qué tipo de modelo secuencia a secuencia es el adecuado?

Ans → muchos a muchos, este problema se puede resolver con un modelo de lenguaje

### Introduction to languaje models

Before training the model, it is necessary to prepare the text that we will give the model.

#### Acostumbrarse a los datos de texto

En este ejercicio, jugarás con datos de texto analizando citas de Sheldon Cooper en la serie de televisión The Big Bang Theory. Esto te dará la oportunidad de analizar frases para obtener información sobre cómo es trabajar con datos de texto del mundo real.

Utilizarás comprensiones de diccionario para crear diccionarios que asignan palabras a índices y viceversa. El uso de diccionarios en lugar de, por ejemplo, un « `pandas.DataFrame` » se debe a que son más intuitivos y no añaden complejidad innecesaria.

Los datos están disponibles en `sheldon_quotes` con las dos primeras frases ya impresas.

```python
# Transform the list of sentences into a list of words
all_words = ' '.join(sheldon_quotes).split(' ')

# Get number of unique words
unique_words = list(set(all_words))

# Dictionary of indexes as keys and words as values
index_to_word = {i: wd for i, wd in enumerate(sorted(unique_words))}

print(index_to_word)

# Dictionary of words as keys and indexes as values
word_to_index = {wd: i for i, wd in enumerate(sorted(unique_words))}

print(word_to_index)
```

#### Preparación de datos de texto para la entrada del modelo

Anteriormente, aprendiste a crear diccionarios de índices de palabras y viceversa. En este ejercicio, dividirás el texto por caracteres y continuarás preparando los datos para el aprendizaje supervisado.

Dividir los textos en caracteres puede parecer extraño, pero es algo que se hace a menudo para generar texto. Además, el proceso para preparar los datos es el mismo, el único cambio es cómo dividir los textos.

Crearás los datos de entrenamiento que contienen una lista de textos de longitud fija y sus etiquetas, que son los caracteres siguientes correspondientes.

Seguirás utilizando el conjunto de datos que contiene citas de Sheldon (The Big Bang Theory), disponible en la variable `sheldon_quotes`.

La función « `print_examples()` » imprime los pares para que puedas ver cómo se han transformado los datos. Para obtener más información, visita `help()`.

```python
# Create lists to keep the sentences and the next character
sentences = []   # ~ Training data
next_chars = []  # ~ Training labels

# Define hyperparameters
step = 2          # ~ Step to take when reading the texts in characters
chars_window = 10 # ~ Number of characters to use to predict the next one  

# Loop over the text: length `chars_window` per time with step equal to `step`
for i in range(0, len(sheldon_quotes) - chars_window, step):
    sentences.append(sheldon_quotes[i:i + chars_window])
    next_chars.append(sheldon_quotes[i + chars_window])

# Print 10 pairs
print_examples(sentences, next_chars, 10)
```

#### Transformar texto nuevo

En este ejercicio, transformarás un nuevo texto en secuencias de índices numéricos en los diccionarios creados anteriormente.

Esto resulta útil cuando ya tienes un modelo entrenado y deseas aplicarlo a un nuevo conjunto de datos. Los pasos de preprocesamiento realizados en los datos de entrenamiento también deben aplicarse al nuevo texto, para que el modelo pueda realizar predicciones/clasificaciones.

Aquí también utilizarás un símbolo especial, `'<UKN/>'`, para representar palabras que no están en el vocabulario. Normalmente, estos tokens especiales son los primeros índices de los diccionarios, la posición `0`.

Las variables `word_to_index`, `index_to_word` y `vocabulary` ya están cargadas en el entorno. Además, la variable con el nuevo texto también se carga como `new_text`. El nuevo texto ha sido impreso para que puedas echarle un vistazo.

```python
# Loop through the sentences and get indexes
new_text_split = []
for sentence in new_text:
    sent_split = []
    for wd in sentence.split(' '):
        index = word_to_index.get(wd, 0)
        sent_split.append(index)
    new_text_split.append(sent_split)

# Print the first sentence's indexes
print(new_text_split[0])

# Print the sentence converted using the dictionary
print(' '.join([index_to_word[index] for index in new_text_split[0]]))
```

### Introduction to RNN inside Keras

En este ejercicio practicarás el uso de dos clases del módulo `keras.models`. Crearás un modelo utilizando la clase `Sequential` y otro modelo con la clase `Model`.

La clase « `Sequential` » es más fácil de usar porque se supone que las capas están ordenadas, mientras que la clase « `Model` » es más flexible y permite múltiples entradas, múltiples salidas y capas compartidas (pesos compartidos).

La clase Model`necesita declarar explícitamente la capa de entrada, mientras que en la clase`Sequential , esto se hace con el parámetro input_shape .

Los objetos y módulos `Sequential`, `Model`, `Dense`, `Input`, `LSTM` y `np` (`numpy`) ya están cargados en el entorno.

```python
# Instantiate the class
model = Sequential(name="sequential_model")

# One LSTM layer (defining the input shape because it is the 
# initial layer)
model.add(LSTM(128, input_shape=(None, 10), name="LSTM"))

# Add a dense layer with one unit
model.add(Dense(1, activation="sigmoid", name="output"))

# The summary shows the layers and the number of parameters 
# that will be trained
model.summary()
```

```python
# Define the input layer
main_input = Input(shape=(None, 10), name="input")

# One LSTM layer (input shape is already defined)
lstm_layer = LSTM(128, name="LSTM")(main_input)

# Add a dense layer with one unit
main_output = Dense(1, activation="sigmoid", name="output")(lstm_layer)

# Instantiate the class at the end
model = Model(inputs=main_input, outputs=main_output, name="modelclass_model")

# Same amount of parameters to train as before (71,297)
model.summary()
```

#### Preprocesamiento con Keras

El segundo módulo más importante de Keras es `keras.preprocessing`. Verás cómo utilizar los módulos y funciones más importantes para preparar los datos sin procesar y darles la forma correcta para su introducción. Keras proporciona funcionalidades que sustituyen el enfoque del diccionario que aprendiste anteriormente.

Utilizarás el módulo `keras.preprocessing.text.Tokenizer` para crear un diccionario de palabras utilizando el método `.fit_on_texts()` y cambiarás los textos a identificadores numéricos que representan el índice de cada palabra en el diccionario utilizando el método `texts_to_sequences()`.

A continuación, utiliza la función « `.pad_sequences()` » (Reducir secuencias de caracteres) de `keras.preprocessing.sequence` para que todas las secuencias tengan el mismo tamaño (necesario para el modelo) añadiendo ceros en los textos pequeños y recortando los grandes.

```python
# Import relevant classes/functions
from tensorflow.keras.preprocessing.text import Tokenizer
from tensorflow.keras.preprocessing.sequence import pad_sequences

# Build the dictionary of indexes
tokenizer = Tokenizer()
tokenizer.fit_on_texts(texts)

# Change texts into sequence of indexes
texts_numeric = tokenizer.texts_to_sequences(texts)
print("Number of words in the sample texts: ({0}, {1})".format(len(texts_numeric[0]), len(texts_numeric[1])))

# Pad the sequences
texts_pad = pad_sequences(texts_numeric, 60)
print("Now the texts have fixed length: 60. Let's see the first one: \n{0}".format(texts_pad[0]))
```

#### Tu primer modelo RNN

En este ejercicio pondrás en práctica los módulos de Keras para construir tu primer modelo de aprendizaje automático ( `RNN` ) y lo utilizarás para clasificar el sentimiento en reseñas de películas.

Este primer modelo tiene una capa recurrente con la celda e `RNN`: `SimpleRNN`, y la capa de salida con dos valores posibles: `0` que representa el sentimiento negativo y `1` que representa el sentimiento positivo.

Utilizarás el conjunto de datos « `IMDB` » (Datos de la encuesta sobre la salud de la población de California) que se encuentra en `keras.datasets`. Ya se ha entrenado un modelo y sus pesos se han almacenado en el archivo `model_weights.h5`. Construirás la arquitectura del modelo y utilizarás las variables precargadas `x_test` y `y_test` para comprobar su rendimiento.

```python
# Build model
model = Sequential()
model.add(SimpleRNN(units=128, input_shape=(None, 1)))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', 
              optimizer='adam',
              metrics=['accuracy'])

# Load pre-trained weights
model.load_weights('model_weights.h5')

# Method '.evaluate()' shows the loss and accuracy
loss, acc = model.evaluate(x_test, y_test, verbose=0)
print("Loss: {0} \nAccuracy: {1}".format(loss, acc))
```

## RNN Architecture

### Vanishing and exploding gradients

#### Problema de gradiente explosivo

En el ejercicio del vídeo, has aprendido dos problemas que pueden surgir al trabajar con modelos RNN: los problemas de gradiente desaparecido y gradiente explosivo.

Este ejercicio explora el problema del gradiente explosivo, mostrando que la derivada de una función puede aumentar exponencialmente, y cómo esolverlo con una técnica sencilla.

Los datos ya están cargados en el entorno como `X_train`, `X_test`, `y_train` y `y_test`.

Utilizarás un optimizador **de descenso estocástico** (SGD) y **el error cuadrático medio** (MSE) como función de pérdida.

En el primer paso, observarás cómo explota el gradiente al calcular el MSE en los conjuntos de entrenamiento y prueba. En el paso 2, cambiarás el optimizador utilizando el parámetro « `clipvalue` » (Optimizador de tipo) para resolver el problema.

El descenso estocástico por gradiente en Keras se carga como `SGD`.

```python
# Create a Keras model with one hidden Dense layer
model = Sequential()
model.add(Dense(25, input_dim=20, activation='relu', kernel_initializer=he_uniform(seed=42)))
model.add(Dense(1, activation='linear'))

# Compile and fit the model
model.compile(loss='mean_squared_error', optimizer=SGD(learning_rate=0.01, momentum=0.9))
history = model.fit(X_train, y_train, validation_data=(X_test, X_train), epochs=100, verbose=0)

# See Mean Square Error for train and test data
train_mse = model.evaluate(X_train, y_train, verbose=0)
test_mse = model.evaluate(X_test, y_test, verbose=0)

# Print the values of MSE
print('Train: %.3f, Test: %.3f' % (train_mse, test_mse))

-------------------------

# Create a Keras model with one hidden Dense layer
model = Sequential()
model.add(Dense(25, input_dim=20, activation='relu', kernel_initializer=he_uniform(seed=42)))
model.add(Dense(1, activation='linear'))

# Compile and fit the model
model.compile(loss='mean_squared_error', optimizer=SGD(learning_rate=0.01, momentum=0.9, clipvalue=3.0))
history = model.fit(X_train, y_train, validation_data=(X_test, y_test), epochs=100, verbose=0)

# See Mean Square Error for train and test data
train_mse = model.evaluate(X_train, y_train, verbose=0)
test_mse = model.evaluate(X_test, y_test, verbose=0)

# Print the values of MSE
print('Train: %.3f, Test: %.3f' % (train_mse, test_mse))
```

#### Problema del gradiente desaparecido

El otro problema posible con los gradientes es cuando estos desaparecen o se reducen a cero. Este es un problema mucho más difícil de resolver porque no es tan fácil de detectar. Si la función de pérdida no mejora en cada paso, ¿es porque los gradientes llegaron a cero y, por lo tanto,  
no actualizaron los pesos? ¿O es porque el modelo no es capaz de aprender?

Este problema se produce con mayor frecuencia en los modelos RNN cuando se requiere una memoria larga (con frases largas).

En este ejercicio observarás el problema en los datos _de IMDB_, con frases más largas eleccionadas. Los datos se cargan en las variables `X` y `y`, así como en las clases `Sequential`, `SimpleRNN`, `Dense` y `matplotlib.pyplot` como `plt`. El modelo se entrenó previamente con 100 épocas, y sus pesos e historial se almacenan en el archivo `model_weights.h5` y en la variable `history`.

```python
# Create the model
model = Sequential()
model.add(SimpleRNN(units=600, input_shape=(None, 1)))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', optimizer='sgd', metrics=['accuracy'])

# Load pre-trained weights
model.load_weights('model_weights.h5')

# Plot the accuracy x epoch graph
plt.plot(history.history['acc'])
plt.plot(history.history['val_acc'])
plt.legend(['train', 'val'], loc='upper left')
plt.show()
```

### GRU and LSTM cells

#### Las células GRU son mejores que las RNN simples.

En este ejercicio volverás a ejecutar el mismo modelo que en el primer capítulo del curso para comparar la precisión del modelo simplemente cambiando la celda « `SimpleRNN` » por « `GRU` ».

El modelo ya se había entrenado con 10 épocas, al igual que en el modelo anterior con una celda e `SimpleRNN`. Para comparar los modelos, ya se ha cargado en el entorno un conjunto de prueba `(x_test, y_test)`, así como el modelo antiguo `SimpleRNN_model`.

```python
# Import the modules
from tensorflow.keras.layers import GRU, Dense

# Print the old and new model summaries
SimpleRNN_model.summary()
gru_model.summary()

# Evaluate the models' performance (ignore the loss value)
_, acc_simpleRNN = SimpleRNN_model.evaluate(X_test, y_test, verbose=0)
_, acc_GRU = gru_model.evaluate(X_test, y_test, verbose=0)

# Print the results
print("SimpleRNN model's accuracy:\t{0}".format(acc_simpleRNN))
print("GRU model's accuracy:\t{0}".format(acc_GRU))
```

#### Apilamiento de capas RNN

Los modelos RNN profundos pueden tener entre decenas y cientos de capas para lograr resultados de vanguardia.

En este ejercicio, obtendrás una visión general de cómo crear modelos RNN profundos apilando capas de celdas _LSTM_ una tras otra.

`LSTM` Para ello, establecerás el argumento « `return_sequences` » (Ajustar a la altura de la capa superior) en « `True` » (Ajustar a la altura de la capa superior) en las dos primeras capas « `LSTM` » (Ajustar a la altura de la capa superior) y en « `False` » (Ajustar a la altura de la capa inferior)

Para crear modelos con aún más capas, puedes seguir añadiéndolas una tras otra o crear una función que utilice el método « `.add()` » dentro de un bucle para añadir muchas capas con pocas líneas de código.

```python
# Import the LSTM layer
from tensorflow.keras.layers import LSTM

# Build model
model = Sequential()
model.add(LSTM(units=128, input_shape=(None, 1), return_sequences=True))
model.add(LSTM(units=128, return_sequences=True))
model.add(LSTM(units=128, return_sequences=False))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

# Load pre-trained weights
model.load_weights('lstm_stack_model_weights.h5')

print("Loss: %0.04f\nAccuracy: %0.04f" % tuple(model.evaluate(X_test, y_test, verbose=0)))
```

### The embedding layer

#### Comparación del número de parámetros

Has visto que la representación one-hot no es una buena representación de las palabras porque es muy escasa. El uso de la capa « `Embedding` » crea una representación densa de los vectores, pero también requiere aprender muchos parámetros.

En este ejercicio compararás el número de parámetros de dos modelos utilizando la codificación `embeddings` y `one-hot` para ver la diferencia.

El modelo `model_onehot` ya está cargado en el entorno, al igual que `Sequential`, `Dense` y `GRU` de `keras`. Por último, también se cargan los parámetros `vocabulary_size=80000` y `sentence_len=200`.

```python
# Import the embedding layer
from tensorflow.keras.layers import Embedding

# Create a model with embeddings
model = Sequential(name="emb_model")
model.add(Embedding(input_dim=vocabulary_size + 1, output_dim=wordvec_dim, input_length=sentence_len, trainable=True))
model.add(GRU(128))
model.add(Dense(1))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

# Print the summaries of the one-hot model
model_onehot.summary()

# Print the summaries of the model with embeddings
model.summary()
```

#### Aprendizaje por transferencia

Has visto que, al entrenar una capa de incrustación, es necesario aprender muchos parámetros.

En este ejercicio, verás que al utilizar el aprendizaje por transferencia es posible utilizar los pesos preentrenados y no actualizarlos, lo que significa que todos los parámetros de la capa de  
incrustación serán fijos y el modelo solo tendrá que aprender los parámetros de las otras capas.

La función `load_glove` ya está cargada en el entorno y recupera la matriz del guante como un vector `numpy.ndarray`. Utiliza la función explicada en las diapositivas de la lección para  
recuperar los vectores glove con 200 dimensiones de incrustación para el vocabulario presente en este ejercicio.

```python
# Load the glove pre-trained vectors
glove_matrix = load_glove('glove_200d.zip')

# Create a model with embeddings
model = Sequential(name="emb_model")
model.add(Embedding(input_dim=vocabulary_size + 1, output_dim=wordvec_dim, 
                    embeddings_initializer=Constant(glove_matrix), 
                    input_length=sentence_len, trainable=False))
model.add(GRU(128))
model.add(Dense(1, ))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

# Print the summaries of the model with embeddings
model.summary()
```

#### Las incrustaciones mejoran el rendimiento.

¿La capa de incrustación mejora la precisión del modelo? Veámoslo en los mismos datos de IMDB.

El modelo ya se había entrenado con 10 épocas, al igual que en el modelo anterior con una celda de tipo « `simpleRNN` ». Para comparar los modelos, hay disponible un conjunto de prueba `(X_test, y_test)` en el entorno, así como el modelo antiguo `simpleRNN_model`. La precisión del modelo antiguo se carga en la variable `acc_SimpleRNN`.

Todos los módulos y funciones necesarios cargados en el entorno: `Sequential()` desde `keras.models`, `Embedding` y `Dense` desde `keras.layers` y `SimpleRNN` desde `keras.layers`.

```python
# Create the model with embedding
model = Sequential(name="emb_model")
model.add(Embedding(input_dim=max_vocabulary, output_dim=wordvec_dim, input_length=max_len))
model.add(SimpleRNN(units=128))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

# Load pre-trained weights
model.load_weights('embedding_model_weights.h5')

# Evaluate the models' performance (ignore the loss value)
_, acc_embeddings = model.evaluate(X_test, y_test, verbose=0)

# Print the results
print("SimpleRNN model's accuracy:\t{0}\nEmbeddings model's accuracy:\t{1}".format(acc_simpleRNN, acc_embeddings))
```

### Sentiment classification revisited

#### Mejor clasificación del sentimiento

En este ejercicio, volverás al problema de clasificación de sentimientos visto en el capítulo 1.

Vas a añadir más complejidad al modelo y mejorar su precisión. Utilizarás una capa « `Embedding` » para entrenar vectores de palabras en el conjunto de entrenamiento y dos capas « `LSTM` » para realizar un seguimiento de los textos más largos. Además, añadirás una capa adicional « `Dense` » antes de la salida.

Ya no se trata de un modelo sencillo, y la formación puede llevar algún tiempo. Por este motivo, hay disponible un modelo preentrenado que se puede cargar con el método « `.load_weights()` » de la clase « `keras.models.Sequential` ». El modelo se entrenó con 10 épocas y sus pesos están disponibles en el archivo `model_weights.h5`.

Los siguientes módulos están cargados en el entorno: `Sequential`, `Embedding`, `LSTM`, `Dropout`, `Dense`.

```python
# Build and compile the model
model = Sequential()
model.add(Embedding(vocabulary_size, wordvec_dim, trainable=True, input_length=max_text_len))
model.add(LSTM(64, return_sequences=64, dropout=0.2, recurrent_dropout=0.15))
model.add(LSTM(64, return_sequences=False, dropout=0.2, recurrent_dropout=0.15))
model.add(Dense(16))
model.add(Dropout(rate=0.25))
model.add(Dense(1, activation='sigmoid'))
model.compile(loss='binary_crossentropy', optimizer='adam', metrics=['accuracy'])

# Load pre-trained weights
model.load_weights('model_weights.h5')

# Print the obtained loss and accuracy
print("Loss: {0}\nAccuracy: {1}".format(*model.evaluate(X_test, y_test, verbose=0)))
```

#### Uso de la capa CNN

En este ejercicio, utilizarás un modelo preentrenado que utiliza las capas « `Conv1D` » y « `MaxPooling1D` » del módulo « `keras.layers.convolutional` » y consigue una precisión aún mayor en la tarea de clasificación.

Esta arquitectura obtuvo buenos resultados en tareas de modelado del lenguaje, como la clasificación, y se añade aquí como ejercicio adicional para verla en acción y adquirir cierta intuición.

Dado que esta capa no forma parte del alcance del curso, te centrarás en cómo utilizar las capas junto con las capas RNN que ya has aprendido.

Sigue las instrucciones para ver los resultados.

```python
# Print the model summary
model_cnn.summary()

# Load pre-trained weights
model_cnn.load_weights('model_weights.h5')

# Evaluate the model to get the loss and accuracy values
loss, acc = model_cnn.evaluate(x_test, y_test, verbose=0)

# Print the loss and accuracy obtained
print("Loss: {0}\nAccuracy: {1}".format(loss, acc))
```

## Multi-Class Classification

### Data pre-processing

#### Prepara vectores de etiquetas

En el ejercicio del vídeo, has aprendido las diferencias entre la clasificación binaria y la clasificación multiclase. Has aprendido que hay algunas modificaciones que deben realizarse en el proceso de  
preparación de datos antes de entrenar los modelos.

En este ejercicio, prepararás un conjunto de datos sin procesar con etiquetas en forma de texto. Los datos se proporcionan en un archivo de texto sin formato ( `pandas.DataFrame` ) llamado `df`, con dos columnas: `text` con los datos de texto y `label` con los nombres de las etiquetas. Tu tarea consiste en realizar todas as transformaciones necesarias en las etiquetas: cambiar la cadena por un número y codificarla con codificación one-hot.

El módulo `pandas` como `pd` y la función `to_categorical()` de `keras.utils.np_utils` ya están cargados en el entorno y las primeras líneas del conjunto de datos se imprimen en la consola para que puedas verlas.

```python
# Get the numerical ids of column label
numerical_ids = df.label.cat.codes

# Print initial shape
print(numerical_ids.shape)

# One-hot encode the indexes
Y = to_categorical(numerical_ids)

# Check the new shape of the variable
print(Y.shape)

# Print the first 5 rows
print(Y[0:5])
```

#### Preprocesar datos

Has aprendido las diferencias en el preprocesamiento de datos en el caso de la clasificación multiclase. Pongámoslo en práctica preprocesando los datos con vistas a crear un modelo sencillo de clasificación multiclase.

El conjunto de datos se carga en la variable `news_dataset` y tiene los siguientes atributos:

- `news_dataset.data`: array con textos
- `news_dataset.target`: array con categorías objetivo como índices numéricos

Los datos de muestra contienen 5000 observaciones.

```python
# Create and fit tokenizer
tokenizer = Tokenizer()
tokenizer.fit_on_texts(news_dataset.data)

# Prepare the data
prep_data = tokenizer.texts_to_sequences(news_dataset.data)
prep_data = pad_sequences(prep_data, maxlen=200)

# Prepare the labels
target_labels = to_categorical(news_dataset.target)

# Print the shapes
print(prep_data.shape)
print(target_labels.shape)
```

### Transfer Learning for language models

#### Punto de partida del aprendizaje por transferencia

En este ejercicio, verás las ventajas de utilizar vectores preentrenados como punto de partida para tu modelo.

Compararás la precisión de dos modelos entrenados con dos épocas. La arquitectura de los modelos es la misma: Una capa de incrustación, una capa de red neuronal ( `LSTM` ) con 128 unidades y la capa de salida con 5 unidades, que es el número de clases en los datos de muestra. La diferencia es que un modelo utiliza vectores preentrenados en la capa de incrustación (aprendizaje por transferencia) y el otro no.

Los vectores preentrenados utilizados fueron los vectores preentrenados de Google ( `GloVE`  
) con 200 dimensiones. El historial de precisión del entrenamiento del conjunto de validación de ambos modelos está disponible en las variables `history_no_emb` y `history_emb`.

```python
# Import plotting package
import matplotlib.pyplot as plt 

# Insert lists of accuracy obtained on the validation set
plt.plot(history_no_emb['acc'], marker='o')
plt.plot(history_emb['acc'], marker='o')

# Add extra descriptions to plot
plt.title('Learning with and without pre-trained embedding vectors')
plt.ylabel('accuracy')
plt.xlabel('epoch')
plt.legend(['no_embeddings', 'with_embeddings'], loc='upper left')

# Display the plot
plt.show()
```

![[Pasted image 20260806094328.png|419]]

#### Word2Vec

En este ejercicio crearás un modelo Word2Vec utilizando _Keras_.

El corpus utilizado para preentrenar el modelo es el guion de todos los episodios de la serie de televisión _The Big Bang Theory_, dividido frase por frase. Está disponible en la variable `bigbang`.

El texto del corpus se transformó a minúsculas y todas las palabras se tokenizaron. El resultado se almacena en la variable « `tokenized_corpus` ».

Se preentrenó un modelo de red de vocabulario con dimensión reducida ( `Word2Vec` ) utilizando un tamaño de ventana de 10 palabras para el contexto (5 antes y 5 después de la palabra central), se eliminaron las palabras con menos de 3 ocurrencias y se utilizó el método del modelo skip-gram con 50 dimensiones. El modelo se guarda en el archivo `bigbang_word2vec.model`.

La clase `Word2Vec` ya está cargada en el entorno desde `gensim.models.word2vec`.

```python
# Word2Vec model
w2v_model = Word2Vec.load('bigbang_word2vec.model')

# Selected words to check similarities
words_of_interest = ["bazinga", "penny", "universe", "spock", "brain"]

# Compute top 5 similar words for each of the words of interest
top5_similar_words = []
for word in words_of_interest:
    top5_similar_words.append(
      {word: [item[0] for item in w2v_model.wv.most_similar([word], topn=5)]}
    )

# Print the similar words
print(top5_similar_words)
```

### Multi-class classification models

#### Exploración del conjunto de datos 20 News Groups

En este ejercicio, se te proporcionará una muestra del conjunto de datos **20 News Groups** obtenido mediante la función `fetch_20newsgroups()` de `sklearn.datasets`, filtrando solo tres clases: `sci.space`, `alt.atheism` y `soc.religion.christian`.

El conjunto de datos se carga en la variable `news_dataset`. Sus atributos están impresos para que puedas explorarlos en la consola.

Para obtener más información sobre cómo utilizar esta función, consulta la [documentación de Sklearn](https://scikit-learn.org/0.19/datasets/twenty_newsgroups.html).

Tokenizarás los textos y codificarás las etiquetas paso a paso con codificación one-hot para comprender cómo se producen las transformaciones.

```python
# See example article
print(news_dataset.data[5])

# Transform the text into numerical indexes
news_num_indices = tokenizer.texts_to_sequences(news_dataset.data)

# Print the transformed example article
print(news_num_indices[5])

# Transform the labels into one-hot encoded vectors
labels_onehot = to_categorical(news_dataset.target)

# Check before and after for the sample article
print("Before: {0}\nAfter: {1}".format(news_dataset.target[5], labels_onehot[5]))
```

#### Clasificación de artículos periodísticos

En este ejercicio crearás un modelo de clasificación multiclase.

El conjunto de datos ya está cargado en el entorno como `news_novel`. Además, todo el preprocesamiento de los datos de entrenamiento ya está realizado y también está disponible en el entorno un modelo de entrenamiento denominado « `tokenizer` ».

Se preentrenó un modelo RNN con la siguiente arquitectura: se utilizó la capa « `Embedding` », una capa « `LSTM` » y la capa de salida « `Dense` », que esperaba tres clases: « `sci.space` », « `lt.atheism` » y « `soc.religion.christian` ». Los pesos de este modelo entrenado están disponibles en el archivo « `classify_news_weights.h5` ».

Preprocesarás los datos nuevos y los evaluarás en un nuevo conjunto de datos `news_novel`.

```python
# Change text for numerical ids and pad
X_novel = tokenizer.texts_to_sequences(news_novel.data)
X_novel = pad_sequences(X_novel, maxlen=400)

# One-hot encode the labels
Y_novel = to_categorical(news_novel.target)

# Load the model pre-trained weights
model.load_weights('classify_news_weights.h5')

# Evaluate the model on the new dataset
loss, acc = model.evaluate(X_novel, Y_novel, batch_size=64)

# Print the loss and accuracy obtained
print("Loss:\t{0}\nAccuracy:\t{1}".format(loss, acc))
```

### Assessing the model’s performances

#### Compromiso entre precisión y recuperación

Cuando se trabaja con tareas de clasificación, a menudo aparece el término _«compromiso entre precisión y recuperación_ ». ¿De dónde viene?

Por lo general, se elige la clase con mayor probabilidad para asignar el documento. Pero, ¿qué pasa si la probabilidad máxima es igual a `0.1`? ¿Deberías considerar que ese documento pertenece a esta clase con solo un 10 % de probabilidad?

La respuesta varía según el problema en cuestión. Es posible añadir un umbral mínimo para aceptar la clasificación, y al cambiar el umbral, los valores de precisión y recuperación se mueven en direcciones opuestas.

Se cargan las variables `y_true` y el modelo `model`. Además, si la probabilidad es inferior al umbral, el documento se asignará a « `DEFAULT_CLASS` » (elegido como clase « `2` »).

```python
# Get probabilities for each class
pred_probabilities = model.predict(X_test)

# Thresholds at 0.5 and 0.8
y_pred_50 = [np.argmax(x) if np.max(x) >= 0.5 else DEFAULT_CLASS for x in pred_probabilities]
y_pred_80 = [np.argmax(x) if np.max(x) >= 0.8 else DEFAULT_CLASS for x in pred_probabilities]

trade_off = pd.DataFrame({
    'Precision_50': precision_score(y_true, y_pred_50, average=None), 
    'Precision_80': precision_score(y_true, y_pred_80, average=None), 
    'Recall_50': recall_score(y_true, y_pred_50, average=None), 
    'Recall_80': recall_score(y_true, y_pred_80, average=None)}, 
  index=['Class 1', 'Class 2', 'Class 3'])

print(trade_off)
```

#### Precisión o recuperación, esa es la cuestión

Has aprendido algunas métricas de rendimiento y quizá te preguntes: ¿cuándo debo usar _la ‘precision’_ y cuándo _la ‘recall’_? Esas dos métricas se calculan para cada clase y, a veces, es difícil saber cuándo centrarse en una y cuándo en la otra.

_La precisión_ es una métrica que mide la eficacia con la que el _modelo_ predice una clase, mientras que _la recuperación_ mide la eficacia con la que se clasifica una _clase_. Si _la precisión_ es alta para una clase, puedes confiar en tu modelo cuando predice esa clase. Cuando _el recuerdo_ es alto para una clase, puedes estar seguro de que el modelo la ha comprendido bien.

Sigue las instrucciones para ver esta comparación entre _precisión_ y **recall** **con un ejemplo. Se cargan las funciones `precision_score()` y `recall_score()`.

```python
# Compute the precision of the sentiment model
prec_sentiment = precision_score(sentiment_y_true, sentiment_y_pred, average=None)
print(prec_sentiment)

# Compute the recall of the sentiment model
rec_sentiment = recall_score(sentiment_y_true, sentiment_y_pred, average=None)
print(rec_sentiment)
```

#### Rendimiento en la clasificación multiclase

En este ejercicio, calcularás las métricas de rendimiento de los modelos utilizando el módulo `sklearn.metrics`.

El modelo ya está entrenado y almacenado en la variable `model`. Además, también se cargan las variables `X_test` y `y_true`, junto con las funciones `confusion_matrix()` y `classification_report()` del paquete `sklearn.metrics`.

Primero calcularás la _matriz de confusión_ del modelo. A continuación, para resumir el rendimiento de un modelo, calcularás la _precisión_, _la recuperación_ y _la puntuación F1_ utilizando la función « `classification_report()` ». En esta función, puedes pasar opcionalmente un `list` que contiene los nombres de las clases (se almacenan en la variable `news_cat` ) al parámetro `target_names` para que el informe sea más legible.

```python
# Use the model to predict on new data
predicted = model.predict(X_test)

# Choose the class with higher probability 
y_pred = np.argmax(predicted, axis=1)

# Compute and print the confusion matrix
print(confusion_matrix(y_true, y_pred))

# Create the performance report
print(classification_report(y_true, y_pred, target_names=news_cat))
```

## Sequence to Sequence Models

#### Ejemplos de generación de texto

En este ejercicio, vas a experimentar con dos modelos preentrenados para la generación de texto.

El primer modelo generará una frase basada en el personaje Sheldon de la serie de televisión The Big Bang Theory, y el segundo modelo generará un poema de Shakespeare de hasta 400 caracteres.

Los modelos se cargan en las variables `sheldon_model` y `poem_model`. Además, hay disponibles dos funciones personalizadas para ayudar a generar texto: `generate_sheldon_phrase()` y `generate_poem()`. Ambos reciben el modelo preentrenado y una cadena de contexto como parámetros.

```python
# Context for Sheldon phrase
sheldon_context = "I’m not insane, my mother had me tested. "

# Generate one Sheldon phrase
sheldon_phrase = generate_sheldon_phrase(sheldon_model, sheldon_context)

# Print the phrase
print(sheldon_phrase)

# Context for poem
poem_context = "May thy beauty forever remain"

# Print the poem
print(generate_poem(poem_model, poem_context))
```

#### Ejemplo de NMT

Este ejercicio tiene como objetivo ampliar los conocimientos previos sobre _NMT_ que has adquirido al inicio del curso. Continuarás traduciendo pequeñas frases del portugués al inglés.

Hay algunas frases de ejemplo disponibles en la variable `sentences` y se imprimen en la consola.

Además, hay un modelo preentrenado disponible en la variable `model` y utilizarás dos funciones personalizadas para simplificar algunos pasos:

- `encode_sequences()`: Cambia los textos en secuencias de índices numéricos y rellénalos.
- `translate_many()`: Utiliza el modelo preentrenado para  
    traducir una lista de frases del portugués al inglés. Más adelante,  
    programarás esta función tú mismo.

Para obtener más información sobre las funciones, utiliza `help()`. El paquete `pandas` se carga como `pd`.

```python
# Transform text into sequence of indexes and pad
X = encode_sequences(sentences)

# Print the sequences of indexes
print(X)

# Translate the sentences
translated = translate_many(model, X)

# Create pandas DataFrame with original and translated
df = pd.DataFrame({'Original': sentences, 'Translated': translated})

# Print the DataFrame
print(df)
```

### The text generating function

#### Predice el siguiente carácter

En este ejercicio, codificarás la función para predecir el siguiente carácter dado un modelo entrenado. Utilizarás los últimos 20 caracteres para predecir el siguiente. Aprenderás a entrenar el modelo en la siguiente lección, ya que este paso es fundamental antes del entrenamiento del modelo.

Este es el paso inicial para crear reglas para generar oraciones, párrafos, textos cortos u otros bloques de texto según sea necesario.

Las variables `n_vocab`, `chars_window` y el diccionario `index_to_char` ya están cargados en el entorno. Además, ya se han creado las siguientes funciones:

- `initialize_X()`: Transforma el texto introducido en una secuencia de números de índice con la forma correcta.
- `predict_next_char()`: Obtiene el siguiente carácter utilizando el método « `.predict()` » de la clase de modelo y el diccionario « `index_to_char` ».

```python
def get_next_char(model, initial_text, chars_window, char_to_index, index_to_char):
  	# Initialize the X vector with zeros
    X = initialize_X(initial_text, chars_window, char_to_index)
    
    # Get next character using the model
    next_char = predict_next_char(model, X, index_to_char)
	
    return next_char

# Define context sentence and print the generated text
initial_text = "I am not insane, "
print("Next character: {0}".format(get_next_char(model, initial_text, 20, char_to_index, index_to_char)))
```

#### Generar frase con contexto

En este ejercicio, vas a experimentar con un modelo preentrenado para la generación de texto. El modelo ya está cargado en el entorno en la variable `model`, así como en las funciones `initialize_params()` y `get_next_token()`.

Este último utiliza el modelo preentrenado para predecir el siguiente carácter y devuelve tres variables: el siguiente carácter ( `next_char`), la frase actualizada ( `res` ) y el texto desplazado ( `seq` ) que se utilizará para predecir el siguiente.

Definirás una función que reciba como entradas un modelo preentrenado y una cadena que será el inicio de la frase generada. Esta es una buena práctica para generar texto con contexto. El límite de caracteres de `100` es un ejemplo, puedes utilizar otros límites (o incluso ninguno) en tus aplicaciones.

```python
def generate_phrase(model, initial_text):
    # Initialize variables  
    res, seq, counter, next_char = initialize_params(initial_text)
    
    # Loop until stop conditions are met
    while counter < 100 and next_char != r'.':
      	# Get next char using the model and append to the sentence
        next_char, res, seq = get_next_token(model, res, seq)
        # Update the counter
        counter = counter + 1
    return res
  
# Create a phrase
print(generate_phrase(model, "I am not insane, "))
```

#### Cambiar la escala de probabilidad

En este ejercicio, verás la diferencia en la frase resultante al utilizar diferentes valores de `temperature` para escalar la distribución de probabilidad.

La función `generate_phrase()` es una adaptación de la función que creaste anteriormente y ya está cargada en el entorno. Recibe los parámetros « `model` » con el modelo preentrenado, « `initial_text` » con el texto de contexto y « `temperature` », que es el valor para escalar la función « `softmax()` ».

```python
# Define the initial text
initial_text = "Spock and me "

# Define a vector with temperature values
temperatures = [0.2, 0.8, 1.0, 3.0, 10.0]

# Loop over temperatures and generate phrases
for temperature in temperatures:
	# Generate a phrase
	phrase = generate_phrase(model, initial_text, temperature)
    
	# Print the phrase
	print('Temperature {0}: {1}'.format(temperature, phrase))
```

### Text generation models

#### Crear vectores de frases y caracteres siguientes

Este ejercicio tiene como objetivo enfatizar aún más el valor de la preparación de datos. Utilizarás textos que contienen frases del personaje Sheldon de la serie de televisión The Big Bang Theory como entrada y crearás vectores de índices de frases y caracteres siguientes que son necesarios antes de crear un modelo de generación de texto.

El texto está disponible en la variable `sheldon`, así como el vocabulario (caracteres) en la variable `vocabulary` y los hiperparámetros `chars_window` y `step` definidos con los valores `20` y `3`.  
Esto significa que se utilizará una secuencia de 20 caracteres para predecir el siguiente, y la ventana se desplazará 3 caracteres en cada iteración.

Además, el paquete `pandas` como `pd` se carga en el entorno

```python
# Instantiate the vectors
sentences = []
next_chars = []
# Loop for every sentence
for sentence in sheldon.split('\n'):
    # Get 20 previous chars and next char; then shift by step
    for i in range(0, len(sentence) - chars_window, step):
        sentences.append(sentence[i:i + chars_window])
        next_chars.append(sentence[i + chars_window])

# Define a Data Frame with the vectors
df = pd.DataFrame({'sentence': sentences, 'next_char': next_chars})

# Print the initial rows
print(df.head())
```

#### Preparación de los datos para el entrenamiento

En este ejercicio, continuarás preparando los datos para entrenar el modelo. Después de crear los arreglos de oraciones y caracteres siguientes, debes transformarlos en valores numéricos que puedan utilizarse en el modelo.

Este paso es necesario porque los modelos RNN solo admiten números, no cadenas de caracteres. Crearás arreglos numéricos que tienen ceros o unos en las posiciones que representan los caracteres presentes en las oraciones. Los unos (o « `True` ») representan que el carácter correspondiente está presente, mientras que los ceros (o « `False` ») representan la ausencia del carácter en esa posición de la frase.

Las variables `sentences`, `next_char`, `n_vocab`, `chars_window`, `num_seqs` (número de frases en los datos de entrenamiento) ya están cargadas en el entorno, así como `numpy` como `np`.

```python
# Instantiate the variables with zeros
numerical_sentences = np.zeros((num_seqs, chars_window, n_vocab), dtype=np.bool)
numerical_next_chars = np.zeros((num_seqs, n_vocab), dtype=np.bool)

# Loop for every sentence
for i, sentence in enumerate(sentences):
  # Loop for every character in sentence
  for t, char in enumerate(sentence):
    # Set position of the character to 1
    numerical_sentences[i, t, char_to_index[char]] = 1
    # Set next character to 1
    numerical_next_chars[i, char_to_index[next_chars[i]]] = 1

# Print the first position of each
print(numerical_sentences[0], numerical_next_chars[0], sep="\n")
```

#### Creación del modelo de generación de texto

En este ejercicio, definirás un modelo de generación de texto utilizando Keras.

Las variables « `n_vocab` », que contiene el tamaño del vocabulario, y « `input_shape` », que contiene la forma de los datos utilizados para el entrenamiento, ya están cargadas en el entorno. Además, los pesos de un modelo preentrenado están disponibles en el archivo `model_weights.h5`. El modelo se entrenó con `40` épocas en los datos de entrenamiento. Recapitulando, para entrenar un modelo en Keras, solo tienes que utilizar el método `.fit()` con los datos de entrenamiento `(X, y)` y el parámetro `epochs`. Por ejemplo:

```
model.fit(X_train, y_train, epochs=40)
```

```python
# Instantiate the model
model = Sequential(name="LSTM model")

# Add two LSTM layers
model.add(LSTM(64, input_shape=input_shape, dropout=0.15, recurrent_dropout=0.15, return_sequences=True, name="Input_layer"))
model.add(LSTM(64, dropout=0.15, recurrent_dropout=0.15, return_sequences=False, name="LSTM_hidden"))

# Add the output layer
model.add(Dense(n_vocab, activation='softmax', name="Output_layer"))

# Compile and load weights
model.compile(loss='categorical_crossentropy', optimizer='adam')
model.load_weights('model_weights.h5')
# Summary
model.summary()
```

### Neural Machine Translation

#### Preparación del texto de entrada

Has visto en el vídeo cómo preparar los textos de entrada y salida. Este ejercicio tiene como objetivo mostrar una práctica habitual que consiste en utilizar la longitud máxima de las frases para rellenarlas todas, de modo que no se pierda ninguna información.

Dado que los modelos RNN necesitan que las entradas tengan el mismo tamaño, esta es una forma de rellenar todas las frases y simplemente añadir ceros a las frases más cortas, sin cortar las más largas.

Además, utilizarás palabras en lugar de caracteres para representar los tokens, lo cual es un enfoque habitual en los modelos _NMT_.

Los textos en portugués se cargan en la variable `pt_sentences` y un _tokenizador_ ajustado en la variable `input_tokenizer`.

```python
# Get maximum length of the sentences
pt_length = max([len(sentence.split()) for sentence in pt_sentences])

# Transform text to sequence of numerical indexes
X = input_tokenizer.texts_to_sequences(pt_sentences)

# Pad the sequences
X = pad_sequences(X, maxlen=pt_length, padding='post')

# Print first sentence
print(pt_sentences[0])

# Print transformed sentence
print(X[0])
```

#### Preparación del texto de salida

En este ejercicio, prepararás los textos de salida que se utilizarán en el modelo de traducción. Además de transformar el texto en secuencias de índices, también debes codificar cada índice con codificación one-hot.

Los textos en inglés se cargan en la variable `en_sentences`, el _tokenizador_ ajustado en la variable `output_tokenizer` y el tamaño del vocabulario en inglés en `en_vocab_size`.

Además, ya se ha creado una función para realizar los primeros pasos de la transformación del idioma de salida (transformación de textos en secuencias de índices). La función se carga en el entorno como « `transform_text_to_sequences()` » y tiene dos parámetros: « `sentences` », que espera una lista de frases en inglés, y « `tokenizer` », que espera un objeto « `Tokenizer` » ajustado del módulo « `keras.preprocessing.text` ».

```python
# Initialize the variable
Y = transform_text_to_sequences(en_sentences, output_tokenizer)

# Temporary list
ylist = list()
for sequence in Y:
  	# One-hot encode sentence and append to list
    ylist.append(to_categorical(sequence, num_classes=en_vocab_size))

# Update the variable
Y = np.array(ylist).reshape(Y.shape[0], Y.shape[1], en_vocab_size)

# Print the raw sentence and its transformed version
print("Raw sentence: {0}\nTransformed: {1}".format(en_sentences[0], Y[0]))
```

#### Traducir portugués a inglés

Este es el último ejercicio del curso, ¡enhorabuena por haber llegado hasta aquí!

Aprenderás a utilizar modelos _NMT_ para realizar traducciones.

Se ha preentrenado un modelo que codifica frases cortas en portugués y las decodifica en frases cortas en inglés, y se ha cargado en la variable `model`.

Además, la función `predict_one()` ya está cargada. Para obtener más información, utiliza `help()`. El conjunto de datos está disponible en las variables `test` (texto sin formato) y `X_test` (tokenizado).

Definirás una función para traducir una lista de frases. En los parámetros, « `sentences` » es una lista de frases que se van a traducir, « `index_to_word` » es un objeto de tipo « `dict` » que contiene índices numéricos como claves y palabras como valores para el idioma inglés, cargado en la variable « `en_index_to_word` ».

Se ha impreso el resumen del modelo para tu consideración.

```python
# Function to predict many phrases
def predict_many(model, sentences, index_to_word, raw_dataset):
    for i, sentence in enumerate(sentences):
        # Translate the Portuguese sentence
        translation = predict_one(model, sentence, index_to_word)
        
        # Get the raw Portuguese and English sentences
        raw_target, raw_src = raw_dataset[i]
        
        # Print the correct Portuguese and English sentences and the predicted
        print('src=[%s], target=[%s], predicted=[%s]' % (raw_src, raw_target, translation))

predict_many(model, X_test[0:10], en_index_to_word, test)
```