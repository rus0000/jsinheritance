#  Herencia basada de prototipos visualizada

Este post es actual solo para :
* ECMA-262 5th edition called ECMAScript 5 (ES5)
* ECMA-262 6th edition called ECMAScript 2015 (ES6)

Este es un post de nivel principiante para clarificar y visualizar la herencia basada en prototipos de Javascript.

## Motivación
Mucha información incompleta e inclusive incorrecta puede ser encontrada en Internet acerca de la herencia prototípica de Javascript. Solo voy a intetar explicarlo nuevamente pero con diagramas.

Entender el mecanísmo de herencia de Javascript es importante, inclusive si no se planea usar los patrones de Javascript OOP, ya que muchas de las funcionalidades integradas del lenguaje son basadas en la herencia.

No estoy defendiendo el uso de patrones de OOP y herencia "clásica" en Javascript en absoluto. Personalmente prefiero usar patrones como el "factory" o "mixin" antes que el del "constructor". Pero este post no eso sobre patrones, es solo acerca de *mecanismos* de visualización de la herencia basada en prototipos de Javascript.

**Notación de diagramas:**
* Los bloques son objetos de Javascript
* El título de ese bloque denota un ancestro de ese objeto
* Todas las demas secciones en un bloque son propiedades de ese objeto
* Las flechas hacen referencia a que dicha propiedad mantiene una referencia al objeto al cual apunta. El origen de la flecha es importante ya que identifica a la misma propiedad pero su dirección no ya que siempre apunta a un objeto.
* La cadena de Prototipo, la cual es usada por la herencia del sistema de Javascript, es coloreada en rojo
* Las propiedades integradas no son listadas y son abreviadas por `<built-ins>`

## TL;DR
Este post es sobre los siguientes diagramas: 

<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/Function-Object.png" target="_blank"><img src="./images/Function-Object.png" width="233" height="154" alt="Function - Object relationship" title="Function - Object relationship"></a>
<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/foo.png" target="_blank"><img src="./images/foo.png" width="233" height="154" alt="Function in JavaScript" title="Function in JavaScript"></a>
<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/simple-object.png" target="_blank"><img src="./images/simple-object.png"  width="233" height="154" alt="Creating simple objects with inheritance" title="Creating simple objects with inheritance"></a>
<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/bar.png" target="_blank"><img src="./images/bar.png" width="233" height="154" alt="Creating an object with constructor function" title="Creating an object with constructor function"></a>
<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/static.png" target="_blank"><img src="./images/static.png" width="233" height="154" alt="JavaScript static method" title="JavaScript static method"></a>
<a href="https://raw.githubusercontent.com/rus0000/jsinheritance/master/images/oop.png" target="_blank"><img src="./images/oop.png" width="233" height="154" alt="Classical JavaScript inheritance and OOP" title="Classical JavaScript inheritance and OOP"></a>

Si los logran comprender por completo entonces estan listos, de lo contrario debajo se encuentra una explicación detallada.

## Algunos conceptos básicos de Javascript
### Parte Básica
* En Javascript hay funciones y objetos
* No hay `clases` ni `constructores` en el lenguaje. Las `clases` de ES6 son solo "azucar sintáctica"
* No hay `metodos` ó `miembros` en un objeto, solo hay `propiedades`. Excepción: sintaxis especial de `get()` y `set()`. 
* La propiedad de un objeto puede mantener un _valor_ o una _referencia_ a un objeto o función 
* Las Funciones también son objetos, pero de un tipo especial
* Cualquier función puede ser invocada como un constructor, pero esto no quiere decir que *debería* ser invocada de esta forma
* Las Funciones que son pensadas para ser usadas como constructores son llamadas `funciones constructoras`. Las mismas tienen que ser invocadas con la palabra reservada `new` para construir un nuevo objeto
* Por convención, Las funciones constructoras son nombradas con `PascalCase`, todas las demás funciones son nombradas con `camelCase`
* Function en JavaScript puede ser usado como:
  * Una función
  * El método de una instancia
  * Un método estático
  * Un objeto, porque es un objeto
  * Un constructor
  * Un nombre de espacio (spacename)
  * Un closure para capturar contexto
  * ...

