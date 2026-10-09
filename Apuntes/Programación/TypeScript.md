#Programming 
# Tutorial 1

## Tipar Arrow Functions

```tsx
// Forma recomendada
/**
* a: number, b: number -> tipar parámetros
* ): number => -> tipar el tipo de dato que devuelve la función 
*/
const sumar = (a: number, b: number): number => {
	return a + b
}

// Forma menos legible
/**
* const resta: -> declaras la función 
* (a: number, b: number) => number -> tipas la función (parámetros y return)
* = (a, b) => -> haces la función 
*/
const resta: (a: number, b: number) => number = (a, b) => {
	return a - b
}

// never, no devuelve nada, solo lanza una excepción (sabes que nunca se va a terminar
// de ejecutar la función)
function thowError(message: string): never {
	throw new Error(message)
}

// La diferencia entre void y never es que never nunca llega a un return implícito porque
// antes lanza una excepción, void llega a un return implícito en el que la función
// devuelve nada
function logMessage(message: string): void {
	console.log(message)
	//return implícito <---- void -> no devuelve nada
}

// inferencia funciones anónimas según el contexto
const avengers = ['Spidey', 'Hulk', 'Avengers'] // -> Array de Strings

// Al iterar sobre un array de Strings, TS hace inferencia sobre 'avenger' que es una
// función anónima. Por lo que sabe que avenger es un String
avengers.forEach( avenger => {
	console.log(avenger.toUpperCase())
}
```

## Inferencia objetos

```tsx
let hero = {
 name: 'thor',
 age: 1500
};

hero.power = 100 // -> error, no existe la propiedad 'power' en 'hero'

function createHero(name: string, age: number) {
	return { name, age }
}

const thor = createHero('Thor', 1500)

//Type Alias

//Podemos crear nuestros propios tipos
type Hero = {
	name: string
	age: number
}

let hero: Hero = {
 name: 'thor',
 age: 1500
};

function createHero(name: string, age: number): Hero {
	return { name, age }
}

const thor = createHero('Thor', 1500)

//Optional properties

type Hero = {
	name: string
	age: number
	isActive?: boolean //si no le ponemos interrogación nos daría error por todos lados ya
	// que hemos instanciado un héroe sin establecer el valor del atributo 'isActive'
}

let hero: Hero = {
 name: 'thor',
 age: 1500
};

function createHero(name: string, age: number): Hero {
	return { name, age }
}

const thor = createHero('Thor', 1500)
```

## Object Freeze

```tsx
const thor = Object.freeze(createHero({ name: 'Thor', age: 1500 })) // -> hace el objeto inmutable (que no se pueda cambiar)
```

## Template Union Types

```tsx
//Definimos el formato concreto que queramos que tenga el id del héroe
type HeroId = `${string}-${string}-${string}-${string}-${string}`
type HeroPowerScale = 'local' | 'planetary' | 'galactic' | 'universal' | 'multiversal' // Opciones de cual puede ser la escala de poder del héroe 

let a: number | string // puede ser o un número o un string

//Asociamos ese formato a la propiedad id de nuestro tipo héroe
 type Hero = {
	 readonly id?: HeroId //<---
	 name: string
	 age: number
	 isActive?: boolean
 }
 
 function createHero(hero: Hero): Hero{
	 const { name, age } = hero
	 return {
		 id: '123', // Error (no tiene el formato especificado)
		 name,
		 age,
		 isActive: true
	 }
 }
 
 thor.powerScale = 'To Fuerte' // Error
 
 -----------------------------
 type HexColor = `#${string}`
 const color = '00fff0' //hex <-- Error (no tiene #)
 const color2 = '#000fff' //hex <-- Bien
```

## Intersection Types

```tsx
type HeroBasicInfo = {
	name: string,
	age: number
}

type HeroProperties = {
	readonly id?: HeroId,
	isActive?: boolean,
	powerScale?: HeroPowerScale
}

type Hero = HeroBasicInfo & HeroProperties // Un tipo que es la suma de dos tipos
```

## Type indexing

```tsx
type HeroProperties = {
	isActive: boolean,
	address: {
		planet: string,
		city: string
	}
}

const addressHero: HeroProperties['address'] = {
	planet: 'Earth',
	city: 'Madrid'
}
```

## Type from value

```tsx
const address = {
	planet: 'Earth',
	city: 'Madrid'
}

type Address = typeof address

const addressCasa: Address = {
	planet: 'Mars',
	city: 'Madrid'
}

//Type from function return

function createAddress(){
	return {
		planet: 'Tierra',
		city: 'Barcelona'
	}
}

type Address = ReturnType<typeof createAddress>
```

## Arrays

```tsx
const languages: string[] = []
//const languages: Array<string> = [] -> otra opción

languages.push('JavaScript')
languages.push('JavaScript')
languages.push('JavaScript')
languages.push('2') // Error

const array: (string | number)[] = [] //array de strings o numeros
```

## Matrices y tuplas

```tsx
type CellValue = 'X' | 'O' | '' //Definimos el contenido 
type GameBoard = [
	[CellValue, CellValue, CellValue], //Definimos la estructura
	[CellValue, CellValue, CellValue],
	[CellValue, CellValue, CellValue]
]

