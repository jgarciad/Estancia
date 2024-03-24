# La desigualdad de genero y el cerebro
## Objetivo
El objetivo de este proyecto consiste en determinar si los niveles de desigualdad de género por municipio en México son reelevantes para la estructura cerebral dentro de un grupo de individuos de estuido. 

## Procedimiento
En términos generales, para poder correr el modelo debemos seguir los siguientes pasos:

* Definir la región de interés (ROI)
* Correr el modelo de regresión sobre la región de interés
* Determinar la tasa FDS del nivel de significacia de nuestras variables explicativas
* En caso de que la variable sea significativa, mapear las zonas del cerebro afectadas

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


## Modelo

Para cada individuo tenemos asociadas 4 variables que son: edad, genero (sólo hombre y mujer), DX_bl que hace referencia a si el individuo corresponde al grupo control o es paciente y el índice de desigualdad de género o IDG. En cualquier caso, hemos probado una regresión líneal multiple considrando las siguientes combinaciones:

1) $cortical_thickness = IDG$
2) $cortical_thickness = IDG + Age$
3) $cortical_thickness = IDG + Sex$
4) $cortical_thickness = IDG + Dx_bl$
5) $cortical_thickness = IDG + Age + Sex$
6) $cortical_thickness = IDG + Age + Dx_bl$
7) $cortical_thickness = IDG + Sex + Dx_bl$
8) $cortical_thickness = IDG + Age + Sex + Dx_bl$

Dada la naturaleza del problema, utilizaremos pruebas FDR (False Discovery Rate) para determinar si una variable es significativa o no. Con las condiciones planteadas en la sescción anteriror, la variable IDG únicamente resulta ser relevante en el modelo 1, los resultados de las pruebas FDR se muestran a continuación:

Hemisferio izquierdo

| Treshold              | F-statistic | tvalue-(Intercept) | tvalue-IDG |
| :---------------- | :------: | ----: | ----: |
| 1%       |   NA  | 8.807274 | NA |
| 5%       |   NA   | 8.807274 | NA |
| 10%    |  NA   | 8.807274 | NA |
| 15% |  4.637186   | 8.807274 | 2.153413 |
| 20% |  3.356472   | 8.807274 | 1.832068 |

Hemisferio derecho

| Treshold              | F-statistic | tvalue-(Intercept) | tvalue-IDG |
| :---------------- | :------: | ----: | ----: |
| 1%       |   NA  | 9.355915 | NA |
| 5%       |   NA   | 9.355915 | NA |
| 10%    |  NA   | 9.355915 | NA |
| 15% |  3.908117   | 9.355915 | 1.976896 |
| 20% |  2.827187   | 9.355915 | 1.681424 |


Considerando una tasa de falsos positivos del 15% en ambos casos, la variable IDG se vuelve relevante para aquellos valores T mayores en valor absoluto a 2.153413 en el caso del hemisferio izquierdo y de 1.976896 en el caso del hemisferio derecho. Las siguientes imagenes muestran las zonas del cerebro donde el IDG se torna relevante. 

Hemisferio izquierdo vista lateral interna

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/bd970989-9b9c-4fb2-bb99-8e8925c1f548">


Vista lateral externa

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/b3958188-097c-4bbd-9fee-54b065d5d2a0">

Hemisferio derecho vista lateral interna

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/ce0869b3-a506-49f4-aad9-7d25a9394bae">

Vista lateral externa

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/a3bfb8d5-9f74-4c0e-bf7d-7248228e667e">


## Modelo. Propuesta 2. 

Siguiendo pasos muy similares a lo que se plantea en la sección de procedimiento, esta vez vamos a seleccionar la ROI considerando la mascara de cada paciente en particular y no la mascara primedo como lo hemos hecho en un inicio. Para hacer eso, dentro de la terminal de la VM escribimos lo siguiente:

```
Hemosferio izquierdo:
for file in neuro_data/output/*/temp/*_subcortical_mask.mnc; do volume_object_evaluate $file neuro_data/output/$(basename -s _subcortical_mask.mnc $file)/surfaces/$(basename -s _subcortical_mask.mnc $file)_mid_surface_left_81920.obj ROI2/ROI/$(basename -s .mnc $file)_left.txt; done

Hemisferio derecho:
for file in neuro_data/output/*/temp/*_subcortical_mask.mnc; do volume_object_evaluate $file neuro_data/output/$(basename -s _subcortical_mask.mnc $file)/surfaces/$(basename -s _subcortical_mask.mnc $file)_mid_surface_right_81920.obj ROI2/ROI/$(basename -s .mnc $file)_right.txt; done

```

La siguiente imagen, muestra el ejemplo de cómo se aprecia la región subcortical de un paciente tomando su propia mascar. 

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/e4949009-d1d3-48c8-be7c-81b59aee8026">

Cada uno de esos achivos .txt contiene 0 y valores positivos, esos últimos hacen referencia a la zona subcortical del cerebro. Lo siguiente es procesar estos archivos, convertiremos los 0's en 1's y los valores positivos en 0's, con ello lo que tendremos es una mascara de lo que NO es la zona subcortical. Para ello hay que pocesar cada archivo, esto lo haremos en R.

```
#Hemisferio izquierdo
for( i in 1:length(id)){
  filepath <- paste0("ROI2/ROI/Left/",id[i],"_subcortical_mask_left.txt")
  assign(paste0("aux"), read.table(filepath, sep = ",", header=FALSE))
  aux <- aux %>% mutate(V2 = if_else(V1>0,0,1))
  aux <- aux %>% select(V2)
  write.table(aux, paste0("ROI2/ROI/Left/",id[i],"_mask_left.txt"), row.names = FALSE, col.names = FALSE)
}


#Hemisferio derecho
for( i in 1:length(id)){
  filepath <- paste0("ROI2/ROI/right/",id[i],"_subcortical_mask_right.txt")
  assign(paste0("aux"), read.table(filepath, sep = ",", header=FALSE))
  aux <- aux %>% mutate(V2 = if_else(V1>0,0,1))
  aux <- aux %>% select(V2)
  write.table(aux, paste0("ROI2/ROI/right/",id[i],"_mask_right.txt"), row.names = FALSE, col.names = FALSE)
}

```

Una vez hecho esto, es momento de proyectar la ROI en cada paciente, para obtener el área de interes en cada uno de ellos y poder hacer la prueba estadística.

```
#Hemosferio izquierdo
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_left.txt; do vertstats_math -mult ROI2/ROI/Subcortical_left.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

#Hemisferio derecho
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_right.txt; do vertstats_math -mult ROIFinal/Subcortical_right.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

```


