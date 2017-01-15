# Funciones de primera clase: definiciones y argumentos

Una de las razones por las que las funciones y los conceptos funcionales son tan importantes en JavaScript es por que las funciones son las unidades primarias de ejecución. Execepto por la ejecución de JavaScript en el contexto global en la fase de construcción de nuestra página, todo el código que ejecutaremos y ejecutaremos en nuestra aplicación será mediante funciones.

Debido a que la mayoría de nuestro código será ejecutado como resultad de la invocación de una función, tener funciones versatiles y poderosas nos dan una gran flexibilidad y balance a la hora de escribir nuestro código.

## Funciones como objetos de primera clase

Las funciones en JavaScript poseen las mismas capacidades de un objeto y por este motivo son tratadas como cualquier otro objeto en el lenguaje. Decimos que las funciones son objetos de primera clase, por que pueden:

+ Ser creadas de forma literal

```javascript
function funcionLiteral() {}
```

+ Asignadas a variables, arrays, y propiedades de otros objetos.

```javascript
const funcion = function() {}; // Asignadas a una variable.
const arr = [];
arr.push(funcion); // Insertadas en un array.
let obj = {};
obj.funcion = function() {}; // Asignadas a la propiedad de un objeto
```

+ Pasadas como argumentos a otras funciones

```javascript
function funcion1(cb) {
    cb();
}

funcion1(function funcion2() {}); // Estamos pasando directamente una función como argumento de otra.
```

+ Retornadas como valor de otra función

```javascript
function funcion1() {
    return function() { // Retornamos una función de otra función.
        console.log('Funcion retornada desde funcion1');
    }
}

const function2 = funcion1(); // Asignamos la función retornada a una variable.
funcion2(); // Ejecutamos la función retornada almacenada en dicha variable.
```

+ Pueden poseer propiedades y dichas propiedades pueden ser creadas y asignadas dinamicamente

```javascript
let funcion1 = function() {};
funcion1.name = 'Funcion1';
```

Cualquier cosa que podamos hacer con objetos lo podemos hacer con funciones también. Las funciones son objetos con funcionalidades adicionales y con la capacida de ser invocadas, es decir, las funciones pueden ser llamads o invocadas para ejecutar una acción y los objetos no.

Una de las características más importantes de las funciones de primera clase es la de ser pasadas como argumentos a otras funciones. Ya que esto nos permite ejecutar las funciones pasadas como argumentos en algún punto durante la ejecución de la función principal. Este concepto es más conocido como funciones callback.

## Funciones callback

Siempre que pasemos una función como argumento a otra función para que esta luego sea ejecutada estamos creando una función callback. El termino callback viene del hecho de que estamos pasando una función que será llamada de nuevo (callback) en un punto especifico.

Las funciones callback son una parte fundamental para usar JavaScript de una forma efectiva, y lo más seguro es que ya las hayas usado en tu código aún y si no sabías sobre este concepto. Por ejemplo: al asignar un eventListener en un botón o al recibir datos de un servidor.

```javascript
function funcion(callback) {
    callback();
}
```

Esta función demuestra la habilidad de pasar una función como argumento de otra función y subsecuentemente invocar dicha función utilizando el nombre del parámetro.

```javascript
const text = 'Dentro de la función callback';

function function1(callback) {
    console.log('Dentro de la función');
    callback();
}

funcion1(function() {
    console.log(text);
});
```

Como podemos observar, declaramos una función que acepta otra función como argumento, luego la invocamos y le pasamos directamente otra función como parámetro. Veremos en pantalla el texto correspondiente.

Las funciones callback también pueden aceptar argumentos. Por ejemplo:

```javascript
function makeAjaxRequest(callback) {
    const ajaxResponse = 'He recibido la respuesta';

    callback(ajaxResponse);
}

makeAjaxRequest(function(data) {
    console.log(data);
});
```

La variable ajaxResponse simula los datos que obtuvimos de una llamada ajax, luego invocamos nuestra función callback con dicho texto y luego cuando Ejecutamos la función makeAjaxRequest le pasamos una función como parámetro y esta función recibe un argumento.

Otra característica importante de JavaScript es la habilidad de crear funciones en cualquier luegar de nuestro código, permitiendonos crear código más compacto. Esta característica también elimina la necesidad de tener que crear funciones antes de pasarlas como parámetros a otras funciones.

Un ejemplo de una función callback con el cual quizás estés familiarizado es el de asignarle una función callback a un event listener. Por ejemplo:

```js
document.addEventListener('mousemove', function() {
    window.alert('Mouse moved');
});
```