const gameBoard: CellValue[][] = [
	['X', 'O', 'X'],
	['O', 'X', 'O'],
	['X', '', 'O']
]

gameBoard[0][1] = '2341234' //Error

//Tuplas

type State = [string, (newName: string) => void]
const [hero, setHero]: State = useState('thor')

type RGB = [number, number, number]
const rgb: RGB = [2, 5, 6, 'f'] // Error, tiene 4 elementos en vez de 3 y uno no es un number
```

# Tutorial 2

## Enums

```tsx
//Hay NO usar 'const' cuando estemos creando una librería/componente que vaya a ser usado fuera de nuestra aplicación
const enum ERROR_TYPES {
	NOT_FOUND, // 0
	UNAUTHORIZED, // 1
	FORBIDDEN // 2
}

function mostrarMensaje (tipoDeError: ERROR_TYPE){
	if (tipoDeError === ERROR_TYPES.NOT_FOUND) {
		console.log('No se encuentra el recurso')
	}else if (tipoDeError === ERROR_TYPES.UNAUTHORIZED){
		console.log('No tienes permisos para acceder')
	}else if (tipoDeError === ERROR_TYPES.FORBIDDEN){
		console.log('No tienes permisos para acceder')
	}
}
```

## Aserciones de tipos

```tsx
const canvas = document.getElementById('canvas') //as HTMLCanvasElement

// null si no lo encuentra
// HTMLElement si lo encuentra
// HTMLCanvasElement es el que necesitamos, se lo tenemos que especificar

// ??? cómo sabe TS que realmente estás recuperando un elemento <canvas/>?

// inferencia --> TS se da cuenta que dentro del if
// ya solo el canvas va a poder ser un HTMLCanvasElement
if(canvas !== null && canvas instanceof HTMLCanvasElement){
	const ctx = canvas.getContext('2d')
}
```

## Aserciones Fetching

```tsx
// fetching de datos en TypeScript

const API_URL = "repositorios_js" //devuelve repositorios

//tomamos la respuesta
const response = await fetch(API_URL)

//Si la respuesta no es "ok", lanzamos error
if(!response.ok){
	throw new Error('Request failed')
}

//Almacenamos los datos en formato json
const data = await response.json()

//Mapeamos los repos
const repos = data.items.map(repo => {
	console.log(repo)
})
```

## Interfaces

```tsx
//Parecido a los Types
interface Heroe{
	id: string
	name: string
	age: number
	saludar: () => void
}

const hero: Hero = {
	id: '1',
	name: 'Spiderman',
	age: 30,
	saludar: () => {
		console.log('Hola')
	}
}

//Podemos usar interfaces dentro de interfaces
interface Producto {
	id: number
	nombre: string
	precio: number
	quantity: number
}

//Las interfaces pueden heredar de otras interfaces
interface Zapatilla extends Producto{
	talla: number
}

interface CarritoDeCompras = {
	totalPrice: number
	productos: Zapatilla[]
}

const carrito: CarritoDeCompras = {
	totalPrice: 100,
	productos: [
		id: 1,
		nombre: 'Producto 1',
		precio: 100,
		quantity: 1,
		talla: 44
	]
}
```

### Indicar funciones en interfaces

```tsx
//Se puede "instanciar" dos veces o más la misma interfaz con distintos métodos (no recomendable)
interface CarritoOps {
    add: (product: Producto) => void,
    remove: (productId: number) => void,
}

interface CarritoOps {
    clear: () => void,
}

const carrito: CarritoOps = {
    add(product) {
        console.log(`Producto ${product.name} agregado al carrito.`);
    },
    remove(productId) {
        console.log(`Producto con ID ${productId} removido del carrito.`);
    },
    clear() {
        console.log('Carrito vaciado.');
    }
};
```

### Diferencia entre Types e Interfaces

Es recomendable usar Types siempre que se pueda. Sin embargo, las interfaces al ser un elemento clásico de la orientación a objetos, es más fácil identificar su uso cuando usamos clases y objetos. Por lo cual, en esos casos podríamos usar interfaces.

## Narrowing

```tsx
//Narrowing

interface Mario {
    company: 'Nintendo';
    name: string;
    jump: () => void
}

interface Sonic {
    company: 'SEGA';
    name: string;
    spinDash: () => void;
}

type Personaje = Mario | Sonic;

function jugar(personaje: Personaje) {
    console.log(personaje.saltar()) //Error
}
//no se puede llamar al método saltar porque no existe para el tipo 'Sonic'
//y el tipo Personaje puede ser o Mario o Sonic, entonces no se puede garantizar
//que el método saltar exista para ambos tipos.
```

### Type Guard

```tsx
// Type Guard
// déjame comprobar si personaje es Socin
// y esta función determina si es Sonic o no
function checkIsSonic(personaje: Personaje): personaje is Sonic {
    return (personaje as Sonic).correr !== undefined
}

function jugar(personaje: Personaje){
    if(checkIsSonic(personaje)){
        personaje.correr()
    }
}
```

## Tipo never

```tsx
function fn(x: number | string){
    if(typeof x === 'string'){
        // x es string
        x.toUpperCase()
    } else if (typeof x === 'number'){
        // x es number
        x.toFixed(2)
    } else{
        // x es never
    }
}
```