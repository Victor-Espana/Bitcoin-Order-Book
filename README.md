# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. 

Función: `get_data(depth, crpt)`

* `crpt`: `str`. Intercambio de divisas del cual se obtiene el Order Book. Para el desarrollo del TFM se han utilizado los valores: BTCBUSD (bitcoin - United States Dollars Tether) y ETHBUSD (ethereum -  United States Dollars Tether). Las opciones requeridas deben ser introducidas en la lista sobre la que itera el bucle que contiene la función `get_data()`. Más posibles intercambios de divisas disponibles en: https://www.binance.com/es/trade/BTC_BUSD?layout=pro&type=spot

* `depth`: `int`. Número de ofertas requeridas a cada lado del Order Book. Para el desarrollo del TFM se han empleado 5.000 (por lado).

Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece en 1 minuto.

El resultado de una iteración en este algoritmo es un archivo tipo `feather` con 10.000 registros (5.000 órdenes de compra y 5.000 órdenes de venta) con las variables: `date`, `price`, `quantity`, `side` y `crpt`. 

## Fichero 2: Lectura y tratamiento de base de datos.ipynb

Fichero para la creación de las estructuras de datos.

Función principal: `createMatrix(tipo, BTCOB, cubes)`

* `tipo`: `int`. Puede tomar los valores `1` o `2` para la creación de las estructuras de datos de los planteamientoa 1 y 2, respectivamente.
* `BTCOB`: `list`. Contiene los archivos tipo `feather` de la criptomoneda bitcoin generados con el Fichero 1.
* `cubes`: `list`. Valores con los tamaños de las amplitudes del Order Book seleccionadas para la creación de las variables de los grupos II y III. La lista debe contener exactamente 30 elementos.

Esta función genera las estructuras de datos en base al `tipo` seleccionado. Las siguientes funciones son comunes para ambos tipos de planteamientos:

* `preProcesado(df)`: toma un archivo tipo `feather` convertido en `pd.DataFrame`, transforma los datos a los formatos requeridos y crea la variable volumen (`PrQt`). Devuelve 3 elementos: (1) una base de datos con las ofertas del lado de los asks (`dfAsks`), (2) una base de datos con las ofertas del lado de los bids (`dfBids`) y (3) el mid-price (`midprice`).
* `addDate(df)`: guarda la fecha (`date`) y la variable `minBeijing`. La información se almacena en un array: `Dn`.

Por otro lado, `createMatrix()` también categoriza la variable `midprice` para que determine sus cambios de tendencia a 5 minutos. De esta forma, tomará el valor 1 si la tendencia es constante o ascendente y 0 si es descendente. 

### Planteamiento 1

Función: `Tipo1(Dn, midpriceArray, y)`

* `Dn`: array con todos los registros creados por la función `addDate()`.
* `midpriceArray`: array con todos los registros de la variable `midprice` creados por la función `preProcesado()`. 
* `y`: array con la variable `midprice` categorizada.

La función `Tipo1()` crea registros a partir de la variable `midpriceArray` en el formato (t, t-1, t-2) (memoria). Finalmente, se concatenan `Dn` e `y`.

### Planteamiento 2

Función: `addOrderBook(dfBids, dfAsks, cubes, midprice, crp)`

* `dfBids`: `pd.DataFrame` (side = bids) creado en `preProcesado()`.
* `dfAsks`: `pd.DataFrame` (side = asks) creado en `preProcesado()`.
* `cubes`: tamaño de las amplitudes del Order Book introducidas por el usuario en `createMatrix()`.
* `midprice`: mid-price de la instantánea del Order Book almacenada. Tercer elemento devuelto de `preProcesado()`.
* `crp`: criptomoneda. Para el planteamiento 2, toma el valor `btc` (referido a bitcoin).

`addOrderBook()` contiene las siguientes funciones:

* `CreateBids(dfBids, cubes, midprice)`: crea las variables del grupo II a partir de los `cubes` introducidos por el usuario.
* `CreateAsks(dfAsks, cubes, midprice)`: crea las variables del grupo III a partir de los `cubes` introducidos por el usuario.
* `WeightedFeatures(dfSide, side)`: crea las variables del grupo IV. Puede tomar los valores {`dfSide = dfBids`, `side = 'bid'`} / {`dfSide = dfAsks`, `side = 'ask'`}. Solo se ejecuta cuando `crp = 'btc'`.
* `CreateSlope(offers, cubes, midprice)`: crea las variables del grupo V. El argumento `offers` debe contener el objeto devuelto por `CreateBids()`/`CreateAsks()` seleccionando tan solo 15 de sus filas. De la misma forma, para el argumento `cubes` tan solo deben introducirse 15 elementos. Por defecto, se seleccionan para ambos casos `[0:30:2]`. 
* `DistrFeatures(dfSide, side)`: crea las variables del grupo VI. Puede tomar los valores {`dfSide = dfBids`, `side = 'bid'`} / {`dfSide = dfAsks`, `side = 'ask'`}. Solo se ejecuta cuando `crp = 'btc'`.

