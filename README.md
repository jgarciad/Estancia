# La desigualdad de genero y el cerebro
## Objetivo
El objetivo de este proyecto consiste en determinar si los niveles de desigualdad de género y el índice de marginación por municipio en México, son reelevantes para la estructura cerebral dentro de un grupo de individuos de estuido. 

## Procedimiento
En términos generales, para poder correr el modelo debemos seguir los siguientes pasos:

* Definir la región de interés (ROI)
* Correr el modelo de regresión sobre la región de interés
* Determinar la tasa FDS del nivel de significacia de nuestras variables explicativas
* En caso de que la variable sea significativa, mapear las zonas del cerebro afectadas

**NOTA**: para mayor detalle sobre el uso de la VM y el procedimiento aquí explicado ver la carpeta "ScriptBash"

El primer paso para obtener la región de interés será definir la estructura promedio del cerebro de nuestros pacientes para utilizarla como base para mostrar los resultados que se obtengan y para que a tevés de ella podamos definir la ROI. Para ello, desde la terminal de la VM escribiremos el siguiente código:

```
#Hemisferio izquierdo
average_surfaces lh_average.obj none none 2 neuro_data/output/*/surfaces/*_mid_surface_rsl_left_81920.obj

#Hemisferio derecho
average_surfaces rh_average.obj none none 2 neuro_data/output/*/surfaces/*_mid_surface_rsl_right_81920.obj

```
Estos comandos generan dos archivos que son *lh_average.obj* y *rh_average.obj* que representan la estrucura promedio del cerebro de las personas dentro del estuidio.

Nuestra ROI será el cerebro sin incluir la zona subcortical, en nuestro caso, cada paciente tiene definida una mascara para esta región, la cual usaremos para definir la ROI. Estos archivos se encuentran en la ruta neuro_data/output/*/temp/*_subcortical_mask.mnc. Notar que la extensión del archivo es *.mnc*. Vamos a seleccionar la ROI considerando la mascara de cada paciente en particular, para hacer eso, dentro de la terminal de la VM escribimos lo siguiente:


```
Hemosferio izquierdo:
for file in neuro_data/output/*/temp/*_subcortical_mask.mnc; do volume_object_evaluate $file neuro_data/output/$(basename -s _subcortical_mask.mnc $file)/surfaces/$(basename -s _subcortical_mask.mnc $file)_mid_surface_left_81920.obj ROI2/ROI/$(basename -s .mnc $file)_left.txt; done

Hemisferio derecho:
for file in neuro_data/output/*/temp/*_subcortical_mask.mnc; do volume_object_evaluate $file neuro_data/output/$(basename -s _subcortical_mask.mnc $file)/surfaces/$(basename -s _subcortical_mask.mnc $file)_mid_surface_right_81920.obj ROI2/ROI/$(basename -s .mnc $file)_right.txt; done

```

La siguiente imagen, muestra el ejemplo de cómo se aprecia la región subcortical de un paciente tomando su propia mascara. 

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

Una vez hecho esto, es momento de proyectar la ROI en cada paciente, para obtener el área de interes en cada uno de ellos y poder hacer la prueba estadística. Para ello escribimos los siguientes comandos en terminal:

```
#Hemosferio izquierdo
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_left.txt; do vertstats_math -mult ROI2/ROI/Subcortical_left.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

#Hemisferio derecho
for file in neuro_data/output/*/thickness/*_native_rms_rsl_tlaplace_30mm_right.txt; do vertstats_math -mult ROIFinal/Subcortical_right.txt $file -old_style_file ROIFinal/thickness/$(basename -s .txt $file).txt; done

```

Para nuestro nuevo modelos hemos incluido una nueva variante que es considerar el índice de marginación, de tal manera que planteamos dos propuestas:

1) $cortical_thickness = IDG + IM$
2) $cortical_thickness = IM$

El resultado del primer modelo basado en la pruena FDR es el siguiente:

Hemisferio izquierdo

| Treshold              | F-statistic | tvalue-(Intercept) | tvalue-IDG | tvalue-IM |
| :---------------- | :------: | ----: | ----: | ----: |
| 1%       |   18.371772  | 2.675596 | NA | 5.646297 |
| 5%       |   18.371772   | 2.019872 | NA | 4.957493 |
| 10%    |  4.252772   | 1.686800 | 2.387421 | 4.583074 |
| 15% |  2.938386   | 1.472713 | 1.962351 | 4.583074 |
| 20% |  2.368125   | 1.310805 | 1.684022 | 2.343637 |


Hemisferio derecho

| Treshold              | F-statistic | tvalue-(Intercept) | tvalue-IDG | tvalue-IM |
| :---------------- | :------: | ----: | ----: | ----: |
| 1%       |   11.363943  | 2.684823 | NA | 4.428994 |
| 5%       |   8.553654   | 2.020289 | NA | 3.759492 |
| 10%    |  3.821878   | 1.688829 | 2.271406 | 3.326305 |
| 15% |  2.896442   | 1.475385 | 1.849553 | 2.929601 |
| 20% |  2.314915   | 1.313015 | 1.617411 | 2.552914 |


Como se puede obsrvar, el modelo muestra mejores resultados cuando se combina con el IM mejorando inculso el treshold de falsos positivos, considerando únciamente hata 10%. Como ejemplo, la siguientes imagenes muestran las zonas del cerebro en donde el IDG se vuelve significativo. 

Hemisferio izquierdo vista lateral interna

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/d49a14e7-7ba8-4660-bf58-613331bb7bb7">

Hemisferio izquierdo vista lateral externa

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/730f481d-cc85-48bb-9e8d-2668b35c0e01">

Hemisferio derecho vista lateral interna

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/410a1b51-68ee-4c74-9433-896d1fd13b1b">

Hemisferio derecho vista lateral externa

<img width="500" alt="Hemisferio Izquierdo" src="https://github.com/cheque/neuro-data/assets/48302106/84d6c888-72a6-44c4-847a-42dd1e7764f3">

Lo anterior también se puede hacer consideradno el índice de marginación.
