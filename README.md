# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. 

Función: `get_data(depth, crpt)`

* `crpt`: `str`. Intercambio de divisas del cual se obtiene el Order Book. Para el desarrollo del TFM se han utilizado los valores: BTCBUSD (bitcoin - United States Dollars Tether) y ETHBUSD (ethereum -  United States Dollars Tether). Las opciones requeridas deben ser introducidas en la lista sobre la que itera el bucle que contiene la función `get_data()`. Más posibles intercambios de divisas disponibles en: https://www.binance.com/es/trade/BTC_BUSD?layout=pro&type=spot

* `depth`: `int`. Número de ofertas requeridas a cada lado del Order Book. Para el desarrollo del TFM se han empleado 5.000 por lado.

Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece en 1 minuto.

El resultado de una iteración en este algoritmo es un archivo tipo `feather` con 10.000 registros (5.000 órdenes de compra y 5.000 órdenes de venta) con las variables: `date`, `price`, `quantity`, `side` y `crpt`. 

## Fichero 2: Lectura y tratamiento de base de datos.ipynb

Fichero para la creación de las estructuras de datos.

Función principal: `createMatrix(tipo, BTCOB, cubes)`

* `tipo`: `int`. Puede tomar el valor `1` para la creación de la estructura de datos del plantemianto 1 o `2` para la estructura del planteamiento 2.
* `BTCOB`: `list`. Contiene los archivos tipo `feather` generados con el Fichero 1.
* `cubes`: `list`. Valores con los tamaños de las amplitudes del Order Book seleccionadas para la creación de las variables. La lista debe contener 30 elementos exactamente.

Esta función genera las estructuras de datos en base al `tipo` seleccionado. Las siguientes funciones son comunes para ambos tipos de planteamientos:

* `preProcesado(df)`: toma un archivo tipo `feather` convertido en `pd.DataFrame` y transforma los datos a los formatos requeridos. Devuelve 3 elementos: (1) una base de datos con las ofertas del lado de los asks (`dfAsks`), (2) una base de datos con las ofertas del lado de los bids (`dfBids`) y (3) el mid-price (`midprice`).
* `addDate(df)`: guarda la fecha (`date`) y la variable `minBeijing`.

Por otro lado, `createMatrix(tipo, BTCOB, cubes)` también categoriza la variable `midprice` para que determine sus cambios de tendencia a 5 minutos. De esta forma, tomará el valor 1 si la tendencia es constante o ascendente y 0 si es descendente. 

### Planteamiento 1

La función generadora de la estructura de datos para el planteamiento 1 es `Tipo1(Dn, midpriceArray, y)` donde:

* `Dn`: array con todos los registros de la variable `date`.
* `midpriceArray`: array con todos los registros de la variable `midprice`. 
* `y`: array con la variable `midprice` categorizada.

La función `Tipo1(dateArray, midpriceArray, y)` crea registros a partir de la variable `midpriceArray` en el formato (t, t-1, t-2), otorgándole de esta forma memoria al modelo. Finalmente, se añade la variable `y`.

### Planteamiento 2

Para la creación de los datos del planteamiento 2 se introduce una nueva función: `addOrderBook(dfBids, dfAsks, cubes, midprice, crp)` donde:

* `dfBids`: `pd.DataFrame` con los bids creado en `preProcesado(df)`.
* `dfAsks`: `pd.DataFrame` con los asks creado en `preProcesado(df)`.
* `cubes`: tamaño de las amplitudes del Order Book introducidas por el usuario en `createMatrix(tipo, BTCOB, cubes)`.
* `midprice`: midprice correspondiente a la instantánea del Order Book almacenada.
* `crp`: criptomoneda. Para el planteamiento 2, toma el valor `btc`.

La función `addOrderBook(dfBids, dfAsks, cubes, midprice, crp)` contiene las siguientes funciones:

* `CreateBids(dfBids, cubes, midprice)`: crea las variables del grupo II a partir de los `cubes` introducidos por el usuario.
* `CreateAsks(dfAsks, cubes, midprice)`: crea las variables del grupo III a partir de los `cubes` introducidos por el usuario.
* `WeightedFeatures(dfSide, side)`: crea las variables del grupo IV. Puede tomar los valores `dfSide = dfBids` y `side = "bid"` o `dfSide = dfAsks` y `side = "ask"`. Solo se ejecuta cuando `crp = "btc"`.
* `CreateSlope(offers, cubes, midprice)`: crea las variables del grupo V. El argumento `offers` debe contener el objeto devuelto por `CreateBids(dfBids, cubes, midprice)` o por `CreateAsks(dfAsks, cubes, midprice)` seleccionando tan solo 15 de sus filas. De la misma forma, para el argumento `cubes` tan solo deben introducirse 15 elementos. 
* `DistrFeatures(dfSide, side)`: crea las variables del grupo VI. Solo se ejecuta cuando `crp = "btc"`.

Finalmente, se concatenan todos los valores obtenidos de las funciones anteriores y se devuelve la lista resultante como resultado de la función.

La función generadora de la estructura de datos para el planteamiento 2 es `Tipo2(Dn, Bn, y)` donde:

* `Dn`: array con todos los registros de la variable `date`.
* `Bn`: array con todos los registros obtenidos en `addOrderBook(dfBids, dfAsks, cubes, midprice, crp)`. 
* `y`: array con la variable `midprice` categorizada.

Finalmente, la función `Tipo2(Dn, Bn, y)` concatena las matrices / vectores de información y devuelve la base de datos resultante.



