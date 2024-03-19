# La desigualdad de genero y el cerebro
## Objetivo
El objetivo de este proyecto consiste en determinar si los niveles de desigualdad de género por municipio en México son reelevantes para la estructura cerebral dentro de un grupo de individuos de estuido. 

## Procedimiento
En términos generales, para poder correr el modelo debemos seguir los siguientes pasos:

*1. Definir la región de interés (ROI)
*2. Correr el modelo de regresión sobre la región de interés
*3. Determinar la tasa FDS del nivel de significacia de nuestras variables explicativas
*4. En caso de que la variable sea significativa, mapear las zonas del cerebro afectadas

El primer paso para obtener la región de interés será definir la estructura promedio del cerebro de nuestros pacientes para utilizarla como base para mostrar los resultados que se obtengan y para que a tevés de ella podamos definir la ROI. Para ello, desde la terminal de la VM escribiremos el siguiente código:

```
#Hemisferio izquierdo
average_surfaces lh_average.obj none none 2 neuro_data/output/*/surfaces/*_mid_surface_rsl_left_81920.obj

#Hemisferio derecho
average_surfaces rh_average.obj none none 2 neuro_data/output/*/surfaces/*_mid_surface_rsl_right_81920.obj

```
Estos comandos generan dos archivos que son *lh_average.obj* y *rh_average.obj* que representan la estrucura promedio del cerebro de las personas dentro del estuidio.

Nuestra ROI será el cerebro sin incluir la zona subcortical, en nuestro caso, cada paciente tiene definica una mascara para esta región, la cual usaremos para definir la ROI. Estos archivos se encuentran en la ruta neuro_data/output/*/temp/*_subcortical_mask.mnc. Notar que la extensión del archivo es *.mnc*. Dado que cada individuo en el estudio tiene su propia región subcortical es convenciente definir una región promedio con la que podamos enmascarar el área de interés dentro de nuestro estudio. Para ello, obtenemos la región subcortical promedio usando el siguiente código dentro de la terminal:

```
mincaverage neuro_data/output/*/temp/*_subcortical_mask.mnc ROIFinal/Subcortical_ROI.mnc
```
El archivo resultante será el proemdio de la región Subcortical de nuestros pacientes. Una vez que se hace esto, proyectamos dicha región sobre un file txt con los vertices que corresponen a esta región. Esto lo hacemos tanto para el hemisferio izquierdo como para el derecho. También se hace desde terminal y el comando es el siguiente:

```
#Hemosferio izquierdo:
volume_object_evaluate ROIFinal/Subcortical_ROI.mnc lh_average.obj ROIFinal/Subcortical_left.txt

#Hemisferio derecho:
volume_object_evaluate ROIFinal/Subcortical_ROI.mnc rh_average.obj ROIFinal/Subcortical_right.txt

```
Cada uno de esos achivos .txt contiene 0 y valores positivos, esos últimos hacen referencia a la zona subcortical del cerebro. Lo siguiente es procesar estos archivos, convertiremos los 0's en 1's y los valores positivos en 0's, con ello lo que tendremos es una mascara de lo que NO es la zona subcortical. Esto se puede hacer en un excel incluso, ya que sólo se trata de dos archivos.

Una vez hecho esto, es momento de proyectar la ROI en cada paciente, para obtener el área de interes en cada uno de ellos y poder hacer la prueba estadística. Para proyectar la ROI obtenida, corremos desde terminal los siguientes comandos:

```
#Hemosferio izquierdo
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_left.txt; do vertstats_math -mult ROIFinal/Subcortical_left.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

#Hemisferio derecho
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_right.txt; do vertstats_math -mult ROIFinal/Subcortical_right.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

```

El resultado de lo anterior será la estructura del cerebro sin incluir la zona subcortical. La siguiente imagen muestra un ejemplo de ello:

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/fdebb163-98b0-4c81-b226-88d7bd399417">

La zona en azul represtna el área subcortical y que hemos descartado para nuestro análisis. 