Una vez ejecutadas todas las funciones, se concatenan todos los valores obtenidos y se devuelve una única lista (`Bn`).

La función generadora de la estructura de datos para el planteamiento 2 es `Tipo2(Dn, Bn, y)` donde:

* `Dn`: array obtenido de `addDate()`.
* `Bn`: array obtenido de `addOrderBook()`. 
* `y`: array con la variable `midprice` categorizada.

Finalmente, la función `Tipo2()` concatena las matrices / vectores de información y devuelve la base de datos resultante, la cual contiene 107 columnas.

## Fichero 3: Reducción de dimensionalidad.ipynb

Tal y como se demuestra en el documento escrito, las variables de los grupos II, III y V presentan una dependencia lineal muy elevada entre ellos. Este hecho justifica la utilización de PCA para la reducción de la dimensionalidad.

El Fichero 3 contiene el código y los resultados de aplicar PCA sobre el conjunto de datos desarrollado anteriormente. Se muestran:

* Gráficos de proporción total de varianza explicada y de proporción explicada por cada componente.
* Coeficientes de la combinación lineal que resultan de la aplicación de PCA.
* Proyección de cada grupo de variables al espacio de 3 dimensiones generado por PCA. 
* Proyección de cada grupo de variables al espacio de 2 dimensiones generado por PCA.
* Order Book canónicos a partir de los cuales se podría determinar cualquier Order Book observado (a partir de PCA sobre los grupos II y III). 

Finalmente, se genera una nueva base de datos con las variables de los grupos II, III y V reducidas por Componentes Principales. Estas nuevas variables serán incluidas en los datos obtenidos del planteamiento 2 (eliminando las originales) y conformando así, la forma final de esta estructura de datos.

### Planteamiento 3

Función: `memory(X)`:

* `X`: base de datos generada en Fichero 3.

Transforma los datos relativos a los grupos II, III y V del planteamiento 2 al formato (t, t-1, t-2) (memoria). Finalmente se concatenan aquellas variables no sometidas a este proceso dando lugar a la estructura de datos resultante del planteamiento 3.

### Planteamiento 4

Función: `Tipo4(ETHOB, cubes):
* `ETHOB`: `list`. Contiene los archivos tipo `feather` de ethereum generados con el Fichero 1.
* `cubes`: `list`. Valores con los tamaños de las amplitudes del Order Book seleccionadas para la creación de las variables de los grupos II y III. La lista debe contener exactamente 30 elementos.

Esta función aplica un procedimiento prácticamente idéntico al descrito en el Planteamiento 2. No obstante, en este caso no se crean las variables de los grupos IV y VI. Tal y como ocurría anteriormente, a la finalización del proceso, este grupo de variables generadas a partir de los datos de ethereum son reducidas mediante PCA. Finalmente, al nuevo conjunto de datos se le aplica la función `memory()`. 

Como último paso, el conjunto de datos generado es concatenado a la estructura de datos del planteamiento 3, creando así, la estructura de datos del planteamiento 4.

## Fichero 4: Descriptivo & Gráfico.ipynb

Script dedicado a la generación de descriptivos y gráficos de los que obtener las justificaciones presentes en el capítulo 4. Exploración y selección de características. Con esta información se responde a las siguientes preguntas:

* ¿Cuáles debían ser las aperturas del Order Book para la creación de las variables de los grupos II y III?
* ¿A cuánto tiempo futuro podemos predecir con el planteamiento descrito?
* ¿Cómo evoluciona el mid-price en cada minuto?
* ¿Cómo es la autocorrelación en la variable mid-price?
* ¿Cómo es la correlación entre las variables de los grupos II, III y V?

## Fichero 5: Modelización.ipynb

* Se dividen los datos en 80% para el conjunto de entrenamiento y 20% para el conjunto de test. Sobre el 80% se aplica la técnica de K-Fold Cross-validation con k = 5. 
* Se plantea un conjunto de parametrizaciones para cada algoritmo propuesto (RandomForest y XGBoost), común para los 4 planteamientos
* Para cada planteamiento y técnica, se muestran las curvas ROC, las matrices de confusión y las métricas: accuracy, precision, recall y F1, tanto para el conjunto de entrenamiento como para el conjunto de test.