addEventListener es una función que acepta el nombre de un evento y una función callback, dicha función callback es invocada cada vez que movemos el mouse.

### Organizando un array utilizando una función callback

Lo más seguro es que alguna vez necesitemos organizar un array de datos. Gracias a JavaScript este proceso es una tarea sencilla ya que posee el método sort. Este método sort acepta una función callback que nos permite organizar el array

Por ejemplo:

```js
let arr = [5, 10, 2, 1, 3];

arr.sort(function(value1, value2) {
    return value1 - value2;
});
```

En este ejemplo hemos ejecutado el método sort en un array el cual acepta una función callback, este método cicla a través de cada elemento de nuestro array y ejecuta la función callback.

Lo importante aquí no es ver como funciona el método sort, si no que podemos utilizar callbacks en casí cualquier parte de nuestra aplicación, podemos crear funciones anónimas y pasarlas como argumentos a otra función, justo como cualquier otro valor en JavaScript.

# Funciones como objetos.

En esta sección examinaremos las maneras en las que podemos explotar las similaridades que comparten las funciones con cualquier otro tipo de dato. Una capacidad que puede resultar sorprendente es que no hay nada que nos detenga de agregar propiedades a nuestra funciones.

```js
let obj = {};
obj.nombre = "Jesus";
obj.saludar = function() {
    console.log(`Hola, mi nombre es ${this.nombre}`);
}
```

En el ejemplo anterior hemos visto como podemos crear un objeto y asignarle propiedades y métodos a este. Es posible hacer lo mismo con funciones.

## Almacenando funciones

En ciertos casos (por ejemplo cuando quremos manejar una colleción de callbacks que deberían ser invocadas una vez un evento ocurra), queremos almacenar una colleción de funciones únicas. Cuando agregamos funciones a dicha colección, una dificultad que podría surgir es determinar que funciones son nuevas a la colección y deberían ser agregadas, y cuales ya existen y no deberían ser agregadas. En general, cuando manejamos una colección de callbacks, no queremos duplicados, porque un solo evento podría resultar en multiples llamadas al mismo callback.

Una solución obvia podría ser agregar las funciones a un array y luego hacer un ciclo por el array para determinar si una función está duplicada o no. Desafortunadamente, hacer un ciclo por un array es muy costoso para nuestra aplicación, y queremos escribir código que funcione de forma efectiva y rápida.

```js
let store = {
    nextId: 1, // llevamos un registro del id que le asignaremos a cada función
    cache: {}, // creamos un objeto que servirá como cache y donde almacenaremos nuestras funciones
    add: function(fn) { // método para agregar nuevas funciones
        if (!fn.id) { // si la función que pasamos como parámetro no tiene la propiedad id
            fn.id = this.nextId++; // le asignamos dicha propiedades
            this.cache[fn.id] = fn; // creamos una propiedad con el id en el objeto cache y le asignamos dicha función.
            return true // retornamos true
        }
    }
}
```

En este listado, le hemos asignado un objeto a la variable store, en el cual almacenaremos un listado único de funciones. Este objeto tendrá dos propiedades de datos: una que almacena el siguiente id disponible, y otra llamada cache que almacena todas nuestras funciones. Las nuevas funciones son agregadas a la propiedad cache mediante el método add.

En el método add verificamos si la función ya ha sido agregada a la colleción verificando si tiene la propiedad id. Si la función actual tiene una propieda id, asumimos que la función ya existe en nuestra colección y no hacemos nada. Y si no, le asignamos una propiedad id (incrementando también la propiedad nextId de nuestro objeto store) y agregamos la función a la propiedad cache, usando el valor del id como nombre de la nueva propiedad. Retornamos true para verificar si la función ha sido o no agregada a nuestro objeto store.

## Funciones memorizadoras

Las funciones memorizadoras tienen la capacidad de recordar valores propios computados previamente. En pocas palabras, cada vez que una función memorizadora termina de ejecutar generando nuevos resultados, almacenamos los valores junto con los argumentos de la función. De esta manera, cuando otra llamada a dicha función ocurra, podemos verificar si ya hemos ejecutado la función con los mismos argumentos y podemos retornar el resultado almacenado sin necesidad de realizar ningún cálculo. Esto puede incrementar la velocidad de nuestra aplicación ya que podríamos evitar computaciones complejas que ya han sido ejecutadas previamente. Esto es particularmente útil cuando realizamos cálculos para datos que no cambian muy seguido, o cualquier cálculo matemático que consuma mucho tiempo.

Como ejemplo, veamos un simple algoritmo para calcular números primos.

