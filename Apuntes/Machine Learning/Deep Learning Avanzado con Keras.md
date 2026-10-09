#DeepLearning 
# Diapositivas

[chapter1-2.pdf](attachment:8f4a472d-560f-4d5f-93a7-502339616166:chapter1-2.pdf)

[chapter2-2.pdf](attachment:4f4645f2-7979-42a4-af27-f4cddd55cacb:chapter2-2.pdf)

[chapter3.pdf](attachment:dc9a1cf2-8f23-4e2b-8c13-7295a8b13920:chapter3.pdf)

[chapter4-2.pdf](attachment:a7484767-a9d4-4a35-89f6-554f1c90bb05:chapter4-2.pdf)

# Apuntes

## The Keras functional API

### Capas Densas y de Input

El primer paso para crear un modelo de red neuronal es definir la capa _de entrada_. Esta capa toma datos sin procesar, normalmente en forma de arreglos numpy. La forma de la capa de entrada define cuántas variables utilizará tu red neuronal. Por ejemplo, si los datos de entrada tienen 10  
columnas, definís una capa de entrada con una forma de `(10,)`.

En este caso, solo estás utilizando una entrada en tu red.

Una vez que tengas una capa de entrada, el siguiente paso es añadir una capa _densa_.

Las capas densas aprenden una matriz de pesos, donde la primera dimensión de la matriz es la dimensión de los datos de entrada y la segunda dimensión es la dimensión de los datos de salida. Recuerda que tu capa de entrada tiene una forma de 1. En este caso, la capa de salida también tendrá una forma de 1. Esto significa que la capa Densa aprenderá una matriz de pesos 1x1.

En este ejercicio, añadirás una capa densa a tu modelo, después de la capa de entrada.

```python
# Load layers
from tensorflow.keras.layers import Input, Dense

# Input layer
input_tensor = Input(shape=(1,))

# Dense layer
output_layer = Dense(1)

# Connect the dense layer to the input_tensor
output_tensor = output_layer(input_tensor)
```

¡Las capas de salida son simplemente capas densas! Las capas de salida se utilizan para reducir la dimensión de las entradas a la dimensión de las salidas. Aprenderás más sobre las dimensiones de salida en el capítulo 4, pero por ahora, siempre utilizarás una única salida en tus redes neuronales, lo que equivale a un `Dense(1)` o una capa densa con una única unidad.

```python
# Load layers
from tensorflow.keras.layers import Input, Dense

# Input layer
input_tensor = Input(shape=(1,))

# Create a dense layer and connect the dense layer to the input_tensor in one step
# Note that we did this in 2 steps in the previous exercise, but are doing it in one step now
output_tensor = Dense(1)(input_tensor)
```

### Construye y compila un modelo

Una vez definidas las capas de entrada y salida, puedes crear un modelo Keras. El objeto modelo es la forma en que le indicas a Keras dónde comienza y termina el modelo: dónde entran los datos y dónde salen las predicciones.

```python
# Input/dense/output layers
from tensorflow.keras.layers import Input, Dense
input_tensor = Input(shape=(1,))
output_tensor = Dense(1)(input_tensor)

# Build the model
from tensorflow.keras.models import Model
model = Model(input_tensor, output_tensor)
```

El último paso para crear un modelo es _compilarlo_. Ahora que has creado un modelo, debes compilarlo antes de poder ajustarlo a los datos. Esto finaliza tu modelo, congela todos sus ajustes y lo prepara para recibir datos.

Durante la compilación, se especifica el optimizador que se utilizará para ajustar el modelo a los datos y una función de pérdida. El optimizador de pérdida de red neuronal ( `'adam'` ) es un  
buen optimizador predeterminado que suele funcionar bien. La función de pérdida depende del problema en cuestión. El error cuadrático medio es una función de pérdida habitual y optimiza la predicción de la media, tal y como se hace en _la regresión por mínimos cuadrados_.

El error absoluto medio optimiza la mediana y se utiliza en la regresión cuantílica. Para este conjunto de datos, la función de pérdida « `'mean_absolute_error'` » funciona bastante bien, así que utilízala como función de pérdida.