### Parte Avanzada
#### Funciones
* Cada **declaración de función** inmediatamente crea **DOS OBJETOS**:
  * El mismo objeto `function`
  * El objeto `prototype`, que le pertenece a esta misma función
  * Eso pasa **antes** que cualquier ejecución de código comience, justo antes de la interpretación del código
* El objeto `function` puede ser accedido usando el nombre de la función sin los parentesis, por ejemplo : `miFuncion`
* El objeto prototype puede ser accedido usando la propiedad `prototype` de un objeto `function`, por ejemplo : `miFuncion.prototype`
* El objeto `prototype` es usado en Javascript cuando una funcion es incovada como `constructor` (con la palabra `new`) para inicializar nuevos objetos construidos con la propiedad `__proto__`
* El objeto `prototype` de una `función constructora` es una reminiscencia de lo que usualmente se almacena en la definición de una `clase` en lenguajes con OOP clásico como Java y C++
* La `función constructora` y su propiedad objeto `prototype` siempre vienen juntos
* El objeto `prototype` no debería ser utilizado en absoluto si la función no va a ser usada como constructora

#### Objetos
* Cada objeto tiene una propiedad integrada `__proto__` 
* La propiedad `__proto__` corresponde a una propiedad interna y oculta `[[Prototype]]` del objeto
* El objeto `function` y su propiedad objeto `prototype` **ambos** también tienen la propiedad `__proto__`
* La propiedad `__proto__` como forma de acceso a su valor es estandarizada solo en ES6. En ES5 la existencia de la propiedad `__proto__` depende de su implementación. En ES5 la manera estandar para acceder al valor de la propiedad `[[Prototype]]` es el método `Object.getPrototypeOf()`
* En ES6 la propiedad `__proto__` puede ser asignada , solo mantiene la referencia a otro objeto. En ES6 también hay un método llamado `Object.setPrototypeOf()` 
* Es posible crear un objeto con la propiedad `__proto__` asignada a `null` usando `var obj = Object.create(null)`
* Object, el cual es referenciado por la propiedad `__proto__` de un objeto dado, es llamado el `padre`. Ese objeto `padre` puede además tener una propiedad `__proto__` a su propio `padre`, formando así la `cadena de prototipos`
* La `cadena de prototipos` de objetos o la `cadena de herencia por prototipos` es la manera como la  **herencia** es implementada en JavaScript
* Cuando JavaScript busca una propiedad con un nombre dado en tiempo de ejecución en un objeto, primero examina el objeto mismo y luego todos los objetos bajo su cadena de prototipos

## Funciones Constructoras Integradas
Esta es una lista de los constructores integrados mas populares de Javascript. Estos son constructores, no solo funciones, objetos o nombres de espacio - esto es **importante!**
* Array
* Boolean
* Date
* Error y sus derivados
* Function
* Math
* Number
* Object
* RegExp
* String

Más lectura : [MDN Standard built-in objects] (https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects)

Los mas confusos son, por supuesto, `Function` y `Object`. Tecnicamente ambos son funciones; funciones constructoras.

## Confusión de términos "Function" y "Object"
Cerrá los ojos y tomalo como viene.

Los autores de JavaScript nombraron una funcion con el nombre de `Object()`. Después hicieron otra función en el sistema con el nombre de `Function()`. Y después hicieron que cada función en el sistema sea un objeto.

Por supuesto que esto es confuso. Pero solo la primera vez, luego te vas acostumbrando.

El orador siempre tiene que ser preciso sobre lo que está hablando.

Significado del término "objecto" ú "object":
* El constructor integrado `Object`
* Objetos específicos de Javascript referenciados por otras rutas
* Un `constructor` con su `prototype`
  * El problema acá es que no tenemos `clases`, y los desarrolladores de Javascript a menudo lo llaman `objeto` pero con el significado de su `tipo`. Es mejor no usar términos como `clase`, `tipo`, `objeto`, `tipo de objeto`, sino llamarlos `constructor` o `función constructora`
* El objeto JSON
  * JSON significa "JavaScript Object Notation"".
  * El típico mal uso y mal entendimiento es que JSON no es un objeto, es siempre un `string`, el cual se convertirá a objeto en memoria solo luego de ser parseado
* POJO, que significa "Plain Old Javascript Object" o solo "simple object" ú "objeto simple"
  * Este es un objeto sin cadena de prototipo customizada o "método añadido", solo un contenedor de información
  * Su propiedad `__proto__` hace referencia directamente a `Object.prototype` o igual a `null`
  * Puede ser considerado un hash

