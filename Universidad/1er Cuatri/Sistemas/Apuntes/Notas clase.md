---
tags:
  - sistemas
  - universidad
---
# Lunes 28 Sep
Equipamiento que hay en un CPD

Qué pasa cuando hay 2 tierras en un mismo edificio? -> es necesario que ambas tierras estén conectadas entre sí 

la auseancia absoluta de humedad es tan peligrosa como el exceso de humedad para los equipos electrónicos

Qué quiere decir que ethernet es una tecnología de mejor esfuerzo -> que dependiendo del entorno, siempre lo va a hacer lo mejor que se pueda (no podemos garantizar el acceso al dato en un tiempo concreto)

Técnicas de enfriamiento de Data Centers

# Martes 29 Sep

### Grid computing
Grid computing - sistemas distribuidos geográficamente que utilizan las comunicaciones para ponerse de acuerdo en base a un determinado protocolo. Trabajan en la solución de un mismo problema. 

Schedulers/Dispatchers - son los que distribuyen el trabajo entre los distintos nodos del grid.

Ejemplo de Grid Computing - Minado de Bitcoin

### Qué es un Cluster

Qué es un cluster - un conjunto de ordenadores interconectados por una red de alta velocidad. El conjunto es visto como una única unidad. Empleados para mejorar el rendimiento (HPC) y la eficiencia (HTC) y la disponibilidad (HA), siendo además más económico que computadoras infividuales.

Los componentes de hardware de un cluster son básicos, como los de cualquier PC. Son capaces de correr un sustema operativo Unix, con adaptadores Ethernet estándar. No contienen nigún componenete hardware personalizado y es fácilmente repoducible.

### Virtualización

Abstracción de los recursos físicos de la máquina para que de la sensación de cada SO corriendo en ese mismo harware, sea una máquina independiente.

Los sistemas con máquinas virtuales permiten compartir los recursos físicos de la máquina anfritiona (host) entre diferentes máquinas virtuales huéspedes (guest), cada una ejecutando su propio sistema operativo.

IaaS, PaaS, SaaS

### Jueves 1 Octubre

Tendencia - consolidación. Reducir el número de dispositivos a gestionar se minimizan las formas de gestionarlo.
Una infraestructura más simple permite una gestión más eficiente.

Mantener los servicios en cloud es mucho más caro que en local, nunca acaba rentabilizándose al largo plazo.

#### El administrador de Sistemas

Persona encargada de administrar y configurar el sistema y de arreglar los problemas informáticos.

¿Qué se espera de un administrador de sistemas? 
- Amplios conocimientos de redes
- Amplios conocimientos de SSOO
- Amplios conocimientos de Servicios y Tecnología
- Conocimientos de hardware y del entorno
- Formación continua

Antes de hacer ningún cambio, planificarlo. Hay que saber las consecuencias que tiene cualquier cambio antes de implementarlo. Tratar de hacer siempre el cambio reversible.

Realizar cambios incrementales con comprobaciones de por medio.

Un cuaderno de bitácora es un documento en el que vas apuntando lo que vas haciendo (como si fuera un diario). En esencia para apuntar los cambios que se van haciendo en un sistema (Trazabilidad de los hechos)

# Lunes 5 de Octubre

Virtualizar - tener algo en un estado y hacerlo parecer que se comporta o que está en un estado diferente.

# Martes 6 de Octubre

Servicio Cloud - servicio de pago por uso (de infraestructura)
Virtualización - permite particionar los recursos físicos y brindarle al usuario una máquina (vitual) con los requerimientos que necesita.

![[Pasted image 20261006175539.png]]

En virtualización es el **administrador** quien gestiona el hipervisor y las MVs creadas.
En un sistema cloud dichas tareas de gestión se realizan automáticamente mediante APIs

#### Tipos de Virtualización
###### Virtualización de plataforma
Consiste en la abstracción de todo el hardware subyacente de una plataforma. Múltiples instancias de sistemas operativos puedan ejecutarse de manera independiente.

**Emulación nativa**:
- Se emula un tipo de arquitectura en otra
- Se replica el hardware para que varias VM puedan ejecutarse concurrentemente.
**Para-virtualización**:
-  La pila de virtualización (hypervisor) no necesariamente emula el hardware, sino que en su lugar (o además) ofrece una serie de APIs (*hypercalls*) para que un SO modificado las utilice cuando esté ejecutando en una máquina virtual.

El registro de Windows es una base de datos que almacena todos los parámetros de configuración del sistema operativo y mantiene configuraciones anteriores.