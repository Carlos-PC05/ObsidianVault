## 19 Nov 2026
A esta altura ya he leído superficialmente los 6 papers facilitados por mi tutora y he documentado cómo puede ayudar cada uno de ellos en la elaboración de mi TFG.

## 21 Nov 2026
### Pruebas con un modelo de IA local -> **Qwen3-0.6B**

Esta mañana he estado haciendo pruebas corriendo este modelo en local, haciendo pruebas para medir la diferencia en el comportamiento del modelo modificando sus pesos.

#### Paso 7: modificar manualmente los pesos

Las pruebas las he realizado con los siguientes prompts: 
```
(
    "¿Cuál es la capital de Francia? Responde en una frase.",
    "Calcula 17 + 25 y explica el cálculo en una frase.",
    "Completa la secuencia: 2, 4, 8, 16, ...",
    "Explica en dos frases qué es una red neuronal.",
)
```

todo el proceso lo he ido versionando en Github en este repositorio: https://github.com/Carlos-PC05/lab-llm-tune
Las modificaciones de los pesos las he realizado en la primera capa de atención del modelo haciendo en total 4 iteraciones:
1. +0,1
2. +1
3. +10
4. +1000

Tanto en la iteración 1 como en la 2 he tenido la misma respuesta por parte del modelo a los 4 prompts. Sin embargo en la 3 ya he empezado a observar cambios en el comportamiento del modelo cambiando su respuesta en el prompt 4. En la iteración 4 he observado discrepancia tanto en el prompt 3 como en el 4.

#### Paso 8 — Primer entrenamiento mediante gradientes

Después de modificar manualmente un peso de Qwen3, he hecho un ejercicio más pequeño para entender de dónde sale el cambio de un peso durante el entrenamiento. En `train_linear.py` he entrenado en CPU una capa de PyTorch con un peso y un sesgo, cuya predicción es $\hat{y} = Wx + b$. Le he dado cuatro pares, $(1, 2)$, $(2, 4)$, $(3, 6)$ y $(4, 8)$, para que aprenda la relación **$y = 2x$**.

He usado el error cuadrático medio (MSE) para medir cuánto se alejan las predicciones de los valores esperados. En cada iteración, `optimizer.zero_grad()` limpia los gradientes acumulados, `loss.backward()` calcula los gradientes del peso y del sesgo, y `optimizer.step()` actualiza ambos parámetros con SGD y una tasa de aprendizaje de 0,01.

En el primer paso, el gradiente del peso ha sido −14,381882. Al restarle al peso inicial el producto de la tasa de aprendizaje por ese gradiente, el peso ha subido de 0,764539 a 0,908357. He comprobado que el valor calculado a mano coincide con el que aplica PyTorch.

Tras 1000 iteraciones, el peso ha quedado en 1,980994 y el sesgo en 0,055880. La pérdida ha bajado de 7,009437 a 0,000522. Al probar con $x = 5$, que no formaba parte de los datos de entrenamiento, la predicción ha sido 9,960850 frente al valor esperado de 10.

***Me quedo con que el gradiente indica cómo cambia la pérdida al variar cada parámetro y que la tasa de aprendizaje controla el tamaño del paso. Esto me da una base para entender cómo se calculan las actualizaciones cuando haga fine-tuning.***

#### Paso 9 — Primer fine-tuning con LoRA

Después del ejercicio con la capa lineal, he hecho mi primera prueba de *fine-tuning* con Qwen3-0.6B. He usado LoRA para mantener fijos los pesos originales del modelo y entrenar unas matrices pequeñas en dos partes de las capas de atención, `q_proj` y `v_proj`. Así he podido entrenar alrededor del 0,1 % de los parámetros.

Para la prueba he preparado tres preguntas con sus respuestas sobre logs de sistema, Docker y uso de CPU. He configurado el entrenamiento para que use la pregunta como contexto y calcule la pérdida sobre la respuesta y su marca de fin. Con estos tres ejemplos he comprobado que el entrenamiento funciona y que puedo guardar el adaptador. Para saber cómo responde a preguntas nuevas tendré que ampliar el conjunto y reservar ejemplos para validación.