```python
# Compile the model
model.compile(optimizer="adam", loss="mean_absolute_error")
```

Ahora que ya has compilado el modelo, ¡echa un vistazo al resultado de tu arduo trabajo! Puedes hacerlo consultando el resumen del modelo, así como su gráfico.

El resumen te indicará los nombres de las capas, así como cuántas unidades tienen y cuántos parámetros hay en el modelo.

```python
# Import the plotting function
from tensorflow.keras.utils import plot_model
import matplotlib.pyplot as plt

# Summarize the model
model.summary()

# Plot the model
plot_model(model, to_file='model.png')

# Display the image
data = plt.imread('model.png')
plt.imshow(data)
plt.show()
```

### Ajustar y evaluar un modelo

Ahora que el modelo está compilado, ¡estás listo para ajustarlo a algunos datos!

En este ejercicio, utilizarás un conjunto de datos con las puntuaciones de los partidos del torneo universitario de baloncesto de EE. UU. Cada fila del conjunto de datos contiene los ID de los equipos: `team_1` y `team_2`, como números enteros. También tiene en cuenta la diferencia entre los equipos (los equipos son asignados por el comité del torneo y representan una clasificación  
según su nivel) y la diferencia de puntos del partido (por ejemplo, si `team_1` gana por 5 puntos, la diferencia de puntos es `5`).

Para ajustar el modelo, proporcionas una matriz de variables X (en este caso, una columna: la diferencia de semillas) y una matriz de variables Y (en este caso, una columna: la diferencia de puntuación).

El DataFrame « `games_tourney` » junto con el objeto « `model` » compilado están disponibles en tu espacio de trabajo.

```python
# Now fit the model
model.fit(games_tourney_train["seed_diff"], games_tourney_train["score_diff"],
          epochs=1,
          batch_size=128,
          validation_split=0.10,
          verbose=True)
```

Después de ajustar el modelo, puedes evaluarlo con datos nuevos. Le darás al modelo una nueva matriz de datos de prueba (también llamada « `X` »), le permitirás hacer predicciones y, a continuación, las compararás con la variable de entrenamiento conocida (también llamada « `y` »).

En este caso, utilizarás los datos del torneo de postemporada para evaluar tu modelo. Los partidos del torneo se disputan después de los partidos de la temporada regular que utilizaste para entrenar nuestro modelo y, por lo tanto, son una buena evaluación del rendimiento de tu  
modelo fuera de la muestra.

El DataFrame « `games_tourney_test` » junto con el objeto « `model` » ajustado están disponibles en tu espacio de trabajo.

```python
# Load the X variable from the test data
X_test = games_tourney_test['seed_diff']

# Load the y variable from the test data
y_test = games_tourney_test['score_diff']

# Evaluate the model on the test data
print(model.evaluate(X_test, y_test, verbose=False))
```

## **Two Input Networks Using Categorical Embeddings, Shared Layers, and Merge Layers**

### Category Embeddings

Las capas compartidas permiten que un modelo utilice la misma matriz de pesos para varios pasos. En este ejercicio, crearás una capa de «fuerza del equipo» que representa a cada equipo con un solo número. Utilizarás este número para ambos equipos del modelo. El modelo  
aprenderá un número para cada equipo que funcione bien tanto cuando el equipo sea `team_1` como cuando sea `team_2` en los datos de entrada.

El DataFrame « `games_season` » está disponible en tu espacio de trabajo.

```python
# Imports
from tensorflow.keras.layers import Embedding
from numpy import unique

# Count the unique number of teams
n_teams = unique(games_season['team_1']).shape[0]

# Create an embedding layer
team_lookup = Embedding(input_dim=n_teams,
                        output_dim=1,
                        input_length=1,
                        name='Team-Strength')
```

La búsqueda de la fuerza del equipo tiene tres componentes: una entrada, una capa de incrustación y una capa de aplanamiento que crea la salida.

Si envuelves estas tres capas en un modelo con una entrada y una salida, puedes reutilizar esa pila de tres capas en varios lugares.

