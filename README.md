# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda, del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. 

La función encargada de esta tarea es `get_data(depth, crpt)` donde:

* `crpt` indica el intercambio de divisas del cual se obtiene el Order Book. Para el desarrollo del TFM se han utilizado los valores: BTCBUSD (bitcoin - United States Dollars Tether) y ETHBUSD (ethereum -  United States Dollars Tether). Las opciones requeridas deben ser introducidas en la lista sobre la que itera el bucle que contiene la función `get_data(depth, crpt)`. Más posibles intercambios de divisas disponibles en: https://www.binance.com/es/trade/BTC_BUSD?layout=pro&type=spot

* `depth` indica el número de ofertas requeridas a cada lado del Order Book. Para el desarrollo del TFM se han empleado 5.000 por lado.

Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece en 1 minuto dado que ha sido el valor utilizado. 

El resultado de una iteración en este algoritmo es un archivo tipo `feather` que cotiene 10.000 registros (5.000 órdenes de compra y 5.000 órdenes de venta) con las variables: `date`, `price`, `quantity`, `side` y `crpt`. 

## Fichero 2: Lectura y tratamiento de base de datos.ipynb

Fichero para la creación de las estructuras de datos.

El eje principal de este script es la función `createMatrix(tipo, BTCOB, cubes)` donde :

* `tipo` puede tomar los valores 1 o 2 para la creación de los datos del planteamiento 1 y del planteamiento 2, respectivamente.
* `BTCOB` es una lista que contiene a los archivos tipo `feather` generados con el Fichero 1.
* `cubes` es el tamaño de las amplitudes del Order Book seleccionadas para la creación de variables. Aunque pueden sufrir variaciones, el programa está especificamente diseñado para que esta lista contenga exactamente 30 elementos.

Esta función genera las estructuras de datos en base al tipo de estructura seleccionada. Las siguientes funciones son comunes para ambos tipos de planteamientos:

* `preProcesado(df)`: toma un archivo tipo `feather` convertido en `pandas data.frame`, transforma los datos al formato correcto, y devuelve 3 elementos: (1) una base de datos con los asks (`dfAsks`), (2) una base de datos con los bids (`dfBids`) y (3) el mid-price (`midprice`).
* `addDate(df)`: guarda la fecha (`date`) y la variable `minBeijing`.

Además también se categoriza la variable `midprice` para que determine los cambios de tendencia a 5 minutos. El resultado será 1 si la tendencia se mantiene o crece respecto a 5 minutos de anterioriedad y será 0 en caso contrario. 

* `CreateBids(dfBids, cubes, midprice)`: crea las variables del grupo II a partir de los  `cubes` introducidos por el usuario.
* `CreateAsks(dfAsks, cubes, midprice)`: crea las variables del grupo III a partir de los  `cubes` introducidos por el usuario.


