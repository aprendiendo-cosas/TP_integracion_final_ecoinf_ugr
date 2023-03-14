# Técnicas de agregación de variables ambientales


> + **_Versión_**: 2022-2023
> + **_Asignatura (titulación)_**: Ciclo de gestión del dato: ecoinformática (máster conservación, gestión y restauración de la biodiversidad. UGR). 
> + **_Autor_**: Curro Bonet-García (fjbonet@uco.es)
> + **_Duración_**: Una sesión de dos horas



## Objetivos

Esta actividad tiene los siguientes objetivos de aprendizaje:

+ Conocer los grandes tipos de herramientas que utilizamos para conocer mejor el funcionamiento de los sistemas naturales.
+ Aprender técnicas de integración de datos relacionados con la asistencia a la toma de decisiones. 



## Hilo argumental

Esta sesión se organiza en torno al siguiente hilo argumental:
+ Enumeración de los factores ecológicos y socioeconómicos que nos permiten responder a la pregunta inicialmente planteada en esta asignatura.
+ Descripción de los tipos de modelos que usamos para mejorar nuestra comprensión de la naturaleza: 
  + Métodos estadísticos: se trata de encontrar una función matemática que explique correctamente cómo cambia la variable dependiente (regeneración de encina bajo el pinar) en función de las múltiples variables independientes. Esta aproximación es la tradicional.
  + Álgebra de mapas: Se trata de un conjunto de técnicas que permiten combinar mapas procedentes de formatos diversos para resolver un problema de ubicación en el espacio de una actividad dada (en nuestro caso ubicación de las zonas con más regeneración).
  + Métodos basados en procesos: Consisten en simular el funcionamiento íntimo de los procesos elementales implicados en la pregunta en cuestión. 


En [esta](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/presentacion/presentacion_agregacion.pptx) presentación se resumen los conceptos anteriores. 


Las siguientes secciones describen brevemente las dos técnicas de integración que hemos visto: evaluación multicriterio e integración mediante operadores booleanos. Antes describiremos el paso previo común a ambas: transformar los mapas de variables en mapas de criterios:

## Transformación de variable a criterio

A lo largo de la asignatura hemos trabajado en la generación de mapas que muestran la distribución espacial de algunas variables biofísicas relevantes para nuestro caso de estudio: densidad de los pinares, diversidad (índice de Shannon), producción primaria, etc. Estas capas expresan la variable en cuestión usando unidades propias de la variable considerada (ej. árboles/hectárea). Además se pueden usar para multitud de situaciones diferentes. Es decir, un mapa de profundidad del suelo, por ejemplo, se puede usar para nuestro caso de estudio y para cualquier otro en el que el suelo sea relevante.

Pero cuando aplicamos a la variable un cierto criterio decisional debemos de transformar el mapa que representa la distribución espacial de dicha variable. Es decir, si queremos tener un mapa que muestre la distribución espacial de un criterio concreto, es necesario transformar la variable de la que partimos. Por ejemplo, imaginemos que queremos ubicar en el territorio un área recreativa. Y resulta que la densidad del bosque es determinante para esto: las zonas más densas no son adecuadas porque en ellas no caben las mesas del área recreativa. Así, en este caso, **al aumentar la densidad del bosque, se reduce la idoneidad para albergar áreas recreativas**. Sin embargo, en el ejemplo que nos ocupa ocurre lo contrario: **al aumentar la densidad aumenta la idoneidad** porque debemos de reducir la competencia intraespecífica en aquellos lugares en los que ésta es más intensa (más densidad). En el proceso de transformación de la *variable* a *criterio*, aplicamos una función de transformación. Esta función cumple dos objetivos:

+ Estandariza los valores de todos los mapas para usar un rango de 0 a 1 (o a veces de 0 a 255)
+ Transforma los valores de la variable (producción primaria, profundidad del suelo, etc.) a valores de idoneidad cualitativos.

La función de transformación puede tener formas diferentes. En nuestro caso asumiremos que la relación entre cada variable y su criterio es lineal. Así que tendremos dos situaciones representadas por los dos dibujos que hay a continuación: Función de preferencia directa e inversa. 



<img src="https://github.com/aprendiendo-cosas/TP_integracion_final_ecoinf_ugr/raw/2022-2023/imagenes/funcion_pertenencia_directa.png" alt="imagen" style="zoom:25%;" />

<img src="https://github.com/aprendiendo-cosas/TP_integracion_final_ecoinf_ugr/raw/2022-2023/imagenes/funcion_pertenencia_inversa.png" alt="imagen" style="zoom:25%;" />



En los dibujos anteriores también puedes ver cómo calcular los parámetros de las funciones. Dado que una recta está definida por dos puntos, es fácil despejar los parámetros de la ecuación de la recta (pendiente y ordenada en el origen) a partir de los valores extremos (que toman valores 0 y 1 en el mapa de idoneidad).