Significados del término "función" ó "function":
* El constructor integrado `Function` 
* Funciones con nombre específicas de Javascript referenciadas por un nombre creado por `declaración de función`
* Funciones anónimas específicas de Javascript referenciadas por otras rutas

## Relación de los constructores Function y Object 
La relación entre los constructores `Function` y `Object` es muy importante. Juega un papel importante en la herencia de Javascript.

Para abreviar:
* Toda función en Javascript es un objeto, mas precisamente dos objetos : la función misma y su propiedad prototype 
* Hay solo dos funciones constructoras integradas relacionadas entre si mismas y son `Function` y `Object`
* El objeto `Function.prototype` hereda de `Object.prototype`
* Toda otra función en el sistema hereda de `Function.prototype`
* Todo objeto en el sistema hereda de `Object.prototype`

![alt Function Object relationship](./images/Function-Object.png "Function Object relationship")

La cadena de herencia por prototipo está dibujada en rojo.

Como se puede ver, `Function` y `Object` ámbos son funciones, entonces, ámbos tienen la propiedad `prototype`,que mantiene una referencia a su objeto `prototipo` respectivo.

`Function` y `Object` ambos son funciones, por esto, su propiedad `__proto__` hace referencia a  `Function.prototype`, el cual a su vez tiene su propiedad `__proto__` haciendo referencia a `Object.prototype`.

Ámbas propiedades `prototype` y `__proto__` de `Function` hacen referencia al mismo objeto `Function.prototype`, lo cual es una situación única, válida solo para el constructor integrado `Function`.

## Confusión de términos "Prototype" y "Prototipo"
Cuando uno dice la palabra "prototype" o "prototipo" automáticamente inicia una confusión en la cabeza de los espectadores. El orador siempre tiene que ser preciso sobre lo que está hablando.

Significado del término "prototipo" y "prototype":
* El objeto "prototipo" de un objeto dado
  * Su padre
  * Accesible con la propiedad `algunObjeto.__proto__`, no con la propiedad `prototype`
  * La parte mas confusa es :
    * El padre del objeto `algunObjeto`, referenciado por la propiedad `algunObjeto.__proto__`, es comunmente llamado su **prototipo** o en inglés **prototype**
* El objeto "prototype" de una función dada, especialmente de una fución constructora
  * Accesible con la propiedad `AlgunConstructor.prototype`
  * Dicha propiedad debería llamarse el **prototype del constructor** o en inglés **constructor prototype**
* El objeto integrado `Function.prototype`
* El objeto integrado `Object.prototype`
* La propiedad prototype de cualquier otro constructor integrado, como por ejemplo `String.prototype`

Para abreviar :
* Solo una función puede tener la propiedad `prototype`
* Toda función tiene una propiedad `prototype`
* La propiedad `prototype` de una función mantiene una referencia a un objeto auxiliar, el cual es usado exclusivamente cuando la función es invocada como un constructor, es decir, con el operador `new`, y completamente ignorado por cualquier otra función regular
* Todo objeto tiene cadena de prototipo
* **La cadena de prototipos se construye usando la propiedad `__proto__`, no la propiedad `prototype`**
* Las funciones también son objetos, por lo tanto tienen la propiedad `__proto__` referenciando directamente al objeto integrado `Function.prototype`. Sus cadenas de prototipos son siempre `algunaFuncion -> Function.prototype -> Object.prototype`
* Todas las cadenas de prototipos terminan con `Object.prototype`
* `Object.prototype.__proto__` mantiene valor `null`. Este es el final real de la cadena de prototipos

## Funciones en JavaScript
Teniendo declaración de funciones simples podemos obtener la siguiente herencia.
```javascript
function foo(){}
```
![alt Function in Javascript](./images/foo.png "Function in Javascript")

Qué podemos ver :
* La declaración de la función crea dos objetos: el mismo objeto `foo` y `foo.prototype`, incluso si `foo` no va a ser utilizada como constructor
* `foo` hereda directamente de `Function.prototype`
* `foo.prototype` hereda de `Object.prototype`
* Esta herencia es válida para cualquier función, incluso anónimas o flecha