Ten en cuenta nuevamente que los pesos de las _tres_ capas se compartirán en _todos los lugares_ donde los utilicemos.

```python
# Imports
from tensorflow.keras.layers import Input, Embedding, Flatten
from tensorflow.keras.models import Model

# Create an input layer for the team ID
teamid_in = Input(shape=(1,))

# Lookup the input in the team strength embedding layer
strength_lookup = team_lookup(teamid_in)

# Flatten the output
strength_lookup_flat = Flatten()(strength_lookup)

# Combine the operations into a single, re-usable model
team_strength_model = Model(teamid_in, strength_lookup_flat, name='Team-Strength-Model')
```

### Shared layers

En este ejercicio, definirás dos capas de entrada para los dos equipos de tu modelo. Esto te permite especificar más adelante en el modelo cómo se utilizarán de forma diferente los datos de cada equipo.

```python
# Load the input layer from tensorflow.keras.layers
from tensorflow.keras.layers import Input

# Input layer for team 1
team_in_1 = Input(shape=(1,), name = "Team-1-In")

# Separate input layer for team 2
team_in_2 = Input(shape = (1,), name = "Team-2-In")
```

Ahora que ya tienes un modelo de fortalezas del equipo y una capa de entradas para cada equipo, puedes buscar las entradas del equipo en el modelo de fortalezas del equipo compartido. Las dos entradas compartirán los mismos pesos.

En este conjunto de datos, tienes 10 888 equipos únicos. Quieres conocer la clasificación de cada equipo, de modo que si se enfrentan dos equipos, puedas predecir el resultado, incluso si nunca han jugado antes. Además, queréis que la clasificación por fuerza sea la misma, independientemente de si el equipo es local o visitante.

Para lograrlo, utilizas una capa compartida, definida por el modelo reutilizable `team_strength_model()`) que creaste en el ejercicio 3 y las dos capas de entrada (`team_in_1` y `team_in_2`) del ejercicio anterior, todas ellas disponibles en tu espacio de trabajo.

```python
team_1_strength = team_strength_model(team_in_1)

team_2_strength = team_strength_model(team_in_2)
```

### Merge layers

Ahora que ya has averiguado la «fuerza» de cada equipo, resta las fortalezas de los equipos para determinar qué equipo tiene más posibilidades de ganar el partido.

Es un poco como las cabezas de serie que utiliza el comité del torneo, que también son una medida de la fuerza de los equipos. Pero en lugar de utilizar las diferencias entre las semillas para predecir las diferencias en la puntuación, utilizarás la diferencia entre tu propio modelo de fuerza del equipo para predecir las diferencias en la puntuación.

La capa de sustracción combinará los pesos de las dos capas restándolos.

```python
# Import the Subtract layer from tensorflow.keras
from tensorflow.keras.layers import Subtract

# Create a subtract layer using the inputs from the previous exercise
score_diff = Subtract()([team_1_strength, team_2_strength])
```

Ahora que tienes tus dos entradas (id. de equipo 1 e id. de equipo 2) y la salida (diferencia de puntuación), puedes incluirlas en un modelo para poder utilizarlo más adelante para ajustarlo a los datos y evaluar nuevos datos.

Tu modelo tendrá el siguiente aspecto:

