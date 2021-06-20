# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda, del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. 

La función encargada de esta tarea es `get_data(depth, crpt)` donde:

* `crpt` indica el intercambio de divisas del cual se obtiene el Order Book. Para el desarrollo del TFM se han utilizado los valores: BTCBUSD (bitcoin - United States Dollars Tether) y ETHBUSD (ethereum -  United States Dollars Tether). Las opciones requeridas deben ser introducidas en la lista sobre la que itera el bucle que contiene la función `get_data(depth, crpt)`. Más posibles intercambios de divisas disponibles en: https://www.binance.com/es/trade/BTC_BUSD?layout=pro&type=spot

* `depth` indica el número de ofertas requeridas a cada lado del Order Book. Para el desarrollo del TFM se han empleado 5.000 por lado.

Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece en 1 minuto.

El resultado de una iteración en este algoritmo es un archivo tipo `feather` que cotiene 10.000 registros (5.000 órdenes de compra y 5.000 órdenes de venta) con las variables: `date`, `price`, `quantity`, `side` y `crpt`. 

## Fichero 2: Lectura y tratamiento de base de datos.ipynb

Fichero para la creación de las estructuras de datos.

El eje principal de este script es la función `createMatrix(tipo, BTCOB, cubes)` donde :

* `tipo`: puede tomar los valores `1` o `2` para la creación de las estructuras de datos de los planteamientos 1 y 2, respectivamente.
* `BTCOB`: lista que contiene a los archivos tipo `feather` generados con el Fichero 1.
* `cubes`: tamaño de las amplitudes del Order Book seleccionadas para la creación de variables. Aunque los valores escogidos pueden sufrir variaciones, el programa está especificamente diseñado para que esta lista contenga exactamente 30 elementos.

Esta función genera las estructuras de datos en base al `tipo` seleccionado. Las siguientes funciones son comunes para ambos tipos de planteamientos:

* `preProcesado(df)`: toma un archivo tipo `feather` convertido en `pd.DataFrame` y transforma los datos a los formatos requeridos. Devuelve 3 elementos: (1) una base de datos con los asks (`dfAsks`), (2) una base de datos con los bids (`dfBids`) y (3) el mid-price (`midprice`).
* `addDate(df)`: guarda la fecha (`date`) y la variable `minBeijing`.

Además `createMatrix(tipo, BTCOB, cubes)` también categoriza la variable `midprice` para que determine sus cambios de tendencia a 5 minutos. Por lo tanto, tomará el valor 1 si la tendencia es constante o ascendente y 0 si es descendente. 

### Planteamiento 1

La función generadora de la estructura de datos para el planteamiento 1 es `Tipo1(Dn, midpriceArray, y)` donde:

* `Dn`: array con todos los registros de la variable `date`.
* `midpriceArray`: array con todos los registros de la variable `midprice`. 
* `y`: array con la variable `midprice` categorizada.

La función `Tipo1(dateArray, midpriceArray, y)` crea registros a partir de la variable `midpriceArray` en el formato (t, t-1, t-2), otorgándole de esta forma memoria al modelo. Finalmente, se añade la variable `y`.

### Planteamiento 2

Para la creación de los datos del planteamiento 2 se introduce una nueva función: `addOrderBook(dfBids, dfAsks, cubes, midprice, crp)` donde:

* `dfBids`: `pd.DataFrame` con los bids creadas en `preProcesado(df)`.
* `dfAsks`: `pd.DataFrame` con los asks creadas en `preProcesado(df)`.
* `cubes`: tamaño de las amplitudes del Order Book introducidas por el usuario en `createMatrix(tipo, BTCOB, cubes)`.
* `midprice`: midprice correspondiente a la instantánea del Order Book almacenada.
* `crp`: criptomoneda. Para el planteamiento 2, toma el valor `btc`.



La función generadora de la estructura de datos para el planteamiento 2 es `Tipo2(Dn, Bn, y)` donde:

* `Dn`: array con todos los registros de la variable `date`.
* `midpriceArray`: array con todos los registros de la variable `midprice`. 
* `y`: array con la variable `midprice` categorizada.

* `CreateBids(dfBids, cubes, midprice)`: crea las variables del grupo II a partir de los `cubes` introducidos por el usuario.
* `CreateAsks(dfAsks, cubes, midprice)`: crea las variables del grupo III a partir de los `cubes` introducidos por el usuario.