Lo que no vemos es que el mismo `foo` tiene una propiedad interna `[[Code]]`, la cual no puede ser accedida pero es usada cuando incovamos a `foo` como `foo()`.

Cuando usamos `foo.algunMetodo()`, todos los métodos integrados vienen de `Function.prototype` y mas debajo de la cadena, de `Object.prototype`. Pero `foo.algunMetodo()` nunca viene de `foo.prototype`. Ver [Static methods](https://github.com/rus0000/jsinheritance/blob/master/README.md#static-methods).

Usualmente `foo.prototype` no debería ser usado en absoluto si la funcion no es constructora y vice versa, es usado cuando la funcion se invoca como tal.

La propiedad `foo.prototype` puede ser asignada como referencia a cualquier otro objeto o valor primitivo. Asignarle un nuevo objeto o definir nuevas propiedades en `foo.prototype` es un patrón común para definir una "clase".

Las funciones flecha de ES6 no tienen la propiedad objeto `prototype` creada por defecto. No pueden ser usadas como constructores ya que mantienen un ámbito léxico para su contexto `this` entonces no pueden inicializar nuevas propiedades de instancia.

## Creando objetos simples con herencia
Los objetos simples en Javascript son creados con la sintaxis de `objeto literal` o con la función `Object.create`.
```javascript
// Objeto literal simple
var foo = {
  a: 10
};

// El objeto foo va a ser usado como el prototipo del objeto bar
var bar = Object.create(foo, {
    b: {
      value: 20
    }
  });

console.log(bar.__proto__ === foo); //true
console.log(bar.a); // 10 - Inherited property
console.log(bar.b); //20 - Own porperty
console.log(foo instanceof Object); // true, foo hereda de Object
console.log(bar instanceof Object); // true, bar hereda de Object
```

![alt Simple object creation](./images/simple-object.png "Simple object creation")

Lo importante a notar en este caso es que al momento de querer cambiar el valor de la propiedad `bar.a`, JavaScript automáticoamente crea una propiedad `bar.a` en el objeto mismo con este nuevo valor, de esta manera se evita la polución de prototipos.

Aunque las cadenas de prototipos de `foo` y `bar` se vean muy simples, podemos notar que ambas tienen una propiedad heredada llamada `constructor` la cual apunta a la función constructora `Object`, la cual hereda a su vez de `Function.prototype`. Extendiendo en esto, hay muchos métodos integrados en `Object.prototype` que no son mostrados para una mayor claridad. Todos ellos pueden ser accedidos desde `foo` y `bar`.

## Creando un objeto con una función constructora
Intentemos ahora declarar una función constructora simple y crear la instancia de un objeto usandola.
```javascript
function Bar() {
  // "this" va a apuntar al nuevo objeto que se va a crear
  this.a = 10;
}

Bar.prototype.readA = function () {
  // "this" va a apuntar al objeto del contexto en el cual el método sea invocado
  return this.a;
}

Bar.prototype.writeA = function (a) {
  this.a = a;
}

// La invocación de la función constructora requiere el operador "new"
var bar = new Bar();

console.log(bar.constructor === Bar); // true, "constructor" - propiedad heredada
console.log(bar instanceof Bar); // true
console.log(bar instanceof Object); // true, bar hereda de Object
console.log(bar.readA()); // 10 - Invocando el método hereado con "bar" como el objeto de su contexto
bar.writeA(20);
console.log(bar.readA()); // 20
console.log(bar.a); // 20 - El objeto "bar" leyendo su misma propiedad
```

![alt Object creation with constructor function](./images/bar.png "Object creation with constructor function")

`readA` y `writeA` son solo funciones regulares de Javascript con referencia similar a `Function` y `Object` tal como la misma función `Bar`. Estas referencias no son mostradas para una mayor claridad. La diferencia importante entre estas y la función `Bar` es que sus prototipos no tienen ningun uso.

El objeto `bar` tiene su propia propiedad `a` ya que la misma es creada cada vez que el constructor `Bar` es invocado. Este comportamiento nos permite producir diferentes objetos, cada uno con sus propias propiedades `a` pero heredando "métodos" de `Bar.prototype`.


## Métodos Estáticos
No existe tal cosa como `métodos estáticos` en la especificación de JavaScript en lo absoluto, pero este patrón de diseño puede ser facilmente implementado agegando propiedades en el objeto que es a su vez la misma `función constructora` antes que a su propiedad objeto `prototype`.

```javascript
function Bar() {
  this.a = 10;
}

Bar.staticMethod = function () {
  // no se puede usar "this" acá
  return "I am static";
}

Bar.prototype.readA = function () {
  return this.a;
}

Bar.prototype.writeA = function (a) {
  this.a = a;
}

var bar = new Bar();

console.log(bar.staticMethod); // undefined, el método no puede ser invocado desde la instancia
console.log(Bar.staticMethod()); // "I am static"
console.log(bar.constructor.staticMethod()); // "I am static", método disponible en la instancia a través de la propiedad heredada constructor
```

![alt JavaScript static method](./images/static.png "JavaScript static method")

Los métodos estáticos del constructor no son accesibles desde la instancia creada con este constructor, éstos estan disponibles en el construcor mismo.

Muchos patrones de diseño útiles en Javascript implementan métodos en la `función constructora` como objeto, por ejemplo las funciones `factory`. Tal `constructor` puede ser usado como `namespace` o `singleton`.

## Herencia clásica y OOP en Javascript
```javascript
// Constructor padre
function Duck (name) {
  this.name = name;
};

// Método padre
Duck.prototype.quack = function () {
  return this.name + " Duck: Quack-quack!";
};

// Constructor hijo
function TalkingDuck (name) {
  // Llamada al constructor padre con los parámetros apropiados
  Duck.call(this, name); // Ésto es muchas veces olvidado
}

// Herencia
TalkingDuck.prototype = Object.create(Duck.prototype);
TalkingDuck.prototype.constructor = TalkingDuck; // Esto es muchas veces olvidado

// Sobre carga de métodos
TalkingDuck.prototype.quack = function () {
  // Llamada al método padre
  return Duck.prototype.quack.call(this) + " My name is " + this.name;
};

// Instanciación de objetos
var donald = new TalkingDuck("Donald");
console.log(donald.quack()); // "Donald Duck: Quack-quack! My name is Donald"

console.log(donald.__proto__ === TalkingDuck.prototype); // true
console.log(TalkingDuck.prototype.__proto__ === Duck.prototype); // true, resultado de invocar Object.create()
console.log(Duck.prototype.__proto__ === Object.prototype); // true
console.log(donald.quack === TalkingDuck.prototype.quack); // true, método encontrado por la cadena de prototipos
console.log(donald instanceof TalkingDuck); // true
console.log(donald instanceof Duck); // true
console.log(donald.name); // "Donald", leyendo su misma propiedad
```

![alt JavaScript classical OOP](./images/oop.png "JavaScript classical OOP")

La cadena de prototipos de `donald` es `donald -> TalkingDuck.prototype -> Duck.prototype -> Object.prototype`. `Function.prototype` no toma parte en esta cadena ya que `donald` no es una función.

Cabe notar que la propiedad `name` es su propia propiedad a pesar de haberse creado con el constructor `Duck`. Esto es porque el constructor `Duck` es invocado con `Duck.call(this, name)`, donde `this` apunta al nuevo objeto creado dentro del constructor `TalkingDuck` y luego ha sido pasado por parámetro como el nuevo contexto del constructor `Duck`. Ver [MDN call() reference](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Function/call)

## Polución de prototipos
La polución de prototipos se da cuando se cambian propiedades de objetos que toman lugar en la cadena de prototipos, afectando asi todas las demas instancias. Usualmente estos objetos son del tipo `AlgunConstructor.prototype`. Asignarle valores a propiedades a estos objetos hace que las mismas se encuentren visibles para todas las instancias de `AlgunConstructor` y sus constructores descendientes resultando en comportamientos impredecibles o indeseados.

Otro caso es aquel con diseño incorrecto de la función constructora. La regla principal es no poner propiedades que mantengan valores en el objeto prototype del constructor sino inicializarlas dentro del cuerpo de la misma función constructora.  El prototype del constructor solo ebe contener métodos.

**Incorrecto!**
```javascript
function Collection(){}

// Popiedad compartida en todas las instancias - incorrecto
Collection.prototype.elements = [];

Collection.prototype.add = function (x) {
  // Cada instancia agrega valores al mismo array
  this.elements.push(x);
};
```

Debería ser

```javascript
function Collection(){
  // Cada instancia obtiene su propio array - correcto
  this.elements = [];
}

// Sin cambios de código pero el resutado es diferente
Collection.prototype.add = function (x) {
  this.elements.push(x);
};
```

El próximo caso de polución de prototipos se dá en cualquier tipo de modificación a la propiedad objeto prototype de los constructores integrados, por ejemplo `Object.prototype` ó `Function.prototype`. Muchas veces es realmente tengador y algunos frameworks lo hacen como na fucionalidad pero hoy en día está fuertemente considerato como un anti-patrón por la comunidad de Javascript.

## Funciones Fábrica
La función fábrica en Javascript no es una funcionalidad del lenguaje, es un patrón. Muchos patrones en Javascript estan disponibles solo por el potencial de su herencia basada en prototipos.

La función fábrica es entonces un patrón de diseño OOP de Javascript muy popular y poderoso. Hay miles de implementaciones del mismo en muchas librerias y frameworks.

El patrón de la función fábrica es simple, nos permite crear objetos y establecer `herencia por cadena de prototipos` pero sin el uso de una `función constructora`, sino usando una `función fábrica`. Las funciones fábrica pueden usar constructores por detrás de escena.

El método `http.createServer` de Node.js es un típico ejemplo de una función fábrica. Reorna una nueva instancia de la clase `http.Server`. El nuevo es creado sin usar la `función constructora` ni el operador `new`.

```javascript
// Carga del módulo http
var http = require('http');

// Llama a la función fábrica para construir la nueva instancia
var server = http.createServer(function (request, response) {
  response.writeHead(200, {"Content-Type": "text/plain"});
  response.end("Hello World\n");
});

// La variable server es una instancia de la clase http.Server
console.log(server instanceof http.Server); // true

// Llamada al método listen heredado de la clase http.Server
server.listen(8000);

console.log(server.hasOwnProperty('listen')); // false
```
El uso de una función fábrica puede resultar en un cadena de prototipos similar a la que obtenemos utilizando constructores. Pueden ser implementadas sin el uso de constructores, reemplazandolos con algún tipo de unción  `init`, pero aún así resutando en una cadena de objetos prototipo. En última instancia el operador `instanceof` no funcionará y el desarrollador tal vez se vea en la necesidad de usar la técnica de `Duck typing` para detectar el tipo del objeto.

## Mixins
Un Mixin es otro patrón poderoso y popular de Javascript. Es también una forma de herencia pero sin usar `cadena de herencia por prototipo`.

El principal objetivo de usar mixins es el de tener `herencia múltiple` y `aumento de performance`.

Un Mixin es una función que pide prestado comportamientos de uno o más objetos `donantes` y se los asigna al objeto `target`. Tecnicamente, consiste en copiar todos los métodos y propiedades de objetos donantes a objetos objetivos consiguiendo así un comportamiento similar.

La parte más importante acá es que los métodos copiados del donante no son copiados o clonados. Los objetos Function no son duplicados. Una función mixin solo copia refeencias a estos. Todas las instancias resultantes haen referencia al mismo conjunto de funciones.

Herencia múltiple es conseguida trás mexclar varios comportamientos de diversos objetos en uno solo.

El aumento de performance se consigue por el hecho de que las búsquedas a través de la cadena de prototipos son costosas y cuando todos los métodos de los donantes residen directamente en un objeto hijo, éste saltea la búsqueda y acelera la invocación del/los métodos.

Una función mixin mantiene intacta la propiedad `__proto__`del objeto target.

El operador `instanceof` no va a funcionar en este caso.

## Lectura
* [MDN Introduction to Object-Oriented JavaScript](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Introduction_to_Object-Oriented_JavaScript)
* [MDN Inheritance and the prototype chain](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Inheritance_and_the_prototype_chain)
* [MDN Function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)
* [MDN Object](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object)
* [Dr. Axel Rauschmayer blog](http://www.2ality.com/)
* [Juriy Zaytsev (kangax) blog](http://perfectionkills.com/)
* [Dmitry Soshnikov blog](http://dmitrysoshnikov.com/ecmascript/javascript-the-core/)
* [Addy Osmani "Learning JavaScript Design Patterns"](http://addyosmani.com/resources/essentialjsdesignpatterns/book/)
