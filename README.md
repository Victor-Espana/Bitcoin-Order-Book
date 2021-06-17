# Bitcoin-Order-Book

Este repositorio forma parte del Trabajo Fin de Máster: Modelización del Proceso de Formación de Precios del Bitcoin Basado en Indicadores de Oferta y Demanda, del alumno Víctor Javier España Roch para la Universidad Europea Miguel de Cervantes (UEMC).

## Fichero 1: Obtención de datos.ipynb

Fichero para la obtención de datos provenientes del Order Book. La función encargada de esta tarea es `get_data(depth, crpt)` donde `crpt` indica el intercambio de divisas del cual se obtiene el Order Book y `depth` el número de ofertas requeridas a cada lado del mismo. Además, es posible programar el intervalo de tiempo transcurrido entre ejecuciones del programa. Por defecto, se establece 1 minuto dado que ha sido el tiempo empleado para el desarrollo del TFM.