```js
function esPrimo(valor) {
    // verificamos si la función esPrimo tiene una propiedad llamada respuestas y si no, la creamos.
    if (!esPrimo.respuestas) {
        esPrimo.respuestas = {};
    }

    // verifíca si existe la propiedad [valor] dentro del objeto respuestas de nuestra función
    // y si es así lo retornamos
    if (esPrimo.respuestas[valor] !== undefined) {
        return esPrimo.respuestas[valor];
    }

    // true si el valor es diferente a 1 (ya que 1 no es primo), falso de otra manera
    let primo = valor !== 1;

		// Este loop se ejecutará hasta que el valor de i sea menor 
    // a nuestro valor
    for (let i = 2; i < valor; i++) {
        // si valor % 2 es igual a 0, el número no es primo
        // si no se ignora y sigue siendo true.
        if (valor % i === 0) {
            primo = false;
            break;
        }
    }
		
    // retorna true o false si es primo o no.
    return esPrimo.respuestas[valor] = primo;
}
```

En la funcion esPrimo empezamos verificando si la función tiene una propiedad llamada respuestas (que usaremos para cachear nuestros resultados) y si no la tiene, la creamos el valor de un objeto vacío y dicha creación solo ocurrirá la primera vez que nuestra función se ejecute (ya que luego existirá y no hará falta crearla de nuevo). Luego verificamos que el resultado para el valor que hemos pasado no exista, y si existe lo retornamos y el resto de la función no se ejecuta. Le asignamos true a la variable primo si el valor es diferente a 1 y si no le asignamos false Entramos en el ciclo for el cual hará los ciclos necesarios hasta que el valor se divida entre sí (lo cual significa que el valor no es primo);

Este enfoque tiene dos grandes ventajas

+ El usuario final se beneficia de un aumento de rendimiento cuando necesita una respuesta de un valor que se ha computado anteriormente.
+ Todo sucede sin problemas y detrás de "escena"; ni el usuario final ni el autor de la página necesitan ejecutar algún tipo de pedido o hacer algún tipo de inicialización para ponerlo a funcionar.

Pero no todo es color rosa; este enfoque tiene grandes desventajas

+ Cualquier tipo de cacheo sacrificará memoria en favor de rendimiento.
+ Hay gente que piensa que el cacheo no debería formar parte de la lógica de negocios de una aplicación; una función debería hacer solo una cosa y hacerla bien.
+ Es dificíl testear o medir el rendimiento de un algoritmo como este, por que los resultados dependen de las llamadas anteriores a dicha función.

# Definiendo funciones

En JavaScript usualmente se definen las funciones utilizando una funcion literal que crea una función de la misma manera que un literal numérico crea un número. Recordemos que, como objetos de primera clase, las funciones son valores que pueden ser usadas como cualquier otro valor, tales como strings o números. Y así te hayas dado cuenta o no, lo has estado haciendo todo este tiempo.
JavaScript provee algunas formas de definir funciones, las cuales pueden ser divididas en 4 grupos:

+ Declaraciones de función y expresiones de función: Las dos formas más comunes, y aún así diferentes, formas de definir una función. Muchas veces la gente no las considera diferentes, pero como veremos, estar consciente de sus diferencias nos ayudará a entender cuando una función está disponible para ser invocada.
+ Funciones flecha: Este forma ha sido implementada recientemente al lenguaje y nos permite definir funciones de una manera más compacta y nos ayuda muchas veces con algunos problema que se presentan en las funciones callback.
+ Funciones constructoras: Una forma menos común que nos permite construir una nueva función a partir de un string.
+ Funciones generadoras: También ha sido implementada recientemente al lenguaje y nos permite crear funciones que, a diferencia de funciones normales, podemos salir y entrar a ellas y al mismo tiempo mantener los valores a través de esas entradas y salidas.

Es importante que entendamos estas diferencias, por que la manera en la que una función es definida puede influenciar significantemente cuando una función está disponible para su ejecución, como se comporta y en que objeto esa función puede ser invocada.

## Declaraciones de función y expresiones de función

Las dos maneras más comunes de definir funciones en JavaScript. Esas dos técnicas son tan similares que muchas personas no saben distinguirlas y desconocen sus diferencias.

### Declaraciones de función

La forma más básica de declarar una función en JavaScript es usando una declaración de función. Todas las declaraciones de función empiezan con la palabra function () y dentro de los paréntesis definimos los parámetros (separados por coma ",") que queremos que acepte nuestra funcion. El cuerpo de la función debe estar encapsulado en { /* código aquí */ }. Adicionalmente, una declaración de una funcion debe estar creada como una declaración separada, pero puede ser contenida por un bloque de codigo, o como parámetro de una función.

