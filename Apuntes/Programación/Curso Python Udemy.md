#Programming
# 7 - POO en Python

No se definen como tal los atributos de una clase, se hace en el propio constructor

El constructor se escribe: `def __init__(self, "argumentos")`

Todos los métodos de instacia deben recibir `self` como primer parámetro.

Para acceder a los atributos de la clase → `self.atributo`

A diferencia de Java, a la hora de instanciar una clase, no hace falta poner el tipo de dato de la clase: `coche = Coche("argumentos")`

para declarar un atributo de clase (stático) lo declaramos fuera del constructor

### Ejemplo de declaración de clase

```python
class Libro:
    # 1. El constructor SIEMPRE se debe llamar __init__, no __init__NombreClase
    numeroLibros = 0 #atributo de clase
    
    def __init__(self, titulo, autor, isbn, disponible):
        """Constructor de la clase Libro"""
        Libro.numeroLibros += 1
        # 2. Usamos 'self' (convención estándar), no 'self_'
        self.titulo = titulo
        self.autor = autor
        self.isbn = isbn
        self.disponible = disponible
        
    # 3. Todos los métodos de instancia deben recibir 'self' como primer parámetro
    def informacion(self):
        # 4. Cuidado con las mayúsculas: self.titulo, no self.Titulo
        print(f"--- Ficha del Libro ---\nTítulo: {self.titulo} \nAutor: {self.autor} \nISBN: {self.isbn} \nDisponible: {self.disponible}\n")
        
    def prestar_libro(self):
        # Es redundante poner "== True", basta con poner la variable sola
        if self.disponible:
            self.disponible = False
            print(f"El libro '{self.titulo}' ha sido prestado correctamente.")
            self.informacion()
        else:
            print(f"Lo siento, '{self.titulo}' ya está prestado.")
            
    def devolver_libro(self):
        if not self.disponible: # "not disponible" es igual a "disponible == False"
            self.disponible = True
            print(f"El libro '{self.titulo}' ha sido devuelto.")
            self.informacion()
        else:
            print(f"Error: '{self.titulo}' ya estaba en la biblioteca.")

# Creación de objetos
libro1 = Libro("Hábitos Atómicos", "James Clear", "asdf92734", True)
libro2 = Libro("Invicto", "Carlos Parra Camacho", "aksdfasd39234", False)

# Pruebas
libro1.prestar_libro()
libro2.devolver_libro()
```

## Decorators

===========================

### Ejemplo de uso de Decorators

```python
# 1. Definimos el Decorator fuera de la clase
def formato_notificacion(func):
    def wrapper(self, plataforma):
        # Código que se ejecuta ANTES de la función original
        print(f"***** NUEVO EVENTO en {plataforma} *****")
        
        # Ejecutamos la función original (el método de la clase)
        # Es vital pasar 'self' y 'plataforma'
        func(self, plataforma)
        
        # Código que se ejecuta DESPUÉS de la función original
        print(f"***** FIN DEL EVENTO en {plataforma} *****")
        
    return wrapper

# 2. Implementamos la clase
class NotificadorRedSocial:
    def __init__(self, evento):
        # Atributo de instancia para guardar el evento
        self.evento = evento

    # 3. Aplicamos Syntactic Sugar (@) sobre el método
    @formato_notificacion
    def notificar_evento(self, plataforma):
        # Lógica para determinar el mensaje según la plataforma
        if plataforma == "Twitter":
            print(f"Tweet: {self.evento}")
        elif plataforma == "Instagram":
            print(f"Instagram Story: {self.evento}")
        elif plataforma == "Facebook":
            print(f"Post en Facebook: {self.evento}")
        else:
            print("Plataforma no soportada")

# Instancia con el evento del ejemplo
notificador = NotificadorRedSocial("Lanzamiento de un nuevo libro")

# Enviamos notificaciones a varias plataformas para probar
notificador.notificar_evento("Twitter")
notificador.notificar_evento("Instagram")
notificador.notificar_evento("Facebook")
notificador.notificar_evento("LinkedIn")
```

## Herencia en Python

Para hacer que una clase herede de otra clase en Python tenemos que poner la clase padre entre los paréntesis de la declaración de la clase hijo → `Class CocheEléctrico(Coche)` así tenemos una clase CocheEléctrico que hereda de la clase coche.

Para llamar al constructor de la clase padre desde el constructor de la clase hijo hacemos lo siguiente → `super().__init__(args)`

### Sobreescribir métodos de la clase padre

