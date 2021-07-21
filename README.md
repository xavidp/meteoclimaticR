meteoclimaticR <img src="img/logo.png" align="right" alt="" width="140" />
=========================================================
# `meteoclimaticR`: Descarga de datos de [Meteoclimatic](https://www.meteoclimatic.net/)

## Descripción

**meteoclimaticR** permite la descarga de datos meteorológicos proporcionados por la red  Meteoclimatic. Se pueden obtener los datos actuales de temperatura, humedad relativa, precipitación, viento y presión atmosférica. Además también se pueden descargar los valores máximos y mínimos del mismo día. 
**Novedad de la última versión (21-07-2021)**: Descarga de los históricos diarios desde 2012 de forma masiva !!!

## ¿Cómo funciona?

### Instalación

``` r
# Descarga desde github (0.0.2):
# install.packages("remotes")
remotes::install_github("lemuscanovas/meteoclimaticR")
```

### Descarga de los últimos registros meteorológicos:

Para la descarga de los datos meterológicos más recientes existe la función `current_download`. Ésta tiene un funcionamento muy simple. Hay que especificar el `id` de la comunidad autónoma (CCAA), provincia o estación meteorológica que proporciona Meteoclimatic. Lógicamente, para los ids relativos a CCAA y provincias, se descargan todas las estaciones pertenecientes a la región solicitada.
Así, por ejemplo, si el usuario tienen interés en descargar los datos de la CCAA Illes Baleares, debe especificar: `id = ESIBA`; Si desea los datos de las estaciones de la isla de Mallorca, entonces `id = ESIBA07`. Para la estación concreta del Port de Sòller: `id = ESIBA0700000107108A`. Si, por ejemplo se desean los datos de Catalunya, País Valencià e Illes Balears, se puede generar un simple vector: `id = c("ESCAT","ESPVA","ESIBA")`.
También es posible exportar directamente la descarga en un fichero excel especificando `save_excel = T`

Veámoslo en un ejemplo:

```r
library(meteoclimaticR)
ppcc <- current_download(id = c("ESCAT"),save = F)

```

A partir de estos datos y usando `ggplot2` es muy sencillo realizar un a visualización espacial de la variable de interés. Sin embargo, se ofrece la posibilidad de un mapeo automático con la función `plot_met`, la cual permite realizar la visualización de cualquiera de las varaibles mostradas en el objeto `bcn_met`. En esta ocasión vamos a plotear la temperatura máxima.
```{r pressure, echo=T}
plot_met(bcn_met, title = "Temperatura máxima 20/07/2020", caption = "Meteoclimatic")
```
<img src="img/bcn_tx.png" align="centre" alt="" width="500" />
Dentro de plot_met se puede cambiar de paleta, tamaño de los puntos, etc... 

### Ejemplo 2: un conjunto de provincias

Para descargar varias provincias a la vez, simplemente hay que crear un vector con todos los identificadores deseados:
``` r
id_ppcc <- c("ESCAT08000000","ESCAT25000000","ESCAT17000000","ESCAT43000000", # cat
             "ESPVA12000000","ESPVA46000000","ESPVA030000000", #val
             "ESIBA0700000") #bal
```
Ahora simplemente se trata de usar la función `meteoclimatic_download` como en el ejemplo anterior. Note que la descarga puede demorar medio minuto.

