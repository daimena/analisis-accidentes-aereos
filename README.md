# Analisis de datos de accidentes aereos

Este repositorio contiene los pasos seguidos y los analisis realizados a una base de datos de accidentes aereos en el contexto de un proyecto academico.
El objetivo de este proyecto es analizar la informacion proporcionada y realizar un dashboard que grafique las observaciones y conclusiones, enfocadas en prevencion de accidedentes aereos.
Se realizo el analisis exploratorio de los datos en python 3 v v3.8.10 y las siguientes dependencias:
- [`pandas` v2.0.3](https://pandas.pydata.org/)
- [`numpy`](https://numpy.org/)
- [`seaborn`](https://seaborn.pydata.org/)
- [`matplotlib`](https://matplotlib.org/)
- [`scipy`](https://scipy.org/)
- [`wordcloud`](https://github.com/amueller/word_cloud)

Y el dashboard fue desarrollado en [`PowerBI`](https://powerbi.microsoft.com/es-es/)

# Analisis Exploratorio de los Datos (EDA)
El EDA consitio de dos etapas:
 - ETL
 - Analisis de las variables numericas
que se encuentran en detalle en un notebook('EDA.ipynb'), donde estan todas las observaciones realizadas y las modificaciones a la tabla original.

Ademas, en un script de python se encuentran solo las lineas de codigo que representaron modificaciones, sin los analisis que llevaron a ellas.
```bash
$ python modificaciones.py
```

## ETL
Haciendo una primera inspeccion de los datos, se ven varios problemas generales de la tabla:
- no unanimidad de idiomas en los nombres de las columnas
- no esta claro el contenido de algunas columnas
- hay valores faltantes
- no hay uniformidad de formatos en algunas columnas

Por lo tanto, se procede a realizar una inspeccion mas minuciosa, columna por columna para intentar resolver estos problemas.
### Nombres de las columnas y contenido:
Se traducen los nombres de las columnas al español y se cambian los nombres que no se consideran representativos del contenido, obteniendo asi las siguientes columnas:

| nombre original | nombre modificado            | descripcion del contenido| ejemplo |
|  -----  |  ------------------  |  ---------  | ------ |
| Unnamed  | id  | identificador del evento    | 2 |
| fecha | fecha            | fecha de ocurrencia del evento | 1908-09-17|
| HORA declarada | hora_decl | hora (en zona horaria local) del evento | 0630|
| Ruta | lugar_accidente | lugar en que ocurrio el accidente | 'Moscow, Russia' |
| OperadOR | empresa_operadora | empresa operadora del vuelo | 'Aeroflot' |
| flight_no | nro_vuelo | numero de vuelo | 'SJ182'|
| route | origen-destino | origen y destino del vuelo separados con un - | 'Prague - Zurich'|
| ac_type | tipo_areonave | tipo de aeronave | 'Vickers Viscount 759D' |
| registration | matricula_aeronave | numero de registro o matricula de la aeronave | 'EC-ANR' |
| cn_ln | nc_nl | numero de construccion y numero de linea, son identificadores del modelo y fabricacion | 1142 |
| all_aboard | total_a_bordo | total de personas a bordo de la aeronave | 280 |
| PASAJEROS A BORDO | pasajeros | total de pasajeros a bordo | 72 |
| crew | tripulacion | total de tripulacion a bordo | 3 |
| cantidad de fallecidos | total_fallecidos | cantidad de personas fallecidas en el accidente | 2 |
| crew_fatalities | tripulacion_fallecidos | cantidad de personas de la tripulacion fallecidas | 3 |
| passenger_fatalities | pasajeros_fallecidos | cantidad de pasajeros fallecidos | 185 |
| ground | tierra | Podria ser la cantidad de personas en el area de control | 1 |
| summary | resumen | resumen del evento | 'The airship flew into a thunderstorm and encou...' |


## Analisis de las variables numericas

El boxplot y los histogramas de la cantidad de pasajeros confirman lo observado en la vision general. El numero de pasajeros es muy disperso y clasifica como outliers a muchos datos que son de interes. teniendo en cuenta los datos de mediana y cuartiles, crearemos una columna con un sistema de clasificacion por numero de pasajeros.
En cuanto a la distribucion por año, observamos que los casos estan mas concentrados entre las decadas de 1940 y 2010, habiendo una cantidad mas o menos relevante y equivalente para cada una de esas decadas.
Similar a lo que ocurre con el numero de pasajeros, vemos que el numero de personas en la tripulacion esta mas fuertemente concentrado en los valores mas bajos. Pero, al ser los maximos mucho menores que los maximos de la columna pasajeros, el desvio estandar es considerablemente menor.

En todo caso, los valores absolutos tanto de tripulacion como de pasajeros brindan informacion incompleta, y tambien me interesaria ver la proporcion que representan respecto al numero de fallecidos. Por lo que creo las columnas:
- porcentaje de la tripulacion fallecidos: relacion entre numero de personas fallecidas de la tripulacion y numero total de tripulacion
- porcentaje de pasajeros fallecidos: relacion entre el numero de pasajeros fallecidos y numero total de pasajeros a bordo
- porcentaje total de fallecidos: relacion entre el numero total de personas fallecidas y el numero de personas a bordo
- relacion entre tripulacion y total: relacion entre el numero de personas de la tripulacion y el numero total de personas a bordo.


## Columnas agregadas
Como resultado de los analisis realizados, se crean columnas que permiten categorizar los casos:

| nombre             | descripcion del contenido| ejemplo |
|  ------------------  |  ---------  | ------ |
| año | año de ocurrencia del evento (extraido de fecha) | 1908 |
| decada | decada de ocurrencia (extraida de fecha) | 1900|
| porc_tripulacion_fallecidos | tripulacion_fallecidos/tripulacion | 0.65 |
| porc_pasajeros_fallecidos | pasajeros_fallecidos / pasajeros | 0.78 |
| porc_total_fallecidos | total_fallecidos/total_a_bordo | 1.0 |
| relacion_tripulacion_total | tripulacion/total_a_bordo | 0.2435 |
| clima | si el evento esta relacionado con algun evento climatico o no | True |
| pista | si el evento esta relacionado con la pista de aterrizaje o despegue | False |

# Dashboard

El dashboard realizado contiene como eje principal de analisis la observacion de los datos enfocada en la prevencion de accidentes aereos. Para esto, se tomaron en cuenta dos KPIs y se realizaron graficas que acompañan a estos.

## KPIs:
1.  **Evaluar la disminución de un 10% la tasa de fatalidad de la tripulación en la década del 2010, comparado con la década del 2000.**

Definiendo la tasa de fatalidad de la tripulación como el número total de tripulantes fallecidos en los accidentes registrados en la década a considerar, dividido en la cantidad total de accidentes aéreos ocurridos en este período de tiempo.

Su fórmula es:
```
Suma total de tripulantes fallecidos en el período de tiempo / Suma total de accidentes en el período de tiempo
```

2. **Evaluar la disminución de un 5% del porcentaje de accidentes relacionados con el clima en la década del 2010, comparado con la década del 2000.**

Definiendo el porcentaje de accidentes relacionados con el clima como la cantidad de accidentes relacionados con el clima en la década considerada, dividido la cantidad total de accidentes de la misma década, representado en porcentaje.

Su fórmula es:
```
Cantidad de accidentes relacionados con el clima / Cantidad de accidentes totales en el período de tiempo * 100
```

## Gráficos y análisis complementarios:
1. Primer KPI:

En cuanto al primer KPI, observamos que no sólo no se cumple este objetivo, sino que la tasa de fatalidad aumenta.

Sin embargo, se puede observar que en un análisis del porcentaje de la tripulación fallecidos (es decir, la relación entre la tripulación fallecida y la tripulación total a bordo) entre las décadas, la diferencia no es significativa y prácticamente se mantiene en el mismo número.

Por otro lado, realizando una clasificación de los vuelos de acuerdo a la cantidad de pasajeros, vemos que en los vuelos con mayor cantidad de pasajeros (máß de 100), esta comparación de tasas se revierte y en la década del 2010 es 25% menor que en la década del 2000.

Para concluir, se podrían analizar en profundidad los protocolos de seguridad de los vuelos de mayor número de pasajeros y compararlos con los de los vuelos con menos pasajeros para ver si potencialemnte mejora la tasa de fallecimientos de la tripulación.

2. Segundo KPI:

Observamos que hay una disminución del porcentaje de eventos relacionados al clima pero no llega al 5%.

Continuamos observando también el porcentaje de pasajeros fallecidos y vemos que es mayor en los eventos relacionados al clima.

Para concluir, podríamos proponer evaluar la prevención de eventos relacionados al clima dada su importancia respecto al fallecimiento de pasajeros.