simplemente redeclaramos el método en la clase hija como en cualquier otro lenguaje.

### Ejemplo código herencia

```python
class Animal:
    def __init__(self, nombre, especie, edad, habitat):
        self.nombre = nombre
        self.especie = especie
        self.edad = edad
        self.habitat = habitat # Aquí habitat es un objeto de la clase Habitat
        
    def mostrar_info(self):
        print(f"-> Nombre: {self.nombre}\nEspecie: {self.especie}\nEdad: {self.edad}\nHabitat: {self.habitat.tipo_habitat}")

class Habitat:
    def __init__(self, tipo_habitat, temperatura):
        self.tipo_habitat = tipo_habitat
        self.temperatura = temperatura
        
class Mamifero(Animal):
    def __init__(self, nombre, especie, edad, habitat, tipo_pelaje):
        # Corrección: super() con paréntesis
        super().__init__(nombre, especie, edad, habitat)
        self.tipo_pelaje = tipo_pelaje

    def mostrar_info(self):
        # Corrección: self.tipo_pelaje
        print(f"-> Nombre: {self.nombre}\nEspecie: {self.especie}\nEdad: {self.edad}\nHabitat: {self.habitat.tipo_habitat}\nTipo pelaje: {self.tipo_pelaje}")

class Ave(Animal):
    def __init__(self, nombre, especie, edad, habitat, tipo_plumaje):
        super().__init__(nombre, especie, edad, habitat)
        self.tipo_plumaje = tipo_plumaje
        
    def mostrar_info(self):
        # Corrección: self.tipo_plumaje
        print(f"-> Nombre: {self.nombre}\nEspecie: {self.especie}\nEdad: {self.edad}\nHabitat: {self.habitat.tipo_habitat}\nTipo plumaje: {self.tipo_plumaje}")

class Zoologico:
    def __init__(self, nombre, animales):
        self.nombre = nombre
        self.animales = animales
        
    # Corrección: Añadido self y uso de self.animales
    def añadir_animal(self, animal):
        self.animales.append(animal)
        
    def mostrar_animales(self):
        print(f"\n--- Animales en el {self.nombre} ---")
        for animal in self.animales:
            animal.mostrar_info() # Llamamos al método del animal
            print("-" * 20)

# Uso del programa
animales_lista = []
zoo = Zoologico("Zoo de Huércal-Overa", animales_lista)

sabana = Habitat("Sabana", "Cálido")
bosque = Habitat("Bosque", "Templado")

leon = Mamifero("Simba", "León", 5, sabana, "Corto")
piolin = Ave("Piolín", "Canario", 2, bosque, "Suave")

zoo.añadir_animal(leon)
zoo.añadir_animal(piolin)
zoo.mostrar_animales()
```