## Evaluación multicriterio

El análisis multicriterio es una técnica muy sencilla que permite conciliar en un mismo mapa criterios diferentes. Es una forma de espacializar criterios decisionales basados en conocimiento experto. Es decir, gracias a esta técnica podemos obtener mapas que recojan los criterios de un centro decisor concreto con relación a un aspecto determinado.  En nuestro ejemplo tenemos varios criterios y se trata de unificarlos en un único mapa que asigne un valor de aptitud global a cada punto ocupado por pinares de repoblación. Por ejemplo:
+ Criterio de densidad: a más densidad más aptitud.
+ Criterio de profundidad del suelo: a más profundidad más aptitud.
+ Criterio de distancia a manchas donadoras de semillas: A más distancia menos aptitud.

Para agregar los criterios empezaremos asignando un peso a cada uno de ellos. La suma de los pesos ha de ser 1. El criterio que tenga más peso contribuirá en mayor medida al mapa final. Procedemos en dos pasos:

El proceso de integración se hace fácilmente con la calculadora de mapas de QGIS u operando con las capas raster en el caso de que trabajemos con R o con Python. El resultado final es la suma del producto de cada criterio (capa _apt_) por su peso. Introducimos la siguiente ecuación en la calculadora raster:

```python  
  ("apt_densidad@1"*0.6)+("apt_cti@1"*0.1)+("apt_distancia@1"*0.3)
 
```
La siguiente imagen muestra el método con otro ejemplo diferente:

<img src="https://github.com/aprendiendo-cosas/TP_integracion_final_ecoinf_ugr/raw/2022-2023/imagenes/pesos_ponderados.png" alt="imagen" style="zoom:40%;" />

Uno de los problemas del análisis multicriterio es que ocurre una compensación de criterios. Si una variable tiene un valor muy alto en un lugar determinado, puede que el resultado final en ese punto sea alto aunque el valor de un criterio importante en ese punto sea bajo. Esto puede hacer que lugares no adecuados sean etiquetados como sí adecuados. Un ejemplo que ilustra esta situación: imaginemos que queremos montar un equipo de baloncesto. Un buen jugador de baloncesto ha de tener las siguientes características:
+ Altura.
+ Fortaleza en los brazos.
+ Tobillos resistentes.

Si le damos distintos pesos a esas variables y con eso "puntuamos" la idoneidad de una lista de personas para entrar en nuestro equipo, puede darse la situación de que una persona tenga alta puntuación final aún teniendo los tobillos débiles. Eso implica tomar una decisión equivocada puesto que estaríamos incluyendo en el equipo a una persona que no rendiría bien. Esta situación denota que hay criterios que no solo tienen más peso que otros, sino que además deben satisfacerse **necesariamente** para tomar una decisión acertada. Y esto nos lleva a la segunda técnica de análisis de la decisión:

## Operadores booleanos

En esta segunda técnica no se asignan pesos a los criterios que combinamos sino que se establecen condiciones que deben cumplir los lugares de nuestra zona de estudio para ser idoneos según el objetivo del proceso decisiona en cuestión. Volviendo al ejemplo del jugador de baloncesto ideal, diríamos algo así: debe de tener los tobillos fuertes **y** **o bien** ser fuerte **o bien** ser alto. De alguna forma estamos diciendo que hay una condición **necesaria** para ser buen jugador, pero no suficiente. Necesita tener los tobillos resistentes y luego una de las otras dos condiciones. Esta forma de combinar criterios decisionales recibe el nombre de integración mediante operadores booleanos porque implican el uso de las conjunciones **o** e **y**. 

En el ejemplo que nos ocupa, usaremos operadores lógicos para integrar las tres capas. Consideraremos que un lugar es adecuado para satisfaer nuestros objetivos si cumple un criterio específico y una combinación de los otros dos. Es decir, pondremos como criterio fundamental que los lugares adecuados tengan una **alta densidad de pinos**. Si no se cumple este criterio, nunca se podrá obtener un valor alto al final del proceso de integración de las variables. Los otros dos criterios serán optativos entre sí. Es decir, seleccionaremos como lugares adecuados aquellos que **o bien están cerca de una mancha de vegetación natural, o bien tienen suelos potencialmente húmedos**. Es decir, en conjunto aplicaremos los siguientes criterios concatenados: Un lugar es considerado como adecuado si:

+ Tiene una alta densidad de pinos **Y**:
+ Está cerca de una mancha de vegetación natural **O** tiene suelos potencialmente húmedos. 

Para implementar esta operación en un SIG, usamos dos operadores matemáticos muy sencillos:

