# Apuntes de SQL :notebook:

## **Historia y recursos**

- [Qué es una BBDD y SQL](https://www.youtube.com/watch?v=FR4QIeZaPeM "DDBB & SQL")
- [Historia de SQL](https://es.wikipedia.org/wiki/SQL "Historia de SQL en Wikipedia")
- [Ejemplos de los apuntes](https://sqlzoo.net/ "Ejemplos en SQLZoo")

## **Sublenguajes de SQL**

SQL es un único lenguaje compuesto de sublenguajes que pueden realizar por separado o conjuntamente con otros sublenguajes distintas operaciones que el usuario le pida.

Estos sublenguajes son:

- **`DQL (Data Query Language)`**: Este sublenguaje se encarga de las consultas de los usuarios (obtener las columnas -atributos- de una tabla, obtener tuplas con información de una columna o la tabla al completo...).
  - **`SELECT`**
- **`DML (Data Manipulation Language)`**: Este sublenguaje se encarga de manipular los datos de la base de datos. Permite, por ejemplo, insertar nuevos datos en una columna.
  - **`INSERT, UPDATE, DELETE` -> En versiones anteriores de SQL incluía `SELECT`**
- **`DDL (Data Definition Language)`**: Este sublenguaje nos permite definir las características u objetos de la base de datos. Podemos, por ejemplo, crear o eliminar tablas.
  - **`CREATE, ALTER, DROP, TRUNCATE, RENAME`**
- **`DCL (Data Control Language)`**: Permite definir controles de seguridad sobre datos u objetos de la base de datos. Con este sublenguaje se puede dar o revocar permisos a los usuarios para que puedan o no acceder a ciertos datos o realizar alguna operación.
  - **`GRANT, REVOKE`**
- **`TCL (Transaction Control Language)`**: Nos permite hacer transacciones (conjunto de operaciones sobre datos u objetos).
  - **`COMMIT, ROLLBACK, SAVEPOINT`**
- **`SCL (Session Control Language)`**: Permite manejar de manera dinámica la sesión de un usuario. La sesión de un usuario se entiende como la representación lógica de una conexión entre la base de datos y un usuario. Con este lenguaje se puede modificar condiciones o parámetros de una sesión activa.
  - **`ALTER SESSION, SET ROLL`**

## DQL (Data Query Language)

El sublenguaje DQL nos permite obtener datos de una base de datos mediante consultas a ésta. Podemos utilizarlo para saber, por ejemplo, los nombres de los países de una supuesta tabla llamada 'world', que contiene todos los países del mundo.

Las consultas que podemos hacer consisten en una combinación de su principal función, **`SELECT`**, y la función **`FROM`**, con la que indicamos desde qué tabla queremos sacar los datos que queremos y que previamente hemos indicado en el **`SELECT`**.

Sólo con estas dos funciones podemos obtener infinidad de datos de un BBDD, aunque al final se quedan cortas para los numerosos casos en los que queremos consultar, por ejemplo, datos que cumplan un cierto criterio. Para éstos casos podemos combinar las funciones anteriores con la función **`WHERE`**, que nos permite indicar una condición (o varias) que deben cumplir los datos que queremos, y ésto se lo indicamos en forma de predicados.

Supongamos la siguiente tabla de una BBDD de ejemplo tomada de [SQLZoo](https://sqlzoo.net/ "SQLZoo"):

![tabla_world](img/tabla-world.PNG)

La tabla está compuesta de cinco columnas **(name, continent, area, population y gdp)** y contiene tuplas de información relacionada con la semántica de ésas columnas. Si quisiéramos saber, por ejemplo, todos los países de África, lo primero que haríamos sería seleccionar los datos que queremos (en este caso **name**), luego de dónde lo queremos (estamos trabajando con la tabla '**world**' así que la elegimos) y finalmente le decimos que queremos los países que estén en el continente africano (este es el predicado). La consulta quedaría así:

```SQL
SELECT name
FROM world
WHERE continent = 'Africa'
ORDER BY name;
/*Utilizamos el comparador '=' para comprobar que 'continent' es exactamente igual a 'Africa' y
finalmente ordenamos la lista por orden alfabético (por defecto con el valor ASC pero
podemos especificar que sea DESC, así los ordenará por orden inverso: ORDER BY name DESC).
*/
```

- Las consultas SQL siempre acaban en **;**.
- Las cláusulas y funciones de SQL se pueden escibir en minúsculas o mayúsculas. Esto puede variar en el caso de los nombres de las tablas o columnas dependiendo
del sistema gestor.
- Para poner comentarios de una línea utilizamos doble guión: ```--COMENTARIO``` y para los de varias líneas los encerramos en barra y asterisco:

```plaintext
/*
COMENTARIO 1
COMENTARIO 2
COMENTARIO 3
...
*/
```

---

- **Operador asterico (*):** es un comodín que significa todo el contenido.

```SQL
SELECT *
FROM world;
```

---

Si queremos, por ejemplo, saber a qué continente pertenece Australia y sabemos que está en una lista dada de continentes utilizamos la cláusula **`IN`**:

```SQL
SELECT name, continent AS c
FROM world
WHERE name = 'Australia' AND c IN ('Africa', 'Oceania', 'Asia');
```

- Podemos selccionar varias columnas de una tabla simplemente separando sus nombres por comas.
- Para que el código sea más legible, podemos renombrar las columnas para nuestra consulta con **`AS`**.
- Para concatenar predicados utilizamos la cláusula **`AND`**.

```SQL
SELECT name, population
FROM world
WHERE name in ('Sweden', 'Norway', 'Denmark', 'Russia')
ORDER BY population ASC;
```

Otra cláusula que podemos utilizar para enfocar aún más nuestra consulta es **`BETWEEN`**. Con esta cláusula podemos indicar que queremos valores comprendidos en un rango en concreto.
Por ejemplo, si quisiéramos saber qué países del continente asiático tienen una población total de entre 25 y 50 millones ejecutamos la siguiente consulta:

```SQL
SELECT upper(name), population
FROM world
WHERE continent = 'Asia' AND population BETWEEN 25000000 AND 50000000;

/*
Con la función 'upper' le indicamos que queremos el nombre de los países en mayúsculas. También disponemos de la función lower(),
que hace lo contrario.
*/
```

```SQL
SELECT name, population
FROM world
WHERE population BETWEEN 50000000 AND 100000000
ORDER BY population;
```

```SQL
SELECT name AS Nombre, area
FROM world
WHERE name BETWEEN 'D' AND 'F';

--WHERE name >= 'D' AND <= 'F';
```

Otra cláusula es **`ROUND`**, que nos permite redondear valores numéricos:

```SQL
SELECT name, ROUND(gdp, -6)
FROM world
WHERE continent = 'Asia';
/*
Como primer argumento se incluye el nombre de la columna que contiene los datos que queremos y como segundo argumento incluimos
la cantidad de números que queremos redondear. Si utilizamos valores positivos redondearemos los decimales; con valores negativos
redondeamos la parte entera (se convierten en 0 los números que estén en ese rango que elegimos)
*/
```

Si queremos saber la longitud de una cadena podemos emplear la cláusula **`LENGTH`**:

```SQL
SELECT LENGTH(name), name
FROM world;
```

```SQL
SELECT name, capital
FROM world
WHERE LENGTH(name) = LENGTH(capital);

-- Devuelve los países cuyo nombre tiene la misma longitud que el nombre de su capital.
```

Podemos utilizar la cláusula **`LEFT`** para aislar caracteres de una cadena empezando desde la izquierda:

```SQL
SELECT name, LEFT(name, 1)
FROM world
-- El segundo argumento indica el número de caracteres que queremos aislar
```

Si queremos obtener resultados que son la unión de dos o más datos en una tabla, disponemos de la cláusula **`CONCAT`**:

```SQL
SELECT name
FROM world
WHERE capital = CONCAT(name, ' City');

-- Obtenemos los países cuya capital sea el nombre del propio país más la cadena 'City'
```

Con la cláusula **`LIKE`** podemos emplear expresiones regulares:

```SQL
SELECT name
FROM world
WHERE name LIKE 'Sw_%' LIMIT 1;

/*
Primero indicamos que el nombre del país tiene que empezar por una S seguida de la letra w, después -obligatoriamente- tiene
que haber un caracter más (_) y después de éste puede haber 0, 3 o infinitos caracteres (%). Finalmente indicamos con la cláusula
LIMIT, que sólo queremos el primer valor que concuerde con las condiciones que establecimos. Esta consulta nos devuelve como resultado:

- Swaziland

Si no hubiésemos utilizado LIMIT nos habría devuelto:

- Swaziland
- Sweden
- Switzerland


  -> %: 0 o más
  -> _: 1 solo caracter
*/
```

```SQL
SELECT name FROM world
WHERE name LIKE 'Y%';

/*
  -> Y% Empieza por Y...
  -> %y Termina por y
  -> %y% Contiene y

  * En el patrón de búsqueda se diferencia entre minúsculas y mayúsculas.
*/
```

---

### Operadores de comparación

En SQL disponemos de los operadores de comparación comunes a otros lenguajes:

**=** | Comprueba si un valor es exáctamente igual a otro.
--- | ---
**<> - !=** | Comprueba si un valor es distinto a otro. (El operador <> es el incluido en el estándar SQL-92, por lo que es más recomendable utilizarlo en lugar de !=)
**>** | Comprueba si un valor es mayor que otro.
**<** | Comprueba si un valor es menor que otro.
**>=** | Comprueba si un valor es mayor e igual que otro.
**<=** | Comprueba si un valor es menor e igual que otro.

### Operadores lógicos

**AND** | Comprueba si dos condiciones se cumplen.
--- | ---
**OR** | Comprueba si al menos una de dos condiciones se cumple.
**NOT** | Comprueba si de dos condiciones una de ellas no es cierta.
**XOR** | Comprueba si de dos condiciones una se cumple y otra no.

**Ejemplos:**

```SQL
SELECT name, gdp/population
FROM world
WHERE population >= 200000000;
```

```SQL
SELECT name, ROUND(gdp/population, -3)
FROM world
WHERE gdp >= 1000000000000;
```

```SQL
SELECT name, population, area
FROM world
WHERE area >= 3000000 OR population >= 250000000;
```

```SQL
SELECT name, population, area
FROM world
WHERE area >= 3000000 XOR population >= 250000000;
```

```SQL
SELECT name, capital
FROM world
WHERE LEFT(name, 1) = LEFT(capital, 1) XOR name = capital;

/*
Devuelve los países junto con sus capitales donde la primera letra del país es igual a la
primera letra de su capital o el país sea igual a la capital, pero no devuelve resultado
cuando se cumple la condición lógica.
*/
```

```SQL
SELECT name, population
FROM world

--WHERE name IN ('France', 'Germany', 'Italy')

WHERE name = 'France' OR name = 'Germany' OR name = 'Italy';

--Una manera de reemplazar el IN.
```

```SQL
SELECT name, capital
FROM world
WHERE LEFT(name, 1) = LEFT(capital, 1) AND name <> capital;

/*Países donde la primera letra de su nombre sea igual a la primera de su capital, pero sólo cuando el nombre
del país no sea igual al de la captial.
*/
```

```SQL
SELECT name
FROM world
WHERE name LIKE '%a%' AND name LIKE '%e%' AND name LIKE '%i%' AND name LIKE '%o%' AND name LIKE '%u%' AND name NOT LIKE '% %';

--Países que contengan todas las vocales.
```

---

```SQL
SELECT name, population
FROM world
WHERE name >= 'Germany'
LIMIT 5;

/*
Obtenemos los cinco primeros países por orden alfabético teniendo en cuenta la cadena que le damos, en este caso 'Germany'.

Como hemos elegido el operador >=, el primer resultado que tenemos es la propia cadena 'Germany'. A continuación,
la consulta nos devuelve 'Ghana', ya que después de la letra G, la letra más pequeña en términos de orden es la
h.

Como tercer resultado tenemos 'Greece', por las mismas razones de arriba, y así hasta 'Guatemala'.
*/
```

Podemos unir las expresiones regulares con las concatenaciones para especificar aún más cómo queremos que sea el resultado:

```SQL
SELECT capital, name
FROM world
WHERE capital LIKE concat('%', name, '%');

/*Obtenemos las capitales del mundo junto con su país, siempre y cuando la capital sea una combinación
de 0 o más caracteres más el nombre del país, seguido de 0 o más caracteres
*/
```

```SQL
SELECT capital, name
FROM world
WHERE capital LIKE concat(name, '_%');
```

---

**Función `REPLACE`: con esta función podemos buscar un patrón dentro de una cadena y reemplazarlo por otra cadena**

```SQL
SELECT name, REPLACE(capital, name, '') AS ext
FROM world
WHERE capital LIKE concat(name, '_%');

/*
Obtenemos los países junto con su capital y en el caso de que la capital sea una combinación de cualquier
cadena de texto más el nombre de su país, reemplazamos el nombre del país por una cadena vacía, obteniendo
así sólo una especie de extensión del país.
*/
```

---

### Subconsultas

Las subconsultas son consultas embebidas utilizadas para obtener datos necesarios para realizar la consulta principal. Tienen un ámbito de ejecución
distinto al de la principal, esto es, los datos de una tabla que utilizamos en una subsonsulta son una instancia aparte de la tabla, por lo que és como
si copiáramos la tabla solo para realizar la subconsulta, así no se interfiere con los datos utilizados por la principal.

Ejemplo:

```SQL
SELECT outerWorld.name, outerWorld.gdp/population
FROM world AS outerWorld
WHERE outerWorld.continent = 'Europe' AND outerWorld.gdp/population > (SELECT innerWorld.gdp/population
                                                                      FROM world AS innerWorld
                                                                      WHERE innerWorld.name = 'United Kingdom');

/*
Obtenemos el nombre y el PIB per capita de los países de Europa y cuyo PIB per capita es mayor
que el de Reino Unido.

En la consulta principal no hay manera de obtener el PIB per capita del Reino Unido en concreto, por lo
que empleamos la subconsulta para obtener este dato y luego utilizarlo para la principal.
*/
```

- Las subconsultas siempre van a la derecha del operador.
- Aunque el ámbito de ejecución es distinto, para que el código sea más comprensible renombramos las tablas.

```SQL
SELECT name, population
FROM world
WHERE population > (SELECT population FROM world WHERE name = 'Canada') AND
population < (SELECT population FROM world WHERE name = 'Poland');

--Países cuya población sea mayor que la de Canadá y menor que la de Polonia.
```

```SQL
SELECT name,
      CONCAT(ROUND((population*100) / (SELECT population FROM world WHERE name = 'Germany'), 0), '%') AS poblacion

FROM world
WHERE continent = 'Europe';

--Muestra los países de Europa junto con su población como porcentaje de la población de Alemania.
```

Para detallar aún más nuestra subconsulta disponemos de la cláusula **`ALL`**, que nos permite comprobar
de una sentada los valores de la subconsulta que cumplen una condición.

```SQL
SELECT name
FROM world
WHERE population >= ALL(SELECT population
                        FROM world
                        WHERE population > 0);

/*

Devuelve el país con mayor población del mundo.

Al utilizar la cláusula ALL los valores que devuelve la subconsulta se mantienen juntos mientras
la consulta principal se va ejecutando y comprobando que la condición se cumple. Cada vez que la
consulta principal encuentra un país cuya población es mayor o igual (no puede haber un país
que tenga un población mayor que la suya propia) a la de la subconsulta, almacena el resultado hasta
que aparezca otro país tenga una población mayor que la del almacenado.

Si no hubiésemos utilizado la cláusula ALL, la consulta nos habría dado un error porque estaríamos trantando
de comparar la población de un solo país con la población de la lista de cada país de la subconsulta a la vez.
*/
```

```SQL
SELECT name
FROM world
WHERE gdp > ALL(SELECT gdp
                FROM world
                WHERE continent = 'Europe' AND gdp IS NOT NULL); -- No funcionaria gdp <> NULL

--Devuelve los países cuyo GDP es mayor al de los países de Europa
```

```SQL
SELECT outerWorld.continent, outerWorld.name, outerWorld.area
FROM world AS outerWorld
WHERE outerWorld.area >= ALL
    (SELECT area
     FROM world AS innerWorld
     WHERE innerWorld.continent = outerWorld.continent
     AND innerWorld.area > 0);

--Devuelve el país de cada continente que tiene el área más grande.
```

```SQL
SELECT outerWorld.continent, outerWorld.name
FROM world AS outerWorld
WHERE outerWorld.name <= ALL
                        (SELECT innerWorld.name
                        FROM world AS innerWorld
                        WHERE innerWorld.continent = outerWorld.continent
                        AND innerWorld.name IS NOT NULL);

--Lista cada continente y el nombre de su país que viene primero por orden alfabético.
```

```SQL
SELECT outerWorld.name, outerWorld.continent
FROM world AS outerWorld
WHERE outerWorld.population > ALL
                            (SELECT innerWorld.population*3
                            FROM world AS innerWorld
                            WHERE innerWorld.continent = outerWorld.continent AND outerWorld.name <> innerWorld.name
                            AND population IS NOT NULL);

--Devuelve los países de cada continente que tienen una población 3 veces mayor que cualquier otro del propio continente.
```

---

### JOIN, INNER JOIN, LEFT JOIN y RIGHT JOIN

