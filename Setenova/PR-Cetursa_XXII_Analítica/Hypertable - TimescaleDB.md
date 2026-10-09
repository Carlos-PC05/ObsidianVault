### ¿Qué es una hypertable?

Una **hypertable** es básicamente la forma que tiene TimescaleDB de tratar una tabla que contiene **muchísimos datos que llegan ordenados por tiempo**.

Por ejemplo, imagina `cetursa_metrics_raw`:

| timestamp        | sensor | temperatura |
| ---------------- | ------ | ----------: |
| 08:00            | S01    |        12.3 |
| 08:01            | S01    |        12.5 |
| 08:02            | S01    |        12.4 |
| ...              | ...    |         ... |
| 2026-09-08 10:00 | S01    |        13.1 |

Si tienes millones o cientos de millones de registros de este estilo, TimescaleDB puede dividir internamente la información en **chunks temporales**.

Conceptualmente:

```text
cetursa_metrics_raw
        │
        ├── chunk → enero
        ├── chunk → febrero
        ├── chunk → marzo
        ├── chunk → abril
        └── ...
```

Tú sigues haciendo:

```sql
SELECT *
FROM cetursa_metrics_raw
WHERE timestamp > NOW() - INTERVAL '24 hours';
```

como si fuese una tabla normal, pero TimescaleDB puede trabajar internamente solo con los chunks relevantes.

Esto es especialmente útil para:

* datos de sensores/telemetría
* métricas
* logs
* eventos
* series temporales
* datos que crecen continuamente
* retención automática de datos antiguos

Y ahí es donde **TimescaleDB encaja muy bien con lo que ya estabais haciendo en QuestDB**.

---

## ¿Por qué no hacer todo hypertable?

Porque **no todas las tablas son series temporales**.

Por ejemplo:

```text
users
roles
zones
fichas
jobs
tokens
```

normalmente contienen información relacional.

Imagina:

```text
api_users
----------------
id          PK
username
email       UNIQUE
password
...
```

Aquí quieres las características normales de PostgreSQL:

```text
PRIMARY KEY
FOREIGN KEY
UNIQUE
CHECK
...
```

No tiene mucho sentido convertir esto en una hypertable simplemente porque estés utilizando TimescaleDB.

---

## En vuestro caso

Por los nombres que te ha dado el agente, yo lo organizaría así:

### 🟢 Hypertables

**`cetursa_metrics_raw`**
Datos temporales de alto volumen.

**`cetursa_metrics_silver`**
Datos temporales procesados.

**`cetursa_metrics_gold`**
Datos temporales agregados/procesados.

Y posiblemente:

**`api_audit_logs`**
si crece mucho y cada registro tiene timestamp.

**`api_activity_logs`**
igual.

**`webcam_images`**
Aquí tendría más cuidado. Depende de cómo esté diseñada la tabla. Si básicamente registra:

```text
timestamp
webcam_id
image
...
```

y almacena continuamente capturas, puede tener sentido. Pero no la convertiría automáticamente en hypertable sin mirar el modelo y el volumen.

---

### 🔵 Tablas PostgreSQL normales

Por ejemplo:

```text
xxii_jobs
xxii_zones
xxii_fichas
api_roles
api_users
api_tokens
```

Estas son tablas relacionales/maestras.

Ahí quieres:

```sql
PRIMARY KEY
FOREIGN KEY
UNIQUE
INDEX
CHECK
```

y el comportamiento normal de PostgreSQL.

---

## La frase importante de la pregunta

Tu agente dice:

> "Hypertable solo donde hay PARTITION BY DAY hoy + retención por tiempo."

Esto es una pista bastante clara de **qué tablas están pensando convertir**.

QuestDB probablemente tiene algo parecido a:

```sql
PARTITION BY DAY
```

Eso significa que esa tabla ya está siendo tratada como **datos temporales que se particionan por día**.

En TimescaleDB, una hypertable sería el equivalente conceptual más natural.

Por ejemplo:

```text
QUESTDB                         TIMESCALEDB

PARTITION BY DAY       →        Hypertable
particiones diarias    →        chunks temporales
retención temporal     →        retention policy
series temporales      →        time-series table
```

No es una equivalencia 1:1 técnicamente, pero **es una buena forma de entender la migración**.

---

# ¿Qué opción elegiría?

**a), sin duda.**

Es decir:

> **Solo convertiría en hypertables las tablas que realmente sean series temporales de alto volumen. El resto las mantendría como tablas PostgreSQL normales, aprovechando PK/FK/UNIQUE.**

De hecho, yo se lo respondería a tu agente así:

> **a) Solo raw/silver/gold y, si el volumen lo justifica, audit/activity como hypertables. El resto de tablas relacionales/maestras deben ser tablas PostgreSQL normales con PK/FK/UNIQUE reales. Para webcam_images decidir según volumen y patrón temporal.**

### ¿Por qué?

Porque una de las ventajas importantes de migrar a PostgreSQL + TimescaleDB es precisamente que **no tienes que tratar toda la base de datos como una base de datos de series temporales**.

Puedes tener perfectamente:

```text
                    PostgreSQL + TimescaleDB
                              │
              ┌───────────────┴───────────────┐
              │                               │
       Tablas PostgreSQL                Hypertables
              │                               │
       users / roles / jobs           metrics / logs
       zones / fichas                 series temporales
       relaciones                     millones de registros
       PK / FK / UNIQUE               particionado temporal
```

Y esa arquitectura me parece **mucho más limpia** que convertir absolutamente todo en hypertables.

**Así que para Q4 yo marcaría la opción a).**
