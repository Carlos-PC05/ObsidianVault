#DeepLearning
# Diapositivas

[chapter1-2_compressed.pdf](attachment:9807484f-0420-4542-9511-ee72c1ec3398:chapter1-2_compressed.pdf)

[chapter2-2_compressed.pdf](attachment:b96e679a-1b4f-4ad9-98bc-609238d84c20:chapter2-2_compressed.pdf)

[chapter3-1_compressed.pdf](attachment:bc96032a-9cc7-4c48-a593-87358edb6321:chapter3-1_compressed.pdf)

[chapter4-1_compressed.pdf](attachment:f09d629a-7a7c-4cee-8ad6-9a2a791335b4:chapter4-1_compressed.pdf)

# Apuntes

## Introducción al Deep Learning con Keras

### What is Keras?

Keras es una envoltura alrededor de una biblioteca backend, por lo que un backend como TensorFlow, Theano, CNTK, etc debe ser proporcionado.

### ¡Hola redes!

Vas a construir una red neuronal simple para tener una idea de lo rápido que es lograr esto en Keras.  
Construirás una red que **toma dos números como entrada**, los pasa a través de **una capa oculta de 10 neuronas**, y finalmente **da como salida un único número no restringido**.  
Se **puede obtener** una **salida sin restricciones evitando establecer una función de activación en la capa de salida**. Esto es útil para problemas como la regresión, cuando queremos que nuestra salida pueda tomar cualquier valor no restringido.

```python
# Import the Sequential model and Dense layer
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

# Create a Sequential model
model = Sequential()

# Add an input layer and a hidden layer with 10 neurons
model.add(Dense(10, input_shape=(2,), activation="relu"))

# Add a 1-neuron output layer
model.add(Dense(1))

# Summarise your model
model.summary()
```

### Parámetros de recuento

Acabas de crear una red neuronal. Pero ahora vas a crear una nueva, tomándote un tiempo para pensar en los pesos de cada capa. La capa de Keras `Dense` y el modelo `Sequential` ya están cargados para que los utilices.

Esta es la red que vas a crear:

![[Pasted image 20260806093057.png|358]]

```python
# Instantiate a new Sequential model
model = Sequential()

# Add a Dense layer with five neurons and three inputs
model.add(Dense(5, input_shape=(3,), activation="relu"))

# Add a final Dense layer with one neuron and no activation
model.add(Dense(1))

# Summarize your model
model.summary()
```

### Especificar un modelo

Construirás un modelo de regresión simple para predecir la órbita del meteoro.

Sus datos de entrenamiento consisten en mediciones tomadas en pasos de tiempo desde **-10 minutos antes de la región de impacto hasta +10 minutos después**. Cada paso temporal puede verse como una coordenada X en nuestro gráfico, que tiene asociada una posición Y para la órbita del meteoro en ese paso temporal.

_Tenga en cuenta que puede ver este problema como la aproximación de una función cuadrática mediante el uso de redes neuronales._