Ejemplos:

```js
function declaracion1() {
    /* código aquí */
}

function declaracion2() {
    /* código aquí */

    function declaracionAnidada() {
        /* código aquí */
    }
}
```

### Expresión de una función

Como ya hemos mencionado numerosas veces, las funciones en JavaScript son objetos de primera clase, esto significa, entre otras cosas, que pueden ser asignadas a variables. Las funciones que siempre son parte de una declaración, son llamadas expresiones de función. Las expresiones de función son geniales por que nos permiten definir funciones exactamente donde las necesitamos, lo que hace que nuestro código sea más fácil de entender.

Ejemplos:

```js
const expresion1 = function() {
    /* código aquí */
}

const expresion2 = function(callback) {
    /* código aquí */
};

expresion2(function expresion3() {
    /* código aquí */
    return function expresion4() {
        /* código aquí */
    };
});
```

Aquí podemos observar que las declaraciones de función están en declaraciones diferentes (pero pueden estar dentro de otra función) y las expresiones de una función siempre son partes de una declaración (return, dentro de un argumento de una función, como valor de una variable, etc).
Además de la posición en la que se encuentran en nuestro código, hay otra diferencia entre declaraciones y expresiones de función. Las declaraciones de función necesitan un nombre obligatoriamente, mientras que en las expresiones es opcional.
Las declaraciones de función deben tener un nombre por que se encuentran "por si solas", y debemos tener una manera de hacer referencia a estas para poder invocarlas.
Las expresiones de función son parte de otras declaraciones de JavaScript, así que tenemos otras maneras de hacer referencia a ellas para luego invocarlas.

### Funciones inmediatas

JavaScript nos permite invocar inmediatamente una función de la siguiente manera:

```js
(function() {
    console.log('Función invocada inmediatamente');
})();
```

Los parentesis alrededor de nuestra función le dicen al interpretador de JavaScript que todo lo que está adentro es una expresión de una función y los () al final la invocan inmediatamente.

## Funciones flecha

Debido a que en JavaScript se utilizan mucho las funciones, tiene sentido que hayan agregado una forma más sencilla de definir funciones y así hacer nuestras vidas un poco más simples.
En muchas formas, las funciones flechas son una forma más simple de expresiones de función.

Para construir una función flecha debemos tener ```parametros => expresión```
Esta función flecha tiene un parametro y retorna el valor de una expresión. Podemos usar esta sintaxis de la siguiente manera:

```js
const saludar = nombre => 'Hello ' + nombre; // definimos una expresión de una función utilizando una función flecha.
saludar('Jesus');
```

Esta es la forma más sencilla de utilizar una función flecha, pero en general, la función flecha puede ser definida de diferentes maneras.
Como podemos ver, una función flecha empieza con una lista opcional de parámetros. Si no hay parámetros, o más de un parámetro, este/os deben ser encapsulado/s en paréntesis. Pero si tenemos solo un parámetro, podemos omitir los paréntesis. Luego de los parámetros viene la flecha "=>" obligatoria, que le dice al interprete de JavaScript que estamos utilizando una función flecha.
Después de la flecha "=>", tenemos 2 opciones. Si solo queremos retornar un valor simple, ponemos una expresión directamente {} y dicha expresión será retornada directamente. Si no, debemos utilizar {} y poner nuestro código dentro de estos.

Por ejemplo:

```js
/* funciones flecha sencillas con retorno directo */
/* retorno directo, es decir, en una misma linea y sin {} y sin especificar que queremos retornar (ya que retorna la expresión inmediatamente) */
const saludarSinParametros = () => 'Hello!'; // ningún parámetro, los paréntesis son obligatorios
const saludarConUnParametro = nombre => 'Hello ' + nombre; // un solo parámetro, los paréntesis son opcionales
const saludarConParametros = (saludo, nombre) => saludo + ' ' + nombre; // más de un parámetro, los paréntesis son obligatorios

/* funciones flechas más complejas sin retorno directo */
/* retorno no directo, es decir, cuando nuestra función es un poco más compleja y debemos colocar nuestro código dentro de un bloque de código {}, tenemos que especificar que queremos retornar */
const saludarCompleja = nombre => {
    if (nombre) {
        return 'Hello ' + nombre;
    }

    return 'Hello stranger!';
};
```

*Nota: en todos las funciones, si no tienen ningún valor de retorno, esta retornará "undefined" por defecto.*

# Argumentos y parámetros de función

Cuando hablamos sobre funciones, regularmente utilizamos términos como parámetros y argumentos casi de manera intercambiable, como si fuesen casi la misma cosa.