![](https://s3.amazonaws.com/assets.datacamp.com/production/course_6554/datasets/basketball_model_2.png)

```python
# Create the model
model = Model([team_in_1, team_in_2], score_diff)

# Compile the model
model.compile(optimizer = "adam", loss = "mean_absolute_error")
```

### Fitting and predicting with multiple intputs

Ahora que has definido un modelo completo de fuerza del equipo, ¡puedes ajustarlo a los datos del baloncesto! Dado que tu modelo ahora tiene dos entradas, debes pasar los datos de entrada como una lista.

```python
# Get the team_1 column from the regular season data
input_1 = games_season["team_1"]

# Get the team_2 column from the regular season data
input_2 = games_season["team_2"]

# Fit the model to input 1 and 2, using score diff as a target
model.fit([input_1, input_2],
          games_season["score_diff"],
          epochs=1,
          batch_size=2048,
          validation_split=.10,
          verbose=True)
```

El modelo que ajustaste a los datos de la temporada regular (`model`) en el ejercicio anterior y el conjunto de datos del torneo (`games_tourney`) están disponibles en tu espacio de trabajo.

En este ejercicio, evaluarás el modelo con este nuevo conjunto de datos. Esta evaluación te indicará tu capacidad para predecir los resultados de los partidos del torneo, basándose en un modelo entrenado con los datos de la temporada regular. Esto es interesante porque muchos equipos se enfrentan entre sí en el torneo que no jugaron en la temporada regular, por lo que es una muy buena forma de comprobar que tu modelo no está sobreajustado.

```python
# Get team_1 from the tournament data
input_1 = games_tourney["team_1"]

# Get team_2 from the tournament data
input_2 = games_tourney["team_2"]

# Evaluate the model using these inputs
print(model.evaluate([input_1, input_2], games_tourney["score_diff"], verbose=False))
```

## Multiple inputs: 3 inputs (and Beyond!)

### Three-inputs models

Ahora mejorarás el modelo que utilizaste en el capítulo anterior para los partidos de la temporada regular. Sabes que existe una ventaja bien documentada del equipo local en el baloncesto, por lo que añadirás una nueva variable a tu modelo para reflejar este efecto.

Este modelo tendrá tres entradas: `team_id_1`, `team_id_2` y `home`. Los ID de los equipos serán números enteros que buscarás en tu modelo de fuerza de los equipos del capítulo anterior, y «home» será una variable binaria, 1 si `team_1` juega en casa y 0 si no es así.

El archivo `team_strength_model` que utilizaste en el capítulo anterior se ha cargado en tu espacio de trabajo. Después de aplicarlo a cada entrada, utiliza una capa Concatenate para unir las dos  
fuerzas del equipo y con la variable local vs visitante, y pasa el resultado a una capa Dense.

```python
# Create an Input for each team
team_in_1 = Input(shape=(1,), name='Team-1-In')
team_in_2 = Input(shape=(1,), name='Team-2-In')

# Create an input for home vs away
home_in = Input(shape=(1,), name='Home-In')

# Lookup the team inputs in the team strength model
team_1_strength = team_strength_model(team_in_1)
team_2_strength = team_strength_model(team_in_2)

# Combine the team strengths with the home input using a Concatenate layer, then add a Dense layer
out = Concatenate()([team_1_strength, team_2_strength, home_in])
out = Dense(1)(out)
```

Ahora que ya tienes las capas de entrada y salida para el modelo de 3 entradas, envuélvelas en una clase de modelo Keras y, a continuación, compila el modelo para poder ajustarlo a los datos y utilizarlo para realizar predicciones sobre nuevos datos.

```python
# Import the model class
from tensorflow.keras.models import Model

# Make a Model
model = Model([team_in_1, team_in_2, home_in], out)

# Compile the model
model.compile(optimizer="adam", loss="mean_absolute_error")
```

Ahora que has definido un nuevo modelo, ajústalo a los datos de la temporada regular de baloncesto.

Utiliza el modelo de entrenamiento ( `model` ) que ajustaste en el ejercicio anterior (que se entrenó con los datos de la temporada regular) y evalúa el modelo con los datos de los partidos del  
torneo (`games_tourney`).

```python
# Fit the model to the games_season dataset
model.fit([games_season["team_1"], games_season["team_2"], games_season["home"]],
          games_season["score_diff"],
          epochs=1,
          verbose=True,
          validation_split=0.10,
          batch_size=2048)

# Evaluate the model on the games_tourney dataset
print(model.evaluate([games_tourney["team_1"], games_tourney["team_2"], games_tourney["home"]], games_tourney["score_diff"], verbose=False))
```

### Summarizing and plotting models

Además de resumir tu modelo, también puedes gráficarlo para obtener una visión más intuitiva del mismo.  
Tu `model` está disponible en el espacio de trabajo.

```python
# Imports
import matplotlib.pyplot as plt
from tensorflow.keras.utils import plot_model

# Plot the model
plot_model(model, to_file="model.png")

# Display the image
data = plt.imread('model.png')
plt.imshow(data)
plt.show()
```

### Stacking models

En la lección 1 de este capítulo, utilizaste el modelo de temporada regular para hacer predicciones sobre el conjunto de datos del torneo y obtuviste resultados bastante buenos. Intenta mejorar tus predicciones para el torneo modelándolo específicamente.

Utilizarás la predicción del modelo de la temporada regular como entrada para el modelo del torneo. Se trata de una forma de «apilamiento de modelos».

Para empezar, toma el modelo de la temporada regular de la lección anterior y haz una predicción basándote en los datos del torneo. Añade esta predicción a los datos del torneo como una nueva columna.

```python
# Predict
games_tourney['pred'] = model.predict([games_tourney['team_1'], games_tourney['team_2'], games_tourney['home']])
```

En este ejercicio, verás una forma diferente de crear modelos con múltiples entradas. Este método solo funciona con datos puramente numéricos, pero es un enfoque mucho más sencillo para crear redes neuronales multivariantes.

Ahora tienes tres columnas numéricas en el conjunto de datos del torneo: `'seed_diff'`, `'home'` y `'pred'`. En este ejercicio, crearás una red neuronal que utiliza una única capa de entrada para procesar estas tres entradas numéricas.

Este modelo debe tener una única salida para predecir la diferencia de puntuación del partido del torneo.

```python
# Create an input layer with 3 columns
input_tensor = Input((3,))

# Pass it to a Dense layer with 1 unit
output_tensor = Dense(1)(input_tensor)

# Create a model
model = Model(input_tensor, output_tensor)

# Compile the model
model.compile(optimizer="adam", loss="mean_absolute_error")
```

Ahora que has enriquecido el conjunto de datos del torneo y has creado un modelo para utilizar los nuevos datos, ajusta ese modelo a los datos del torneo.

Ten en cuenta que este `model` solo tiene una capa de entrada capaz de gestionar las tres entradas, por lo que sus entradas y salidas no necesitan ser una lista.

Los juegos del torneo se dividen en un conjunto de entrenamiento y un conjunto de prueba. Los partidos del torneo anteriores a 2010 se encuentran en el conjunto de entrenamiento, y los posteriores a 2010, en el conjunto de prueba.

```python
# Fit the model
model.fit(games_tourney_train[['home', 'seed_diff', 'pred']],
          games_tourney_train['score_diff'],
          epochs=1,
          verbose=True)
```

Ahora que ya has ajustado tu modelo a los datos de entrenamiento del torneo, evalúalo con los datos de prueba del torneo. Recordemos que los datos de prueba del torneo contienen partidos posteriores a 2010.

```python
# Evaluate the model on the games_tourney_test dataset
print(model.evaluate(games_tourney_test[['home', 'seed_diff', 'prediction']],
               games_tourney_test['score_diff'], verbose=False))
```

## Multiple outputs

### Two-output models

En este ejercicio, utilizarás los datos del torneo para crear un modelo que haga dos predicciones: los resultados de ambos equipos en un partido determinado. Tus aportaciones serán la diferencia inicial entre los dos equipos, así como la diferencia de puntuación prevista según el modelo que creaste en el capítulo 3.

El resultado de tu modelo será la puntuación prevista tanto para el equipo 1 como para el equipo 2. Esto se denomina «regresión múltiple»: un modelo que realiza más de una predicción.

```python
# Define the input
input_tensor = Input(shape=(2,))

# Define the output
output_tensor = Dense(2)(input_tensor)

# Create a model
model = Model(input_tensor, output_tensor)

# Compile the model
model.compile(optimizer = 'adam', loss = 'mean_absolute_error')
```

Ahora que ya has definido tu modelo de 2 salidas, ajústalo a los datos del torneo. He dividido los datos en `games_tourney_train` `and games_tourney_test`, así que por ahora utiliza el conjunto de entrenamiento para ajustarlos.

Este modelo utilizará las clasificaciones previas al torneo, así como tus predicciones previas al torneo del modelo de temporada regular que creaste anteriormente en este curso.

Como recordatorio, este modelo predecirá los resultados de ambos equipos.

```python
# Fit the model
model.fit(games_tourney_train[['seed_diff', 'pred']],
  		  games_tourney_train[['score_1', 'score_2']],
  		  verbose=True,
  		  epochs = 100,
  		  batch_size=16384)
```

Ahora que ya has ajustado tu modelo, echemos un vistazo. Puedes utilizar el método « `.get_weights()` » para inspeccionar los pesos de tu modelo.

La capa de entrada tendrá 4 pesos: 2 por cada entrada por 2 por cada salida.

La capa de salida tendrá 2 pesos, uno para cada salida.

```python
# Print the model's weights
print(model.get_weights())

# Print the column means of the training data
print(games_tourney_train.mean())
```

Ahora que ya has ajustado tu modelo y has comprobado sus pesos para asegurarte de que tiene sentido, evalúalo en el conjunto de pruebas del torneo para ver cómo funciona con datos nuevos.

```python
# Evaluate the model on the tournament test data
print(model.evaluate(games_tourney_test[['seed_diff', 'pred']],
  		  games_tourney_test[['score_1', 'score_2']], verbose=False))
```

### Single model for classification and regression

Ahora crearás un modelo diferente de 2 salidas. En esta ocasión, predice la diferencia de puntos, en lugar de los puntos de ambos equipos, y luego predice la probabilidad de que el equipo 1 gane el partido. Este es un modelo muy interesante: ¡va a realizar tanto la clasificación como la regresión!

En este modelo, desactiva el sesgo o el intercepto para cada capa. Tus entradas (diferencia entre semillas y diferencia de puntuación prevista) tienen una media muy cercana a cero, y tus salidas tienen medias cercanas a cero, por lo que tu modelo no debería necesitar el término de sesgo para ajustarse bien a los datos.

```python
# Create an input layer with 2 columns
input_tensor = Input(shape=(2,))

# Create the first output
output_tensor_1 = Dense(1, activation='linear', use_bias=False)(input_tensor)

# Create the second output (use the first output as input here)
output_tensor_2 = Dense(1, activation='sigmoid', use_bias=False)(output_tensor_1)

# Create a model with 2 outputs
model = Model(input_tensor, [output_tensor_1, output_tensor_2])
```

Ahora que tienes un modelo con dos salidas, compílalo con dos funciones de pérdida: error absoluto medio (MAE) para `'score_diff'` y entropía cruzada binaria (también conocida como logloss) para `'won'`. A continuación, ajusta el modelo con `'seed_diff'` y `'pred'` como entradas. Para los resultados, pronostica `'score_diff'` y `'won'`.

Este modelo puede utilizar las puntuaciones de los partidos para garantizar que los partidos reñidos (pequeña diferencia de puntuación) tengan menos probabilidades de ganar que los partidos desiguales (gran diferencia de puntuación).

El problema de regresión es más sencillo que el de clasificación, ya que el MAE penaliza menos al modelo por una pérdida debida al azar. Por ejemplo, si `score_diff` es -1 y `won` es 0, eso significa que `team_1` tuvo mala suerte y perdió por un solo tiro libre. Los datos del problema fácil ayudan al modelo a encontrar una solución al problema difícil.

```python
# Import the Adam optimizer
from tensorflow.keras.optimizers import Adam

# Compile the model with 2 losses and the Adam optimzer with a higher learning rate
 
model.compile(loss=['mean_absolute_error', 'binary_crossentropy'], optimizer = Adam(learning_rate = 0.01))

# Fit the model to the tournament training data, with 2 inputs and 2 outputs
model.fit(games_tourney_train[['seed_diff', 'pred']],
          [games_tourney_train[['score_diff']], games_tourney_train[['won']]],
          epochs=10,
          verbose=True,
          batch_size=16384)
```

Ahora deberías echar un vistazo a los pesos de este modelo. En particular, ten en cuenta el último peso del modelo. Este peso convierte la diferencia de puntuación prevista en una probabilidad de victoria prevista. Si multiplicas la diferencia de puntuación prevista por el último peso del modelo y luego aplicas la función sigmoide, obtienes la probabilidad de victoria del juego.