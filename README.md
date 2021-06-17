# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda, del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. 

La función encargada de esta tarea es `get_data(depth, crpt)` donde:

* `crpt` indica el intercambio de divisas del cual se obtiene el Order Book. Para el desarrollo del TFM se han utilizado: BTCBUSD (bitcoin - United States Dollars Tether) y ETHBUSD (ethereum -  United States Dollars Tether). Las opciones requeridas deben ser introducidas en la lista sobre la que itera el bucle que contiene la función `get_data(depth, crpt)`. Más posibles intercambios de divisas disponibles en: https://www.binance.com/es/trade/BTC_BUSD?layout=pro&type=spot

* `depth` indica el número de ofertas requeridas a cada lado del Order Book. Para el desarrollo del TFM se han empleado 5.000 por lado.

Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece en 1 minuto dado que ha sido el valor utilizado.