[Comprehensions](https://app.notion.com/p/Comprehensions-2f9528c1993a80fdbde7ffb0a7024008?pvs=21)

# 8 - Trabajando con objetos en Python

### Números

**`*int.help()*`** → para ver los métodos que hay para los números enteros

`num.real` → devuelve la parte real de un número

`num.imag` → devuelve la parte imaginaria de un número

`complex(50000, 2.5)` → crea un número complejo con parte real 50000 e imaginaria 2.5

### Strings

**`*str.help()` →*** para ver los métodos que hay para los strings

```python
texto = "HoLA MUnDo"
texto.***capitalize***() #esto devuelve "Hola mundo"
texto.***lower***() #esto devuelve "hola mundo"
texto.***upper***() #esto devuelve "HOLA MUNDO"
texto.***title***() #esto devuelve "Hola Mundo"
texto.***swapcase***() #esto transforma las minúsculas en mayúsculas y viceversa.

#=========================================
#========= 2.Buscar y reemplazar==========
#=========================================

texto = "aoo boo coo"

#Cuenta las veces que se repite una subcadena
texto.***count***("oo") #devuelve "3"

#Devuelve el índice donde se encuentra la subcadena 
texto.***find***("boo") #devuelve 4

#Busca la subcadena empezando por el final
texto.***rfind***("boo") #devuelve 12

#=========================================
#===== 3.Clasificación de caracteres =====
#=========================================

texto1 = "123abc"
texto2 = "123$abc"

#Determina si todos son caracteres alfanuméricos
texto1.***isalnum***() #-> true
texto2.***isalnum***() #-> false

#Determina si todos los caracteres son numeros
texto1.***isdigit***() #-> false

#=========================================
#========== 4.String formating ==========
#=========================================

texto = "hola mundo"
texto.center(20) #-> centra el texto en una cadena de caracteres de tamaño 20
texto.ljust(40) #le añade 40 espacios en blanco a la derecha del texto

texto = "    hola    que    tal   "
texto.lstrip() # devuelve -> "hola    que    tal   "
texto.rstrip() # devuelve -> "    hola    que    tal"
texto.strip() #devuelve -> "hola    que    tal"
texto.replace("a","b") #sustituye caracteres
texto.zfill(8) # devuelve -> "0000Hola"

#Concatena un string con un objeto iterable
", ".join(["azul","verde","rojo"])

"{}-{}-{}".format(3, "hola", [2, 3])
#imprime lo especificado en entre cada llave

```

### Listas

```python
lista = ["azul", "verde", "rojo"]

#añade un nuevo elemento al final de la lista
lista.append("amarillo") # -> azul, verde rojo amarillo

#añade una serie de elementos a una lista
lista.extend(["morado, rojo, blanco"])
# -> azul, verde rojo amarillo morado rojo blanco

#elimina un item de una lista empezando por la izda
lista.remove("azul")

#darle la vuelta a la lista
lista.reverse()

#extraer el último elemento de la lista
lista.pop()

#hacer una copia de unan lista
lista2 = lista.copy()

#=========================================
#=Accediendo a los elementos de una lista=
#=========================================

#devuelve la posición en la que se encuentra un elemento empezando por la izquierda
lista.index("verde")

#devuelve cuantos elementos hay en una lista
lista.count("azul")
```

### Tuplas

```python
tupla = ("azul", "rojo", "verde", "azul", "azul")

#Cuenta el número de veces que aparece un elemento en la tupla
tupla.count("azul") -> 3
tupla.count("amarillo") -> 0

#devuelve la posición en la que se encuentra un elemento empezando por la izquierda
tupla.index("rojo") -> 1
```

### Sets

```python
set1 = {'azul', 'rojo', 'verde'}
set2 = {'morado', 'rojo', 'amarillo'}

#Hacer la intersección entre dos sets, devuelve los elementos comunes a ambos sets
set1.intersection(set2) #-> 'rojo'

#Unión de dos sets, un set nuevo con los elementos que no se repiten en ambos sets
set1.union(set2)
#-> 'amarillo', 'azul', 'morado', 'rojo', 'verde' 

#dice si un set es subconjunto de otro (True o False)
set1.issubset(set2)

#Unión de dos sets, un set nuevo con los elementos distintos de un set y otro
set1.symetric_difference(set2)
```

### Diccionarios

```python
diccionario = {
	'key1' : 1,
	'key2' : 2
}

#actualizar clave de diccionario
#Si la clave especificada no existe, crea una nueva clave con el valor especificado
diccionario.update({'key1' : 10})

#pop
diccionario.pop("Key1")

#método copy
diccionario2 = diccionario.copy()

#limpia el diccionario
diccionario.clear()

#============================================
#Accediendo a los elementos de un diccionario
#============================================

diccionario = {
	'key1' : 1,
	'key2' : 2,
	'key3' : 3
}

#Devuelve los valores del diccionario
diccionario.values()

#Devuleve las claves del diccionario
diccionario.keys()

#Nos devuelve los items del diccionario en forma de tupla clave-valor
diccionario.items()
#Así podemos iterar fácilmente los elementos de un diccionario imprimiendo clave y valor
for key, value in diccionario.items():
	print("{} -> {}".format(key,value))
```

# 9 - Programación modular en Python

## Módulos

```python
import math #importamos el módulo math

def funcion():
	return math.pi * 2 #-> usamos una función de math
	
-----------------------------------------------------------------

from math import pi #importamos solo la función pi

def funcion():
	return pi * 2
	
-----------------------------------------------------------------

from math import pi as funcionPi

def funcion():
	return funcionPi * 2
```

## Paquetes

los paquetes son directorios en los que encontramos distintos módulos, los paquetes tienen un archivo **init**.py que define que ese directorio es un paquete y tratará los archivos como módulos de python

Para importar un módulo de un paquete hacemos lo siguiente:

```python
from mipaquete.modulo import funcionModulo
```

Si queremos importar todos los módulos de un paquete debemos crear una lista__**all__** en el ****init**** en la que definamos los módulos que queremos importar. Es decir:

```python
__all__ = [
	'modulo1',
	'modulo2'
]

-------------------------------------------

from mipaquete import *

#al tener definida la lista __all__ sí podemos usar esta sintaxis. De no tenerla,        nos daría un error
```

### ****name****

Es una variable que guarda el modo de ejecución de código en python

Se usa en una sentencia ‘if’ → `if __name__ == "__main__":`

Esto sirve para que un módulo pueda ser tanto ejecutado independientemente como importado. Dentro de esta sentencia deberá ir el código que queremos que se ejecute cuando queramos ejecutar ese módulo en concreto de forma independiente.

Es decir, la variable `“__name__”` tiene el valor `“__main__”` en el módulo que estamos ejecutando.

```python
def funcion():
	print("Hola mundo")
	
if __name__ == "__main__"
print("Hola")
funcion()
```

Si nosotros ejecutamos este módulo de forma independiente, se imprimiría tanto “Hola” ocmo “Hola mundo”. En cambio, si importamos el módulo, solo se ejecutaría lo que está fuera de la sentencia _if_

## PIP

pip es el gestor de paquetes de python

```bash
#Actualizar nueva versión de pip
python -m pip install --upgrade pip

#Instalar paquetes 
pip install "paquete"

#listar los paquetes que hay en nuestro entorno
pip list

#mostrar los detalles de un paquete instalado
pip show "paquete"

#Exportar los paquetes instalados en el entorno
pip freeze > requirements.txt

#instalando requirements.txt
pip install -r requirements.txt

#Desinstalar paquetes
pip uninstall "paquete"
pip uninstall -r requirements.txt
```

## Entorno virtual

```bash
#Creación de entorno virtual
python -m venv nombre_entorno

#Activar entorno virtual
"entorno"\Scripts\activate

#desactivar
deactivate
```

# 10 - Excepciones en Python

### Listado de Excepciones

```python
Exception

#Base class for all exceptions

StopIteration

#Raised when the next() method of an iterator does not point to any object.

SystemExit

#Raised by the sys.exit() function.

StandardError

#Base class for all built-in exceptions except StopIteration and SystemExit.

ArithmeticError

#Base class for all errors that occur for numeric calculation.

OverflowError

#Raised when a calculation exceeds maximum limit for a numeric type.

FloatingPointError

#Raised when a floating point calculation fails.

ZeroDivisonError

#Raised when division or modulo by zero takes place for all numeric types.

AssertionError

#Raised in case of failure of the Assert statement.

AttributeError

#Raised in case of failure of attribute reference or assignment.

EOFError

#Raised when there is no input from either the raw_input() or input() function and the end of file is reached.

ImportError

#Raised when an import statement fails.

KeyboardInterrupt

#Raised when the user interrupts program execution, usually by pressing Ctrl+c.

LookupError

#Base class for all lookup errors.

IndexError

#Raised when an index is not found in a sequence.

KeyError

#Raised when the specified key is not found in the dictionary.

NameError

#Raised when an identifier is not found in the local or global namespace.

UnboundLocalError

#Raised when trying to access a local variable in a function or method but no value has been assigned to it.

EnvironmentError

#Base class for all exceptions that occur outside the Python environment.

IOError

#Raised when an input/ output operation fails, such as the print statement or the open() function when trying to open a file that does not exist.

OSError

#Raised for operating system-related errors.

IndentationError

#Raised when indentation is not specified properly.

SystemError

#Raised when the interpreter finds an internal problem, but when this error is encountered the Python interpreter does not exit.

SystemExit

#Raised when Python interpreter is quit by using the sys.exit() function. If not handled in the code, causes the interpreter to exit.

TypeError

#Raised when an operation or function is attempted that is invalid for the specified data type.

ValueError

#Raised when the built-in function for a data type has the valid type of arguments, but the arguments have invalid values specified.

RuntimeError

#Raised when a generated error does not fall into any category.

NotImplementedError

#Raised when an abstract method that needs to be implemented in an inherited class is not actually implemented.
```

### Gestión de Excepciones

```python
try:
	<sentencia(s)>
except <excepción>:
	<sentencia(s) si excepción>
	
#Podemos crear una instancia del objeto de la excepción para usarla

except <excepción> as error:
	print("La excepción consiste en: " error)
```

### Excepciones Personalizadas

con la palabra reservada `raise` podemos levantar excepciones en determinadas situaciones.

Ejemplo:

```python
colores_permitidos = ("verde", "rojo", "azul")

color = "amarillo"

if color not in colores_permitidos:
	raise Exception(f"El color {color} no está en la lista de colores permitidos")
```