``` r
# Descarga de los datos de temperatura máxima de la Provincia de Barcelona
ppcc_met <- meteoclimatic_download(id_prov = id_ppcc)
ppcc_met

# # A tibble: 523 x 26
#    name  id      num mes     año hora    lat   lon Temp.unit Temp.act Temp.max Temp.min Hum.unit Hum.act Hum.max Hum.min
#    <chr> <chr> <dbl> <chr> <dbl> <chr> <dbl> <dbl> <chr>        <dbl>    <dbl>    <dbl> <chr>      <dbl>   <dbl>   <dbl>
#  1 Aigu~ ESCA~    20 Jul    2020 20:2~  41.8  2.25 C             21.5     31       15.3 %             85      91      37
#  2 Alel~ ESCA~    20 Jul    2020 20:1~  41.5  2.29 C             24.2     29.3     21.9 %             82      84      55
#  3 Alel~ ESCA~    20 Jul    2020 20:1~  41.5  2.29 C             22.4     28.1     21.6 %             87      88      57
#  4 Aren~ ESCA~    20 Jul    2020 20:1~  41.6  2.56 C             23.9     29       20.9 %             85      85      65
#  5 Aren~ ESCA~    20 Jul    2020 20:1~  41.6  2.55 C             24.7     27.6     21.2 %             85      86      63
#  6 Aren~ ESCA~    20 Jul    2020 20:2~  41.6  2.54 C             23.8     28.2     20.7 %             74      74      48
#  7 Arge~ ESCA~    20 Jul    2020 20:1~  41.6  2.39 C             23.9     29.9     20.9 %             84      87      56
#  8 Bada~ ESCA~    20 Jul    2020 20:2~  41.5  2.24 C             25.2     29.6     22.4 %             77      80      66
#  9 Bada~ ESCA~    20 Jul    2020 20:1~  41.4  2.22 C             25.6     30       22.1 %             77      85      48
# 10 Bada~ ESCA~    20 Jul    2020 20:1~  41.4  2.22 C             26.2     29.9     22.7 %             71      79      47
# # ... with 513 more rows, and 10 more variables: Pres.unit <chr>, Pres.act <dbl>, Pres.max <dbl>, Pres.min <dbl>,
# #   Vient.unit <chr>, Vient.act <dbl>, Vient.dir <dbl>, Vient.max <dbl>, Precip.unit <chr>, Precip.total <dbl>```
```
Y ahora visualizamos:

``` r
plot_met(ppcc_met,var =  "Hum.min", title = "Humedad Relativa mínima 20-07-2020",
         caption = "Meteoclimatic",units = "HR (%)",pal = rev(pals::jet(100)))
```
<img src="img/ppcc_hr.png" align="centre" alt="" width="500" />

A continuación se muestra el vector para todas la regiones de la Península:
``` r
id_prov <- c("ESCAT08000000","ESCAT25000000","ESCAT17000000","ESCAT43000000", # cat
             "ESPVA12000000","ESPVA46000000","ESPVA030000000", #val
             "ESMUR30000000", # mur
             "ESAND040000000","ESAND18000000","ESAND23000000","ESAND14000000","ESAND29000000",
             "ESAND41000000","ESAND1100000011600A","ESAND21000000", # and
             "PTSUR080000000","PTSUR120000000","PTCEN110000000",
             "PTCEN200000000","PTCEN100000000","PTNOR040000000","PTNOR180000000", # pt
             "ESEXT060000000","ESEXT10000000", #ext
             "ESCLM45000000","ESCLM13000000","ESCLM16000000","ESCLM020000000",
             "ESCLM19000000", #cylam
             "ESMAD28000000", #mad
             "ESCYL0500000005","ESCYL40000000","ESCYL42000000","ESCYL42000000",
             "ESCYL34000000","ESCYL24000000","ESCYL49000000","ESCYL47000000",
             "ESCYL37000000","ESCYL49000000", #cyleo
             "ESGAL320000000","ESGAL36000000","ESGAL15000000","ESGAL27000000", # gal
             "ESAST33000000", #astu
             "ESCTB39000000" ,#cant
             "ESEUS48000000","ESEUS20000000","ESEUS010000000", #eusk
             "ESNAF31000000", #nav,
             "ESLRI26000000", #rioj
             "ESARA22000000","ESARA50000000","ESARA44000000", #arag
             "ESIBA0700000","ESAND52000000")#"ESICA3800000") # bal, melilla y canarias
```
Para cualquier cuestión no dudes en contactarme: mlemus@ub.edu