![](https://assets.datacamp.com/production/repositories/4335/datasets/4f15cb3709395af69eee859c892c0775c610c46f/meteor_orbit_3.jpg)

Estos datos se almacenan en dos matrices numpy: una llamada `time_steps`, a la que llamamos _características_, y otra llamada `y_positions`, con las _etiquetas_.Adelante, construye tu maqueta. Debe ser capaz de predecir las posiciones y de la órbita del meteoro en futuros pasos temporales.

El modelo Keras `Sequential` y las capas `Dense` están disponibles para su uso.

```python
# Instantiate a Sequential model
model = Sequential()

# Add a Dense layer with 50 neurons and an input of 1 neuron
model.add(Dense(50, input_shape=(1,), activation='relu'))

# Add two Dense layers with 50 neurons and relu activation
model.add(Dense(50,activation="relu"))
model.add(Dense(50, activation = "relu"))

# End your model with a Dense layer and no activation
model.add(Dense(1))
```

### Formación

En este curso vas a entrenar a tu primera modelo, ¡y por una buena causa!

Recuerda que **antes de entrenar tus modelos Keras necesitas compilarlos**. Esto puede hacerse con el método `.compile()`. El método `.compile()` toma argumentos como el `optimizer`, utilizado para la actualización de pesos, y la función `loss`, que es lo que queremos minimizar. Entrenar su modelo es tan fácil como llamar al método `.fit()`, pasando las _características_, _etiquetas_ y un número de _épocas_ para entrenar.

La regresión `model` que construyó en el ejercicio anterior está cargada para que la utilice, junto con los datos de `time_steps` y `y_positions`. Entrénalo y evalúalo con estos mismos datos, a ver si tu modelo puede aprender la trayectoria del meteoro.

```python
# Compile your model
model.compile(optimizer = "adam", loss = "mse")

print("Training started..., this can take a while:")

# Fit your model on your data for 30 epochs
model.fit(time_steps,y_positions, epochs = 30)

# Evaluate your model 
print("Final loss value:",model.evaluate(time_steps, y_positions))
```

### Predecir la órbita

Ya has entrenado un `model` que se aproxima a la órbita del meteoro acercándose a la Tierra y está cargado para que lo utilices.

Como has entrenado tu modelo para valores entre -10 y 10 minutos, tu modelo aún no ha visto otros valores para diferentes pasos de tiempo.Ahora visualizará cómo se comporta su modelo con datos no vistos.

Si desea consultar el código fuente de `plot_orbit`, pegue `show_code(plot_orbit)` en la consola.

¡Date prisa, a la Tierra se le acaba el tiempo!

_Recuerde que `np.arange(x,y)` produce un rango de valores de **x** a **y-1**. Este es el intervalo `[x, y)`._

```python
# Predict the twenty minutes orbit
twenty_min_orbit = model.predict(np.arange(-10, 11))

# Plot the twenty minute orbit 
plot_orbit(twenty_min_orbit)
```

```python
# Predict the eighty minute orbit
eighty_min_orbit = model.predict(np.arange(-40, 41))

# Plot the eighty minute orbit 
plot_orbit(eighty_min_orbit)
```

## Profundizando

### Clasificación binaria

Practicará la creación de modelos de clasificación en Keras con el conjunto de datos **Banknote Authentication**.

Tu objetivo es distinguir entre billetes de dólar auténticos y falsos. Para ello, el conjunto de datos incluye 4 características: `variance`,`skewness`,`kurtosis` y `entropy`.  
Estas características se calculan aplicando operaciones matemáticas sobre las imágenes de los billetes de dólar. Las etiquetas se encuentran en la columna `class` del marco de datos.

![](https://assets.datacamp.com/production/repositories/4335/datasets/6ce6fd4fdc548ecd6aaa27b033073c5bfc0995da/dollar_bills.png)

Un DataFrame de pandas llamado `banknotes` está listo para usar, ¡vamos a explorar los datos!

```python
# Import seaborn
import seaborn as sns

# Use pairplot and set the hue to be our class column
sns.pairplot(banknotes, hue="class") 

# Show the plot
plt.show()

# Describe the data
print('Dataset stats: \n', banknotes.describe())

# Count the number of observations per class
print('Observations per class: \n', banknotes['class'].value_counts())
```

Ahora que ya sabe cómo es el conjunto de datos de **Autenticación de Billetes**, vamos a construir un modelo sencillo para distinguir entre billetes auténticos y falsos.

Realizará una clasificación binaria utilizando una sola neurona como salida. La capa de entrada tendrá 4 neuronas, ya que tenemos 4 características en nuestro conjunto de datos. La salida del modelo será un valor limitado entre 0 y 1.

Interpretaremos este número de salida como la probabilidad de que nuestras variables de entrada provengan de un billete de dólar falso, con 1 significando que estamos seguros de que es un billete falso.

![](https://assets.datacamp.com/production/repositories/4335/datasets/db1c482fd8cb154572c3ce79fe9a406c25ed1a9b/model_chapter2_binary_classification.JPG)

```python
# Import the sequential model and dense layer
from tensorflow.keras.models import Sequential
from tensorflow.keras.layers import Dense

# Create a sequential model
model = Sequential()

# Add a dense layer 
model.add(Dense(1, input_shape=(4,), activation="sigmoid"))

# Compile your model
model.compile(loss='binary_crossentropy', optimizer="sgd", metrics=['accuracy'])

# Display a summary of your model
model.summary()
```

Ya está listo para entrenar a su `model` y comprobar su eficacia a la hora de clasificar billetes nuevos. El conjunto de datos ya se ha dividido en características: `X_train` y `X_test`, y etiquetas: `y_train` y `y_test`.

```python
# Train your model for 20 epochs
model.fit(X_train, y_train, epochs = 20)

# Evaluate your model accuracy on the test set
accuracy = model.evaluate(X_test, y_test)[1]

# Print accuracy
print('Accuracy:', accuracy)
```

### Clasificación multiclase

Vas a construir un modelo que predice quién lanzó qué dardo basándose únicamente en dónde cayó ese dardo. (Son las coordenadas x e y del dardo en el tablero).

Este problema es un problema de clasificación multiclase, ya que cada dardo sólo puede ser lanzado por uno de 4 competidores. Así que las clases/etiquetas son mutuamente excluyentes, y por lo tanto podemos construir una neurona con tantas salidas como competidores y utilizar la  
función de activación `softmax` para conseguir una suma total de probabilidades de 1 sobre todos los competidores.

El modelo `Sequential` y las capas `Dense` ya están importados para que usted los utilice.

```python
# Instantiate a sequential model
model = Sequential()
  
# Add 3 dense layers of 128, 64 and 32 neurons each
model.add(Dense(128, input_shape=(2,), activation='relu'))
model.add(Dense(64, activation='relu'))
model.add(Dense(32, activation='relu'))
  
# Add a dense layer with as many neurons as competitors
model.add(Dense(4, activation="softmax"))
  
# Compile your model using categorical_crossentropy loss
model.compile(loss="categorical_crossentropy",
              optimizer='adam',
              metrics=['accuracy'])
```

En la consola puede comprobar que sus etiquetas, `darts.competitor` aún no están en un formato que su red pueda entender. Contienen los nombres de los competidores como cadenas. Primero convertirá estos competidores en números únicos, luego utilizará la función `to_categorical()` de `keras.utils` para convertir estos números en su representación codificada de un solo golpe.

Esto es útil para problemas de clasificación multiclase, ya que hay tantas neuronas de salida como clases y para cada observación de nuestro conjunto de datos sólo queremos que se active una de las neuronas.

El conjunto de datos de dart se carga como `darts`. Pandas se importa como `pd`. Preparemos este conjunto de datos

```python
# Transform into a categorical variable
darts.competitor = pd.Categorical(darts.competitor)

# Assign a number to each category (label encoding)
darts.competitor = darts.competitor.cat.codes 

# Print the label encoded competitors
print('Label encoded competitors: \n',darts.competitor.head())

# Import to_categorical from keras utils module
from tensorflow.keras.utils import to_categorical

coordinates = darts.drop(['competitor'], axis=1)
# Use to_categorical on your labels
competitors = to_categorical(darts.competitor)

# Now print the one-hot encoded labels
print('One-hot encoded competitors: \n',competitors)
```

Su modelo ya está listo, al igual que su conjunto de datos. Es hora de entrenar.

Las características de `coordinates` y las etiquetas de `competitors` que acaba de transformar se han dividido en `coord_train`,`coord_test` y `competitors_train`,`competitors_test`.

Su `model` también está cargado. Siéntete libre de visualizar tus datos de entrenamiento o `model.summary()` en la consola.

Averigüemos quién lanzó qué dardo con sólo mirar el tablero.

```python
# Fit your model to the training data for 200 epochs
model.fit(coord_train,competitors_train,epochs=200)

# Evaluate your model accuracy on the test data
accuracy = model.evaluate(coord_test, competitors_test)[1]

# Print accuracy
print('Accuracy:', accuracy)
```

Su recién entrenado `model` está cargado para usted. Este modelo está generalizando bien!, por eso obtuvo una alta precisión en el conjunto de pruebas.

Dado que ha utilizado la función de activación `softmax`, por cada entrada de 2 coordenadas proporcionadas a su modelo hay un vector de salida de 4 números. Cada uno de estos números codifica la probabilidad de que un dardo determinado sea lanzado por uno de los 4  
competidores posibles.

Al calcular la precisión con el método `.evaluate()` del modelo, su modelo toma la clase con la probabilidad más alta como predicción. `np.argmax()` puede ayudarle a hacer esto ya que devuelve el índice con el valor más alto en un array.

Utilice la colección de lanzamientos de prueba almacenada en `coords_small_test` y `np.argmax()`para comprobarlo.

```python
# Predict on coords_small_test
preds = model.predict(coords_small_test)

# Print preds vs true values
print("{:45} | {}".format('Raw Model Predictions','True labels'))
for i,pred in enumerate(preds):
  print("{} | {}".format(pred,competitors_small_test[i]))

# Extract the position of highest probability from each pred vector
preds_chosen = [np.argmax(pred) for pred in preds]

# Print preds vs true values
print("{:10} | {}".format('Rounded Model Predictions','True labels'))
for i,pred in enumerate(preds_chosen):
  print("{:25} | {}".format(pred,competitors_small_test[i]))
```

### Clasificación multietiqueta

Vas a automatizar el riego de parcelas agrícolas fabricando una máquina de riego inteligente. Los problemas de clasificación multietiqueta difieren de los problemas multiclase en que cada  
observación puede etiquetarse con cero o más clases. Por lo tanto, las clases/etiquetas no son mutuamente excluyentes, podría regar todas, ninguna o cualquier combinación de parcelas agrícolas en función de las entradas.

Para explicar este comportamiento lo que hacemos es tener una capa de salida con tantas neuronas como clases pero esta vez, a diferencia de los problemas multiclase, cada neurona de salida tiene una función de activación `sigmoid`. Esto hace que cada neurona de la capa de salida pueda emitir un número entre 0 y 1 de forma independiente.

El modelo `Sequential()` y las capas `Dense()` están listos para ser utilizados. ¡Es hora de construir una máquina de riego inteligente!

![](https://assets.datacamp.com/production/repositories/4335/datasets/e24040be99106cdb8ed07af937013615d29274ba/mutilabel_dataset.jpg)

```python
# Instantiate a Sequential model
model = Sequential()

# Add a hidden layer of 64 neurons and a 20 neuron's input
model.add(Dense(64, input_shape=(20,), activation = "relu"))

# Add an output layer of 3 neurons with sigmoid activation
model.add(Dense(3, activation="sigmoid"))

# Compile your model with binary crossentropy loss
model.compile(optimizer='adam',
           loss = "binary_crossentropy",
           metrics=['accuracy'])

model.summary() 
```

Una salida de su multietiqueta `model` podría tener este aspecto: `[0.76 , 0.99 , 0.66 ]`. Si redondeamos las probabilidades por encima de 0,5, esta observación se clasificará como que contiene las 3 etiquetas posibles `[1,1,1]`. Para este problema concreto, esto significaría que regar las 3 parcelas de su granja es lo correcto, según la red, dadas las mediciones de los  
sensores de entrada.

Ahora entrenarás y predecirás con la `model` que acabas de construir.`sensors_train`, `parcels_train`, `sensors_test` y `parcels_test` ya están cargados para que los utilices.

Veamos el rendimiento de tu máquina inteligente.

```python
# Train for 100 epochs using a validation split of 0.2
model.fit(sensors_train, parcels_train, epochs = 100, validation_split = 0.2)

# Predict on sensors_test and round up the predictions
preds = model.predict(sensors_test)
preds_rounded = np.round(preds)

# Print rounded preds
print('Rounded Predictions: \n', preds_rounded)

# Evaluate your model's accuracy on the test data
accuracy = model.evaluate(sensors_test, parcels_test)[1]

# Print accuracy
print('Accuracy:', accuracy)
```

### Keras Callbacks

La llamada de retorno del historial se devuelve por defecto cada vez que se entrena un modelo con el método `.fit()`. Para acceder a estas métricas puede acceder al parámetro de diccionario `history` dentro del objeto `h_callback` devuelto con las claves correspondientes.

La máquina de riego `model` que construyó en la lección anterior está cargada para que la entrene, junto con sus características y etiquetas ahora cargadas como `X_train`, `y_train`, `X_test`, `y_test`. Esta vez almacenará la llamada de retorno del modelo `history`y utilizará el parámetro `validation_data` mientras se entrena.

Trazará los resultados almacenados en `history` con `plot_accuracy()` y `plot_loss()`, dos sencillas funciones de matplotlib. Puedes comprobar su código en la consola pegando `show_code(plot_loss)`.

¡Veamos los entresijos de nuestra formación!

```python
# Train your model and save its history
h_callback = model.fit(X_train, y_train, epochs = 25,
               validation_data=(X_test, y_test))

# Plot train vs test loss during training
plot_loss(h_callback.history["loss"], h_callback.history["val_loss"])

# Plot train vs test accuracy during training
plot_accuracy(h_callback.history["accuracy"], h_callback.history["val_accuracy"])
```

El callback de parada temprana es útil ya que te permite detener el entrenamiento del modelo si ya no mejora después de un número determinado de épocas. Para hacer uso de esta funcionalidad es necesario pasar el callback dentro de una lista al parámetro callback del modelo en el método `.fit()`.

El `model` que construiste para detectar billetes de dólar falsos está cargado para que lo entrenes, esta vez con parada anticipada. `X_train`, `y_train`, `X_test` y `y_test` también están disponibles para su uso.

```python
# Import the early stopping callback
from tensorflow.keras.callbacks import EarlyStopping

# Define a callback to monitor val_accuracy
monitor_val_acc = EarlyStopping(monitor="val_accuracy", 
                       patience=5)

# Train your model using the early stopping callback
model.fit(X_train, y_train, 
           epochs=1000, validation_data=(X_test, y_test),
           callbacks=[monitor_val_acc])
```

Los modelos de aprendizaje profundo pueden tardar mucho tiempo en entrenarse, especialmente cuando se pasa a arquitecturas más profundas y conjuntos de datos más grandes. Guardar el modelo cada vez que mejora y detenerlo cuando ya no lo hace le permite preocuparse menos por elegir el número de épocas de entrenamiento. También puedes restaurar un modelo guardado en cualquier momento y reanudar el entrenamiento donde lo dejaste.

Los datos de entrenamiento y validación del modelo están disponibles en su espacio de trabajo como `X_train`, `X_test`, `y_train`, y `y_test`.

Utiliza `EarlyStopping()` y las devoluciones de llamada de `ModelCheckpoint()` para que puedas ir a comerte un tarro de galletas mientras dejas el ordenador para trabajar.

```python
# Import the EarlyStopping and ModelCheckpoint callbacks
from tensorflow.keras.callbacks import EarlyStopping, ModelCheckpoint

# Early stop on validation accuracy
monitor_val_acc = EarlyStopping(monitor = "val_accuracy", patience = 3)

# Save the best model as best_banknote_model.hdf5
model_checkpoint = ModelCheckpoint("best_banknote_model.hdf5", save_best_only = True)

# Fit your model for a stupid amount of epochs
h_callback = model.fit(X_train, y_train,
                    epochs = 1000000000000,
                    callbacks = [monitor_val_acc, model_checkpoint],
                    validation_data = (X_test, y_test))
```

## Mejorar el rendimiento del modelo

### Curvas de apredizaje

Vas a construir un modelo en el **conjunto de datos de dígitos**, un conjunto de datos de muestra que viene precargado con scikit learn. El **conjunto de datos de dígitos** consta de **dígitos manuscritos de 8x8 píxeles del 0 al 9**:

![](https://assets.datacamp.com/production/repositories/4335/datasets/a4236d3a85ce1aaf5361ed7549b18b9d4de00860/digits_dataset_sample.png)

Se desea distinguir entre cada uno de los 10 dígitos posibles dada una imagen, por lo que se trata de una**clasificación multiclase**

El conjunto de datos ya se ha dividido en `X_train`, `y_train`, `X_test`, y `y_test`,  
utilizando el 30% de los datos como datos de prueba. Las etiquetas ya  
son vectores codificados de un solo golpe, por lo que no es necesario  
utilizar la función de Keras `to_categorical()`.

¡Construyamos esta nueva `model`!

```python
# Instantiate a Sequential model
model = Sequential()

# Input and hidden layer with input_shape, 16 neurons, and relu 
model.add(Dense(16, input_shape = (64,), activation = "relu"))

# Output layer with 10 neurons (one per digit) and softmax
model.add(Dense(10, activation = "softmax"))

# Compile your model
model.compile(optimizer = "adam", loss = "categorical_crossentropy", metrics = ["accuracy"])

# Test if your model is well assembled by predicting before training
print(model.predict(X_train))
```

Vamos a entrenar el `model` que acabas de construir y a trazar su curva de aprendizaje para comprobar si se está sobreajustando. Puede hacer uso de la función cargada `plot_loss()` para  
trazar la pérdida de formación frente a la pérdida de validación, puede obtener ambos de la devolución de llamada de la historia.

Si desea inspeccionar el código de la función `plot_loss()`, pegue esto en la consola: `show_code(plot_loss)`

```python
# Train your model for 60 epochs, using X_test and y_test as validation data
h_callback = model.fit(X_train, y_train, epochs = 60, validation_data = (X_test, y_test), verbose=0)

# Extract from the h_callback object loss and val_loss to plot the learning curve
plot_loss(h_callback.history["loss"], h_callback.history["val_loss"])
```

Es hora de comprobar si el **conjunto de datos de dígitos** `model` que has creado se beneficia de más ejemplos de entrenamiento.

```python
for size in training_sizes:
  	# Get a fraction of training data (we only care about the training data)
    X_train_frac, y_train_frac = X_train[:size], y_train[:size]

    # Reset the model to the initial weights and train it on the new training data fraction
    model.set_weights(initial_weights)
    model.fit(X_train_frac, y_train_frac, epochs = 50, callbacks = [early_stop])

    # Evaluate and store both: the training data fraction and the complete test set results
    train_accs.append(model.evaluate(X_train_frac, y_train_frac)[1])
    test_accs.append(model.evaluate(X_test, y_test)[1])
    
# Plot train vs test accuracies
plot_results(train_accs, test_accs)
```

### Activation Functions

Comparar funciones de activación requiere un poco de programación, pero nada que no se pueda hacer.

Probarás diferentes funciones de activación en el **modelo multietiqueta** que construiste para tu máquina de riego agrícola en el capítulo 2. La función `get_model('relu')` devuelve una copia de este modelo y aplica la función de activación `'relu'` a su capa oculta.

Realizará un bucle a través de varias funciones de activación, generará un nuevo modelo para cada una y lo entrenará. Si almacena la llamada de retorno del historial en un diccionario, podrá visualizar qué función de activación funcionó mejor en el siguiente ejercicio.

`X_train``y_train`, `X_test`, `y_test` están listos para que los utilices cuando entrenes tus modelos.

```python
# Activation functions to try
activations = ['relu', 'leaky_relu', 'sigmoid', 'tanh']

# Loop over the activation functions
activation_results = {}

for act in activations:
  # Get a new model with the current activation
  model = get_model(act)
  # Fit the model and store the history results
  h_callback = model.fit(X_train, y_train, validation_data = (X_test, y_test), epochs = 20, verbose = 0)
  activation_results[act] = h_callback
```

Se ha ejecutado lo codificado en el ejercicio anterior para obtener la variable`activation_results`, esta vez **se han utilizado 100 epochs en lugar de 20**. De esta forma tendrás más épocas para comparar más a fondo cómo evoluciona el entrenamiento por función de activación.

Para cada `h_callback` de cada función de activación en `activation_results`:

- Se ha extraído el `h_callback.history['val_loss']`.
- Se ha extraído el `h_callback.history['val_accuracy']`.

Ambos se guardan en dos diccionarios: `val_loss_per_function` y `val_acc_per_function`.

Pandas también está cargado como `pd` para que lo utilices. Vamos a trazar unos gráficos rápidos de pérdida de validación y precisión.

```python
# Create a dataframe from val_loss_per_function
val_loss= pd.DataFrame(val_loss_per_function)

# Call plot on the dataframe
val_loss.plot()
plt.show()

# Create a dataframe from val_acc_per_function
val_acc = pd.DataFrame(val_acc_per_function)

# Call plot on the dataframe
val_acc.plot()
plt.show()
```

### Tamaño del lote y normalización del lote

Ya has visto que los modelos suelen entrenarse en lotes de un tamaño fijo. Cuanto menor sea el tamaño del lote, mayor será el número de actualizaciones de peso por época, pero a costa de un descenso de gradiente más inestable. Especialmente si el tamaño del lote es demasiado pequeño y no es representativo de todo el conjunto de entrenamiento.

Veamos cómo afectan los distintos tamaños de lote a la precisión de un modelo sencillo de clasificación binaria que separa los puntos rojos de los azules.

Utilizarás un tamaño de lote de uno, actualizando los pesos una vez por muestra en tu conjunto de entrenamiento para cada época. A continuación, utilizará todo el conjunto de datos, actualizando los pesos sólo una vez por epoch

```python
model = get_model()

# Fit your model for 5 epochs with a batch of size the training set
model.fit(X_train, y_train, epochs=5, batch_size=len(y_train))
print("\n The accuracy when using the whole training set as batch-size was: ",
      model.evaluate(X_test, y_test)[1])
```

¿Recuerdas el **conjunto de datos de dígitos** que entrenaste en el primer ejercicio de este capítulo?

![](https://assets.datacamp.com/production/repositories/4335/datasets/a4236d3a85ce1aaf5361ed7549b18b9d4de00860/digits_dataset_sample.png)

Un problema de clasificación multiclase que resolvió utilizando `softmax` y 10 neuronas en su capa de salida.

Ahora construirá un nuevo modelo más profundo consistente en 3 capas ocultas de 50 neuronas cada una, utilizando normalización por lotes entre capas.  
El parámetro `kernel_initializer` se utiliza para inicializar los pesos de forma similar.

```python
# Import batch normalization from keras layers
from tensorflow.keras.layers import BatchNormalization

# Build your deep network
batchnorm_model = Sequential()
batchnorm_model.add(Dense(50, input_shape=(64,), activation='relu', kernel_initializer='normal'))
batchnorm_model.add(BatchNormalization())
batchnorm_model.add(Dense(50, activation='relu', kernel_initializer='normal'))
batchnorm_model.add(BatchNormalization())
batchnorm_model.add(Dense(50, activation='relu', kernel_initializer='normal'))
batchnorm_model.add(BatchNormalization())
batchnorm_model.add(Dense(10, activation='softmax', kernel_initializer='normal'))

# Compile your model with sgd
batchnorm_model.compile(optimizer="sgd", loss='categorical_crossentropy', metrics=['accuracy'])
```

La normalización por lotes tiende a aumentar la velocidad de aprendizaje de nuestros modelos y a hacer más estables sus curvas de aprendizaje. Veamos cómo se comparan dos modelos idénticos con y sin normalización de lotes.

El modelo que acaba de construir `batchnorm_model` está cargado para que pueda utilizarlo. Una copia exacta sin normalización por lotes: `standard_model` también está disponible. Puedes consultar su `summary()` en la consola. `X_train`, `y_train`, `X_test`, y `y_test` también se cargan para que puedas entrenar ambos modelos.

Comparará las curvas de aprendizaje de precisión de ambos modelos trazándolas con `compare_histories_acc()`.

Puede comprobar la función pegando `show_code(compare_histories_acc)` en la consola.

```python
# Train your standard model, storing its history callback
h1_callback = standard_model.fit(X_train, y_train, validation_data=(X_test,y_test), epochs=10, verbose=0)

# Train the batch normalized model you recently built, store its history callback
h2_callback = batchnorm_model.fit(X_train, y_train, validation_data=(X_test, y_test), epochs=10, verbose=0)

# Call compare_histories_acc passing in both model histories
compare_histories_acc(h1_callback, h2_callback)
```

### Hyperparameter tuning

Afinemos los hiperparámetros de un modelo de **clasificación binaria** que clasifique bien el **conjunto de datos del cáncer de mama**.

Ya has visto que el primer paso para convertir un modelo en un estimador sklearn es construir una función que lo cree. La definición de esta función es importante ya que el ajuste de hiperparámetros se realiza variando los argumentos que recibe su función.

Construya una función `create_model()` simple que reciba como argumentos una tasa de aprendizaje y una función de activación. El optimizador `Adam` se ha importado como un objeto de `tensorflow.keras.optimizers` para que también pueda cambiar su parámetro de tasa de aprendizaje.

```python
# Creates a model given an activation and learning rate
def create_model(learning_rate, activation):
  
  	# Create an Adam optimizer with the given learning rate
  	opt = Adam(lr = learning_rate)
  	
  	# Create your binary classification model  
  	model = Sequential()
  	model.add(Dense(128, input_shape = (30,), activation = activation))
  	model.add(Dense(256, activation = activation))
  	model.add(Dense(1, activation = 'sigmoid'))
  	
  	# Compile your model with your optimizer, loss, and metrics
  	model.compile(optimizer = opt, loss = "binary_crossentropy", metrics = ['accuracy'])
  	return model
```

Es hora de probar distintos parámetros en tu modelo y ver qué tal funciona.

La función `create_model()` que construyó en el ejercicio anterior está lista para ser utilizada.

Dado que ajustar el objeto `RandomizedSearchCV` llevaría demasiado tiempo, los resultados que se obtendrían se imprimen en la función `show_results()`Puedes probar `random_search.fit(X,y)` en la consola para comprobar que funciona después de haber construido todo lo demás, pero  
es probable que se agote el tiempo del ejercicio (así que copia tu código primero si intentas esto o puedes perder tu progreso).

No necesita utilizar los parámetros opcionales `epochs` y `batch_size` cuando construya su objeto `KerasClassifier` ya que los está pasando como `params` a la búsqueda aleatoria y esto ya funciona.

```python
# Import KerasClassifier from tensorflow.keras scikit learn wrappers
from tensorflow.keras.wrappers.scikit_learn import KerasClassifier

# Create a KerasClassifier
model = KerasClassifier(build_fn = create_model)

# Define the parameters to try out
params = {'activation': ['relu', 'tanh'], 'batch_size': [32, 128, 256], 
          'epochs': [50, 100, 200], 'learning_rate': [0.1, 0.01, 0.001]}

# Create a randomize search cv object passing in the parameters to try
random_search = RandomizedSearchCV(model, param_distributions = params, cv = KFold(3))

# Running random_search.fit(X,y) would start the search,but it takes too long! 
show_results()
```

Es hora de entrenar el modelo con los mejores parámetros encontrados: **0,001** para la **tasa de aprendizaje**, **50 épocas**, **un tamaño_lote de 128** y **activaciones relu**.

La función `create_model()` del ejercicio anterior está lista para su uso. `X` y `y` se cargan como funciones y etiquetas.

Utilice los mejores valores encontrados para su modelo al crear su objeto `KerasClassifier` para que se utilicen al realizar la validación_cruzada.

Termina este capítulo entrenando un modelo afinado impresionante en el **conjunto de datos del cáncer de** mama.

```python
# Import KerasClassifier from tensorflow.keras wrappers
from tensorflow.keras.wrappers.scikit_learn import KerasClassifier

# Create a KerasClassifier
model = KerasClassifier(build_fn = create_model(learning_rate = 0.001, activation = 'relu'), epochs = 50, 
             batch_size = 128, verbose = 0)

# Calculate the accuracy score for each fold
kfolds = cross_val_score(model, X, y, cv = 3)

# Print the mean accuracy
print('The mean accuracy was:', kfolds.mean())

# Print the accuracy standard deviation
print('With a standard deviation of:', kfolds.std())
```

## Arquitecturas de modelos avanzados

### Tensores, capas y autocodificadores

Si ya ha construido un modelo, puede utilizar `model.layers` y `tensorflow.keras.backend` para construir funciones que, con un tensor de entrada válido, devuelvan el tensor de salida correspondiente.

Se trata de una herramienta útil cuando queremos obtener la salida de una red en una capa intermedia.

Por ejemplo, si se obtienen la entrada y la salida de la primera capa de una red, se puede construir una función `inp_to_out`  
que devuelva el resultado de llevar a cabo la propagación hacia delante  
a través únicamente de la primera capa para un tensor de entrada dado.

¡Así que eso es lo que vas a hacer ahora mismo!

`X_test` del conjunto de datos **Banknote Authentication** y su `model` están precargados. Escriba `model.summary()` en la consola para comprobarlo.

```python
# Import tensorflow.keras backend
import tensorflow.keras.backend as K

# Input tensor from the 1st layer of the model
inp = model.layers[0].input

# Output tensor from the 1st layer of the model
out = model.layers[0].output

# Define a function from inputs to outputs
inp_to_out = K.function([inp], [out])

# Print the results of passing X_test through the 1st layer
print(inp_to_out([X_test]))
```

¡Ponte los guantes porque vas a hacer cirugía cerebral!

Las neuronas aprenden actualizando sus pesos a valores de salida que les ayudan a distinguir mejor entre las distintas clases de salida de su conjunto de datos.  
Utilizarás la función `inp_to_out()` que acabas de construir para visualizar la salida de dos neuronas en la primera capa de la **Autentificación de Billetes** `model` a medida que aprende.

El `model` que construiste en el capítulo 2 está listo para que lo utilices, al igual que `X_test` y `y_test`. Pegue `show_code(plot)` en la consola si desea comprobar `plot()`.

Una vez hecho todo esto, ¡haz clic en los gráficos para ver la separación en directo!

```python
for i in range(0, 21):
  	# Train model for 1 epoch
    h = model.fit(X_train, y_train, batch_size = 16, epochs = 1, verbose = 0)
    if i%4==0: 
      # Get the output of the first layer
      layer_output = inp_to_out([X_test])[0]
      
      # Evaluate model accuracy for this epoch
      test_accuracy = model.evaluate(X_test, y_test)[1] 
      
      # Plot 1st vs 2nd neuron output
      plot()
```

![[Pasted image 20260806093155.png]]

Los autocodificadores tienen varias aplicaciones interesantes, como la detección de anomalías o la eliminación de ruido en imágenes. Su objetivo es producir un producto idéntico a sus insumos. La entrada se comprimirá en un espacio de menor dimensión, **codificado**. A continuación, el modelo aprende a **descodificarlo** para devolverlo a su forma original.

Se codificará y decodificará el **MNIST** conjunto de datos de dígitos manuscritos, la capa oculta codificará una representación de 32 dimensiones de la imagen, que originalmente consta  
de 784 píxeles (28 x 28). El autocodificador aprenderá esencialmente a convertir la imagen original de 784 píxeles en una imagen comprimida de 32 píxeles y a utilizar esa representación codificada para recuperar la imagen original de 784 píxeles.

El modelo `Sequential` y las capas `Dense` están listos para su uso.

¡Construyamos un autocodificador!

```python
# Start with a sequential model
autoencoder = Sequential()

# Add a dense layer with input the original image pixels and neurons the encoded representation
autoencoder.add(Dense(32, input_shape=(784, ), activation="relu"))

# Add an output layer with as many neurons as the orginal image pixels
autoencoder.add(Dense(784, activation = "sigmoid"))

# Compile your model with adadelta
autoencoder.compile(optimizer = "adadelta", loss = "binary_crossentropy")

# Summarize your model structure
autoencoder.summary()
```

Bien, acabas de construir un modelo `autoencoder`. Veamos cómo gestiona una tarea más difícil.

En primer lugar, construirás un modelo que codifique imágenes y comprobarás cómo se representan distintos dígitos con `show_encodings()`. Para construir el codificador utilizarás tu `autoencoder`, que ya ha sido entrenado. Sólo utilizará la primera mitad de la red, que contiene la entrada y la salida del cuello de botella. De este modo, obtendrá una salida de 32 números que representa la versión codificada de la imagen de entrada.

A continuación, aplicará su `autoencoder` a las imágenes ruidosas de `MNIST`, debería ser capaz de limpiar los artefactos ruidosos.

`X_test_noise` se carga en su espacio de trabajo. Los dígitos de este conjunto de datos ruidosos tienen este aspecto:

![](https://assets.datacamp.com/production/repositories/4335/datasets/55f20c8d36688a6a83b2609ef574c0965915e012/noisy_mnist_sample.png)

¡Aplica el poder del autocodificador!

```python
# Build your encoder by using the first layer of your autoencoder
encoder = Sequential()
encoder.add(autoencoder.layers[0])

# Encode the noisy images and show the encodings for your favorite number [0-9]
encodings = encoder.predict(X_test_noise)
show_encodings(encodings, number = 1)

# Predict on the noisy images with your autoencoder
decoded_imgs = autoencoder.predict(X_test_noise)

# Plot noisy vs decoded images
compare_plot(X_test_noise, decoded_imgs)
```

### Intro to CNNs

Construir un modelo CNN en Keras no es mucho más difícil que construir cualquiera de los modelos que ya has construido a lo largo del curso. Basta con utilizar capas convolucionales.

Usted va a construir un poco profunda `model` convolucional que clasifica la **MNIST** conjunto de datos de dígitos. ¡El mismo que desnotificaste con tu autocodificador! Las imágenes son de 28 x 28 píxeles y **sólo tienen un canal**, ya que son imágenes en blanco y negro.

Adelante, construye este pequeño modelo convolucional.

```python
# Import the Conv2D and Flatten layers and instantiate model
from tensorflow.keras.layers import Conv2D,Flatten
model = Sequential()

# Add a convolutional layer of 32 filters of size 3x3
model.add(Conv2D(filters = 32, kernel_size = 3, input_shape = (28, 28, 1), activation = 'relu'))

# Add a convolutional layer of 16 filters of size 3x3
model.add(Conv2D(filters = 16, kernel_size = 3, activation = "relu"))

# Flatten the previous layer output
model.add(Flatten())

# Add as many outputs as classes with softmax activation
model.add(Dense(10, activation = 'softmax'))
```

Inspeccionar las activaciones de una capa convolucional es una pasada. Hay que hacerlo al menos una vez en la vida.

Para ello, construirás un nuevo modelo con el objeto Keras `Model`, que recibe una lista de entradas y una lista de salidas. Las salidas que proporcionará a este nuevo modelo son las salidas de la primera capa convolucional cuando se le da un **MNIST** dígito como imagen de entrada.

El convolucional `model` que construyó en el ejercicio anterior ya ha sido entrenado para usted. Ahora puede clasificar correctamente **MNIST** imágenes manuscritas. Puedes comprobarlo con `model.summary()` en la consola.

Veamos las máscaras convolucionales aprendidas en la primera capa convolucional de este modelo.

```python
# Obtain a reference to the outputs of the first layer
first_layer_output = model.layers[0].output

# Build a model using the model's input and the first layer output
first_layer_model = Model(inputs = model.layers[0].input, outputs = first_layer_output)

# Use this model to predict on X_test
activations = first_layer_model.predict(X_test)

# Plot the activations of first digit of X_test for the 15th filter
axs[0].matshow(activations[0,:,:,14], cmap = 'viridis')

# Do the same but for the 18th filter now
axs[1].matshow(activations[0,:,:,17], cmap = 'viridis')
plt.show()
```

El **modelo ResNet50** original se entrenó con imágenes de tamaño **224 x 224 píxeles** y una serie de operaciones de preprocesamiento; como la sustracción del valor medio de píxel en el conjunto de entrenamiento para todas las imágenes de entrenamiento. Es necesario preprocesar de la misma manera las imágenes sobre las que se desea predecir.

Al predecir sobre una sola imagen, es necesario que se ajuste a la orma de entrada del modelo, que en este caso tiene este aspecto: (batch-size, width, height, channels),`np.expand_dims` con el parámetro `axis = 0` añade la dimensión batch-size, que representa que se pasará una sola  
imagen a predecir. Este valor de dimensión de tamaño de lote es 1, ya que sólo estamos prediciendo en una imagen.

Repasará estos pasos de preprocesamiento mientras prepara la imagen de esta perra (llamada Ivy) en una que pueda ser clasificada por **ResNet50**.

```python
# Import image and preprocess_input
from tensorflow.keras.preprocessing import image
from tensorflow.keras.applications.resnet50 import preprocess_input

# Load the image with the right target size for your model
img = image.load_img(img_path, target_size=(224, 224))

# Turn it into an array
img_array = image.img_to_array(img)

# Expand the dimensions of the image, this is so that it fits the expected model input format
img_expanded = np.expand_dims(img_array, axis = 0)

# Pre-process the img in the same way original images were
img_ready = preprocess_input(img_expanded)
```

Bien, la foto de Ivy está lista para ser utilizada por **ResNet50**. Está almacenado en `img_ready` y ahora tiene este aspecto:

![](https://assets.datacamp.com/production/repositories/4335/datasets/f5092a58464fd6887e436db3eba85534fdf34bde/dog_processed.png)

**ResNet50** es un modelo entrenado en el **conjunto de datos Imagenet** que es capaz de distinguir entre 1000 objetos etiquetados diferentes. **ResNet50** es un modelo profundo con 50 capas, puede comprobarlo en 3D [aquí](https://tensorspace.org/html/playground/resnet50.html).

`ResNet50` y `decode_predictions` se han importado de `tensorflow.keras.applications.resnet50` para usted.

Es hora de utilizar este modelo entrenado para averiguar la raza de Ivy

```python
# Instantiate a ResNet50 model with 'imagenet' weights
model = ResNet50(weights="imagenet")

# Predict with ResNet50 on your already processed img
preds = model.predict(img_ready)

# Decode the first 3 predictions
print('Predicted:', decode_predictions(preds, top=3)[0])
```

### **Introducción a LSTMs**

Durante los siguientes ejercicios construirá un modelo de juguete LSTM capaz de predecir la siguiente palabra utilizando un pequeño conjunto de datos de texto.  
Este conjunto de datos consta de citas depuradas de las películas de **El Señor de los Anillos**. Puede encontrarlos en la variable `text`.

Convertirás este `text` en `sequences` de **longitud 4** y harás uso de Keras `Tokenizer` para preparar las características y etiquetas para tu modelo.

Keras `Tokenizer` ya está importado para que lo utilices. Asigna un número único a cada palabra única y almacena las correspondencias en un diccionario. Esto es importante ya que el modelo  
trata con números pero más tarde querremos decodificar los números de salida en palabras.

```python
# Split text into an array of words 
words = text.split()

# Make sentences of 4 words each, moving one word at a time
sentences = []
for i in range(4, len(words)):
  sentences.append(' '.join(words[i-4:i]))

# Instantiate a Tokenizer, then fit it on the sentences
tokenizer = Tokenizer()
tokenizer.fit_on_texts(sentences)

# Turn sentences into a sequence of numbers
sequences = tokenizer.texts_to_sequences(sentences)
print("Sentences: \n {} \n Sequences: \n {}".format(sentences[:5],sequences[:5]))
```

Ya ha preparado sus secuencias de texto. ¡Es hora de construir su modelo LSTM!

Recuerde que sus secuencias tenían 4 palabras cada una, su modelo será entrenado en las tres primeras palabras de cada secuencia, prediciendo la 4ª. Va a utilizar una capa `Embedding` que  
esencialmente aprenderá a convertir las palabras en vectores significativos. A continuación, estos vectores se pasarán a una capa simple de `LSTM`. Nuestra salida es una capa `Dense` con tantas neuronas como palabras en el vocabulario y `softmax` activación. Esto se debe a que queremos obtener la palabra siguiente más probable de entre todas las posibles.

El tamaño del vocabulario de palabras (el número único de palabras) se almacena en `vocab_size`.

```python
# Import the Embedding, LSTM and Dense layer
from tensorflow.keras.layers import Embedding, LSTM, Dense

model = Sequential()

# Add an Embedding layer with the right parameters
model.add(Embedding(input_dim = vocab_size, input_length = 3, output_dim = 8, ))

# Add a 32 unit LSTM layer
model.add(LSTM(32))

# Add a hidden Dense layer of 32 units and an output layer of vocab_size with softmax
model.add(Dense(32, activation='relu'))
model.add(Dense(32, activation='softmax'))
model.summary()
```

Tu LSTM `model` ya ha sido entrenado (detalles en el mensaje de éxito del ejercicio anterior) para que no tengas que esperar. Es hora de **definir una función** que descifre sus predicciones. El `model` entrenado se pasará como parámetro por defecto a esta función.

Dado que estás prediciendo en un modelo que utiliza la función softmax, se puede utilizar `argmax()` de numpy para obtener el índice/posición que representa la siguiente palabra más probable del vector de salida de probabilidades.

La página `tokenizer` que previamente ha creado y ajustado, se carga para usted. Utilizará su diccionario interno `index_word` para convertir la predicción de la siguiente palabra de `model`(que es un número entero) en la palabra escrita que representa.

¡Estás muy cerca de experimentar con tu modelo!

```python
def predict_text(test_text, model = model):
  if len(test_text.split()) != 3:
    print('Text input should be 3 words!')
    return False
  
  # Turn the test_text into a sequence of numbers
  test_seq = tokenizer.texts_to_sequences([test_text])
  test_seq = np.array(test_seq)
  
  # Use the model passed as a parameter to predict the next word
  pred = model.predict(test_seq).argmax(axis = 1)[0]
  
  # Return the word that maps to the prediction
  return tokenizer.index_word[pred]
```