+ Un parámetro es una variable que listamos como parte de la definición de una función.
+ Un argumento es un valor que pasamos a una función cuando la invocamos.

Como podemos ver, los parámetros de una función son especificado con la definición de la función, y todo tipo de funciones pueden tener parámetros.
Mientras que los argumentos están conectados a la invocación de una función; son valores que pasamos a una función a la hora de invocarla.

Cuando una lista de argumentos es suministrada como parte de la invocación de una función, esos argumentos son asignados en el orden especificado a los parámetros de la definición de la función. El primer argumento se le asigna al primer parámetro, el segundo argumento al segundo parámetro y así sucesivamente.
Si el número de argumentos es diferente al número de parámetros, no sucederá ningún error. Los argumentos de más simplemente no son asignados a ningún parámetro y son ignorados. Sin embargo, si tenemos más parámetros que argumentos, los parámetros que no tengan argumentos correspondientes tendrán un valor de undefined.

## Parametros Rest

Los parámetros Rest son una adición reciente al lenguaje (ES6).
Los parámetros Rest transforman una lista de argumentos en un array. Se definen utilizando "..." y el nombre del parámetro.

Por ejemplo:

```js
// gracias a ... el parámetro tabla se convierte en un array que contiene todos los argumentos
function multiplicar(numero, ...tabla) {
    /* ahora el parámetro tabla es un array que contiene todos los argumentos ubicados después del primer número */
    return tabla.map(elemento => numero * elemento);
}
multiplicar(5, 1,2,3,4,5,6,7,8,9,10); // después del 5, todos esos argumentos se convertirán en un array con el nombre del parámetro tabla
```

*Nota: Solo el último parámetro de una función puede ser un parámetro Rest.*

## Parámetros por defecto

Los parámetros por defecto también son una adición reciente al lenguaje (ES6).
Los parámetros por defecto nos permiten asignarlen cualquier tipo de valor por defecto (string, número, objeto, función, array, etc) a uno o más parámetros a la hora de definir una, dichos valores serán sobreescritos si un argumento es proporcionado. Para asignar un valor por defecto a algún parámetro debemos utilizar la siguiente sintáxis:

```js
function valoresPorDefecto(nombre, saludo="Hola") {
    console.log(saludo + ' ' + nombre);
}
valoresPorDefecto(, nombre);
```

La asignación de valores por defecto a parámetros de nuestras funciones no hacen que nuestro código sea mas legible ni más rápido, por este motivo es recomendable que se eviten cuando sea posible (aunque algunas veces es necesario y no puede evitarse).

# En resumen

+ Las funciones son objetos de primera clase y son tratadas como cualquier otro objeto en JavaScript. Similar a otros objetos, las funciones pueden ser:
    + Creadas vía literales.
    + Asignadas a variables o propiedades de un objeto.
    + Pasadas como parámetros de otra función.
    + Como valor de retorno de la ejecución de otra función.
    + Asignada a propiedades y métodos de otros objetos.
+ Las funciones callback son funciones que otras funciones llamarán luego, y son muy utilizadas, especialmente en código asíncrono.
+ Podemos aprovechar el hecho de que a las funciones se le pueden asignar propiedades y que dichas propiedades pueden ser utilizadas para almacenar información.
    + Podemos almacenar funciones en propiedades para referenciarlas luego.
    + podemos utilizar las propiedades de una función para crear una cache y evitar computaciones innecesarias.
+ Hay varios tipos de funciones: declaraciones de función, expresiones de función, funciones flecha, generadores, funciones constructoras, funciones inmediatas, etc.
+ Declaraciones de función y expresiones de función son los tipos más comunes de función. Las declaraciones de función deben tener un nombre y deben ser separadadas de otras partes de nuestro código. Las expresiones de función no necesariamente deben tener nombre y pueden ser parte de otras piezas de nuestro código.
+ Las funciones flecha son una nueva adición al lenguaje y nos permiten definir funciones de forma más sencilla.
+ Un parámetro es una variable que listamos a la hora de definir una función, mientras que un argumento es un valor que le pasamos a una función a la hora de invocarla.
+ La lista de parámetros y argumentos de una función puede ser de diferente largo.
    + Los parámetros que no se han asignado tendrán como valor undefined.
    + Los argumentos extra no serán asignados a ningún parámetro y serán ignorados.
+ Los parámetros Rest y parámetros por defecto son una nueva adición al lenguaje:
    + Los parámetros Rest nos permiten convertir una lista de argumentos en un array.
    + Los parámetros por defecto nos permiten especificar un valor por defecto de un parámetro.