Primero he hecho la prueba en CPU, luego en mi RX 5600 XT. El script detecta la GPU automáticamente y usa la CPU si PyTorch no encuentra una compatible. Al terminar he guardado el adaptador por separado en `checkpoints/qwen3-0.6b-lora/`.

***Me quedo con que puedo adaptar el modelo entrenando una parte muy pequeña y guardar ese ajuste para volver a usarlo con el modelo original.***

A continuación dado que este primer fine-tuning con LoRA ha sido muy rápido lo he vuelto a realizar con una proporción más grande del modelo. La configuración ampliada aplica LoRA de rango 8 a las cuatro proyecciones de atención y a las tres de la MLP en cada bloque. Los parámetros entrenables pasaron de **573 440 a 5 046 272**, unas **8,8 veces más**.

#### Comparación de los dos adaptadores LoRA

He comparado el modelo base y los dos adaptadores con las mismas nueve preguntas. He usado las tres preguntas de entrenamiento, tres reformulaciones de esas preguntas y tres sobre temas nuevos. Para cada pregunta he medido la pérdida sobre los tokens de la respuesta esperada. Cuanto menor es la pérdida, más probabilidad le da el modelo a esa respuesta.

| Preguntas | Qwen3 base | LoRA original | LoRA ampliado |
| --- | ---: | ---: | ---: |
| Entrenamiento | 3,904 | 3,317 | **2,037** |
| Reformulaciones | 3,891 | 3,516 | **2,619** |
| Temas nuevos | 3,202 | 2,804 | **2,268** |

La versión ampliada ha obtenido una pérdida menor en las nueve preguntas. La mayor mejora media se ha dado en las preguntas de entrenamiento, pero también he visto mejoras en las reformulaciones y en los temas nuevos.

***Me quedo con que ampliar LoRA ha dado mejores resultados en esta prueba.*** La muestra es pequeña y todavía me falta comparar las respuestas que genera cada versión por sí sola. Como he subido el rango de 4 a 8 a la vez que he añadido más proyecciones, tendría que probar cada cambio por separado para saber cuánto aporta.

#### Paso 10 — Comparar las respuestas de los modelos ajustados

He cargado los dos adaptadores guardados con `test_lora.py` y he hecho la misma pregunta al modelo base y a las dos versiones con LoRA. Esta vez he preguntado **«¿Para qué sirve una copia de seguridad?»**, un tema que no estaba en los tres ejemplos de entrenamiento.

**Qwen3 base**

> Una copia de seguridad sirve para:
>
> 1. **Evitar daños** en la información o dispositivos que no estén en uso.
> 2. **Proteger datos sensibles** en caso de incidentes, como un ataque o un malentendido.
> 3. **Reparar o restaurar información** en caso de que se de error o que se

**LoRA original**

> Una copia de seguridad sirve para:
>
> 1. **Evitar daños** en la información o dispositivos que no estén en uso.
> 2. **Proteger datos sensibles** de alteraciones o accesos no autorizados.
> 3. **Reparar o recuperar información** en caso de daño o pérdida.
> 4. **Reparar o recuperar datos**

**LoRA ampliado**

> Una copia de seguridad sirve para proteger datos y evitar que se destruyan.

Me ha llamado la atención que el modelo base y el LoRA original empiezan casi igual y quedan cortados por el límite de 80 tokens. El LoRA ampliado acaba en una sola frase, más parecida al estilo de las respuestas de entrenamiento. Aun así, «evitar que se destruyan» es impreciso. Una copia de seguridad permite recuperar los datos si los originales se pierden o se dañan.

***Me quedo con que, en este ejemplo, la versión ampliada ha sido más breve, pero el LoRA original ha mencionado con más claridad la idea de recuperar los datos.*** Esto me ayuda a entender por qué también tengo que leer las respuestas que genera cada versión, además de comparar la pérdida.