+ Valor **máximo** entre dos capas: es el equivalente al operador **O**. Si en un píxel hay valores altos de aptitud en una capa y bajos en otra, el resultado será alto, puesto que estamos eligiendo el máximo de ambos. Si la aptitud es alta en los dos casos, también seleccionaremos un valor alto. Es decir, se trata de un operador poco restrictivo.
+ Valor **mínimo** entre dos capas: es el equivalente al operador **Y**. Si en un píxel hay valores altos de aptitud en una capa y bajos en otra, el resultado será bajo, puesto que estamos eligiendo el mínimo de ambos. Solo si la aptitud es alta en los dos casos, seleccionaremos un valor alto. Es decir, se trata de un operador poco restrictivo.

La siguiente figura muestra el funcionamiento de estos operadores:

<img src="https://github.com/aprendiendo-cosas/TP_integracion_final_ecoinf_ugr/raw/2022-2023/imagenes/operadores_booleanos.png" alt="imagen" style="zoom:40%;" />

Para aplicar estos operadores a nuestras capas, puedes usar el comando [mosaic de SAGA](https://gis.stackexchange.com/questions/150312/combining-multiple-overlapping-rasters-retain-maximum-value). Este comando está disponible en QGIS. 

Los operadores anteriores son un poco "rígidos" dado que solo seleccionan los valores extremos (mínimo o máximo). Para suavizar el resultado se pueden usar otros operadores como los mostrados en la siguiente figura:

<img src="https://github.com/aprendiendo-cosas/TP_integracion_final_ecoinf_ugr/raw/2022-2023/imagenes/operadores_difusos.png" alt="imagen" style="zoom:40%;" />



## Reclasificación de los resultados obtenidos

Tras aplicar cualquiera de las técnicas anteriores obtendremos un mapa con valores de aptitud que van de 0 a 1. Este mapa puede ser muy útil para comprender mejor el proceso socioecológico en el que estamos trabajando. Pero normalmente cuando se toman decisiones es necesario seleccionar una serie de zonas concretas en las que se va a realizar una actuación determinada. Por eso es útil simplificar el mapa resultante para elegir solo los lugares (=píxeles) que resulten más idoneos para nuestro objetivo. El resto los descartaremos porque no reunen los requisitos que hemos impuesto. Para hacer esta selección aplicaremos una operación muy común en análisis raster: [reclasificación](https://docs.qgis.org/3.4/en/docs/user_manual/processing_algs/qgis/rasteranalysis.html#qgisreclassifybytable). Consiste en reducir la diversidad de valores de un raster asigando nuevos en función de un rango. Por ejemplo, asignaremos el valor de 1 a todos los píxeles que tengan una aptitud igual o mayor de 0.8. Para hacer esto, construimos una tabla de reclasificación.

Para reclasificar una capa rastser en QGIS, buscamos el algoritmo "reclassify by table" en el buscador de procesos de QGIS. Añadimos los siguientes parámetros:
- _raster layer_: _apt\_final.tif_
- _reclassification table_: Abrimos la tabla y añadimos las siguientes filas:


| Desde (Mínimo) | hasta (Máximo)| Valor nuevo|
|----------------:|------------:|---------:|
|0|0.9| 0|
| 0.9 | 1|1|
 - _reclassified raster_ (capa de salida): _apt\_final\_re.tif_



# Lecturas complementarias

Además de lo visto en clase, os paso la siguiente información que puede resultar de utilidad:

+ [Artículo](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/modelos_ecologicos.pdf) que describe distintos tipos de modelos ecológicos. Incide en alguno de los conceptos descritos en la sesión final de nuestra asignatura. 
+ [Resumen](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/herramientas_apoyo_decisiones.pdf) de mi tesis (año 2003, no os riais de los esquemas, por favor. En esa época no existía R). En el texto se describen los conceptos generales sobre integración de información ambiental usando técnicas de operadores booleanos. He recortado solo la parte interesante. Eso hace que el texto no sea muy fluido porque faltan secciones.
+ Varios textos sobre análisis multicriterio:
  + [Artículo](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/multicriterio_seleccion_zonas_plantas_electricas.pdf) sobre el uso del análisis multicriterio para localizar plantas de producción fotovoltaica. 
  + [Interesante](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/MCE_review.pdf) revisión del uso de las técnicas multicriterio en cuestiones de conservación de la naturaleza. Muy recomendable este trabajo.
  + [Artículo](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/ecological_corridors_multicriteria.pdf) que describe cómo la conectividad ecológica del paisaje usando evaluación multicriterio.
  + [Informe](https://github.com/aprendiendo-cosas/integracion_final_ecoinf_ugr/raw/2022-2023/biblio/memoria_apicola_2004.pdf) de la REDIAM que describe cómo se hizo el mapa de aprovechamientos apícolas de Andalucía usando la técnica de la evaluación multicriterio. 

