# Links de interés: 

https://developers.google.com/earth-engine/apidocs : Revisar todas las funciones disponibles para la interfaz del usuario. Se indican los argumentos que acepta cada función, así como el tipo de objeto de la salida. En la sección de **Client libraries** se pueden revisar los métodos disponibles en GEE clasificados por tipo de objeto del servidor.

https://epsg.io/ : Consultar los codigos EPSG.

https://htmlcolorcodes.com/es/ : Consultar códigos hexadecimales de los colores, para poder pasarlos como argumentos.




# Consideraciones de GEE:

> Google Earth Engine utiliza una versión anterior de JavaScript a ES6, de modo que para declarar variables no se utiliza la declaratoria const o let, solo es válida la declaratoria var.

> En algunas ocasiones, al intentar cargar objetos en la pantalla de mapa, que sean producto de procesamientos demandantes, la consola puede mostrar un error indicando que se superó el tiempo de espera o el límite de memoria disponible. En estos casos, se sugiere exportar el resultado en lugar de tratar de cargarlo en la pantalla de mapa y después visualizarlo de manera local en algún SIG o software similar (por ejemplo, QGIS).

> La proyección por defecto para mostrar la información en la pantalla de mapa corresponde a EPSG:3857. 

> Solo se pueden exportar imágenes en formato ee.Image. Sin embargo, no siempre resulta obvio el formato de las imágenes,ya que algunas veces se pueden convertir a un formato de arreglo (ee.Array), tras lo cual hay que volver a convertir la información a formato de imagen (ee.Image) para poder exportarla. La aplicación permite exportar únicamente en formato GeoTiff o TFRecord (tensores), siendo la primera la opción por defecto.

> En GEE muchas operaciones utilizan el término de scale, sin embargo, este no se refiere a la escala de trabajo en un sentido tradicional (por ejemplo, 1:50000), sino que se refiere al tamaño de píxel expresado en metros.

> En algunas ocasiones, al intentar exportar una imagen muy grande (mayor a 10 000 000 de píxeles), la consola puede mostrar un error indicando que el objeto a exportar tiene un número muy alto de píxeles. En este caso, se debe aumentar el
número de píxeles máximo permitido para la exportación. Esto se logra indicando el argumento maxPixels dentro del diccionario que se pasa a Export.image.toDrive. Por ejemplo, maxPixels: 1e10, lo cual permite exportar una imagen con hasta 1 x 10^10 píxeles.

> GEE permite exportar información en formato vector, pero solo en formato CSV, GeoJSON, KML, KMZ, SHP o TFRecord. El primer formato es el predeterminado.

> Al igual que en la pestaña donde se organizan los repositorios y los códigos, en la pestaña de Assets se puede organizar la información en carpetas.

> El usuario puede compartir sus archivos con otros usuarios de GEE como lector o editor. Esta opción está disponible al darle clic en el símbolo de compartir a la derecha de cada archivo (aparece una vez que se coloca el puntero sobre el nombre del archivo).

> Para los diccionarios se recomienda utilizar la notación de (punto). Seguida del nombre de la clave, ya que es la recomendada por GEE.

> En algunos casos, los objetos que se obtienen a partir de ciertos métodos retornan un objeto de tipo indefinido (tipo objeto, ee.Object) como por ejemplo, al usar, en casos particulares, los métodos .first o .get, por lo cual se recomienda meter este objeto indefinido en un contenedor que indique el tipo de objeto del servidor. De no hacerlo, GEE mostrará un <p style="color:red; font-weight:bold;">Esta es una frase en rojo y en negrita dentro de un bloque de citas.</p>


# Secciones de la interfaz

**Assets**: Sección donde el usuario puede subir su información a GEE. 


# Indice de bibliotecas

**Map**: funciones encargadas del manejo de la pantalla del mapa.  
**ui.Chart**: funciones para realizar gráficos de diversos tipos.  
**Export**: exporta resultados fuera de GEE.  

# Indice de objetos:

**ee.Feature**: objetos de tipo vector.  
**ee.Image**: objetos de tipo imagen.  
**ee.FeatureCollection**: colecciones de vectores.  
**ee.ImageCollection**: colecciones de imágenes.  
**ee.Array**: arreglos.  
**ee.List**: listas.  



# Indice de métodos:

**.setOptions**: Para indicar los colores o el título a utilizar en el gráfico. Se utiliza sobre el objeto de tipo gráfico.

# Interaccion con la pantalla de mapa

Grupo de funciones que periten agregar objetos al lado del servidor a la pantalla de mapa, así como controlar algunos parámetros de dicha pantalla. La bilbioteca **Map** contiene todas las funciones encargadas del manejo de la pantalla de mapa.

## Map.addLayer

Permite mostrar elementos en la pantalla de mapa de la API. Permite mostrar objetos de tipo vector o imagen.

El siguiente es un código de ejemplo que muestra el uso de esta función sobre una imagen. Se muestran como argumentos las bandas, valores mínimos y máximos para visualizar la info y la opción de visualizar en RGB o modo monobanda.

```
Map.addLayer(image, {bands: ['B4', 'B3', 'B2'], min: 0,
                    max: 2000},
                    'RGB');
```

Para el caso de los vectores se puede realizar una operación similar a las imágenes, auqnue el único argumento válido es **color**.

```
Map.addLayer(feature, {color: 'FF0000'}, 'featuresColored')
```

## Map.centerObject

Permite centrar la pantalla de mapa en algún objeto o coordenada. También se puede indicar el zoom al que se quiere centrar.

```
Map.setCenter(39.86, 20.52, 5)
```

# Creación de gráficos

Todas las funciones que permiten realizar gráficos de diversos tipos se encuentran agrupadas en la bilbioteca **ui.Chart**.

## ui.Chart.feature

Permiten obtener distintos tipos de gráficos a partir de un vector o una colección de vectores y mostrar su resultado al imprimirlos en la consola. Algunas funciones disponibles son:

```
ui.Chart.feature.byFeature.
ui.Chart.feature.byProperty.
ui.Chart.feature.groups.
ui.Chart.feature.histogram.
```

Ejemplo: 

```
ui.Chart.feature.byProperty({
// Indicar el vector o colección de atributos a partir del cual se
// va a hacer el gráfico
feature: coleccionAtributos,
// La propiedad a colocar en el eje de las x
xProperties: 'Clase',
})
```

## ui.Chart.image

Permite obtener distintos tipos de gráficos a partir de una imagen o colección de imágenes. Algunas funciones diponibles son:

```
ui.Chart.image.byClass
ui.Chart.image.byRegion
ui.Chart.image.doySeries
ui.Chart.image.histogram
ui.Chart.image.seriesByRegion
```

Ejemplo: 

```
ui.Chart.image.seriesByRegion({
// Definir la colección de imágenes a graficar
imageCollection: coleccionImagenes,
// Definir las regiones sobre las cuales se va a extraer la
// información para el gráfico
regions: areas.filter(ee.Filter.eq('Tipo','Bosque')),
// El reductor a utilizar para resumir la información de la
// colección de imágenes en las regiones indicadas
reducer: ee.Reducer.mean(),
// La banda sobre la cual se quiere construir el gráfico
band: 'NDVI',
// Tamaño en m del píxel para obtener la información
scale: 30,
// Propiedad para nombrar a cada serie
seriesProperty: 'Tipo'
}).setOptions({
title: 'Bosque',
colors: ['#EE3A19']
});
```

# Exportación de objetos fuera de GEE

Sólo existen cuatro formatos válidos para exportar desde GEE: ráster (ee.Image), vector (ee.FeatureCollection), mapa y video.

## Export.image

Exporta imágenes (ráster, ee.Image). Hay tres opciones para exportar los resultados de una imagen:

**Export.image.toAsset**: Exporta la imagen (ráster) a la sección de Assets. Útil cuando resultado se va a utilizar enotro procedimiento de GEE.  
**Export.image.toDrive**: Exporta a Google Drive. Útil para trabajar con las imágenes (rásters) en un entorno local, como algún SIG. Esta es la opción más común y se recomienda utilizar distintas carpetas para organizarla y definir su nombre en el argumento de folder.  
**Export.image.toCloudStorage**: Exporta la imagen a Google Cloud Storage para utilizarla en algún otro proceso a realizar en Google Cloud Platform.  

Ejemplo: 

```
Export.image.toDrive({
// Definir la imagen a exportar
image: imgDiff,
// Especificar el nombre con el cual se va a guardar la imagen en
// el Google Drive
description: 'DiferenciaNDVI_2016-2017',
// Determinar el tamaño del píxel en m de la imagen a exportar
scale: 30,
// Exportar la imagen en formato GeoTIFF
fileFormat: 'GeoTIFF',
// Definir la carpeta del Google Drive en la que se va a exportar
// la imagen
folder: 'DiferenciaNDVIL8'
});
```

## Export.table

El formato vector (ee.Feature) permite exportar información en forma de vectores y tablas que no contienen información geográfica (es decir, sin geometría). Para poder exportar en el formato vector, el objeto exportado debe estar en formato de colección de vectores (ee.FeatureCollection). Hay tres opciones para exportar objetos de tipo vector:

**Export.table.toAsset**  
**Export.table.toDrive**  
**Export.table.toCloudStorage**  

Ejemplo: 

```
Export.table.toDrive({
// Definir la colección de vectores a exportar
collection: feature,
// Especificar el nombre con el que se va a guardar el
// archivo en el Google Drive
description: 'nombreArchivo',
// Determinar la carpeta dentro de Google Drive donde
// se va a guardar el archivo
folder: 'mifolder',
// Exportar la información en formato csv (valores
// delimitados por comas)
fileFormat: 'CSV'
});
```

## Export.video

Permite exportar una colección de imágenes como video, ya sea al almacenamiento de la nube de Google o al Drive.

**Export.video.toCloudStorage**  
**Export.video.toDrive**

Ejemplo:

```
Export.video.toDrive({
// Definir la colección de imágenes a exportar
collection: coleccionImgs,
// Especificar el nombre con el que se va a guardar el video
// en el Google Drive
description: 'videoTimelapse',
// Definir la carpeta donde se va a exportar el video
folder: 'mifolder',
// Determinar el número de cuadros por segundo
framesPerSecond: 24
});
```

# Importación de información a GEE.

En la sección de Assets se pueden importar varios tipos de archivos a GEE para utilizarlos en la API: imágenes (GeoTIFF), archivos vector (shapefiles), archivos separados por comas (csv), colecciones de imágenes exportadas por el mismo GEE y carpetas.

Para importar cualquiera de estos tipos de archivos, debes seguir el paso a paso que indica el libro en esta sección.

El progreso en la subida del archivo a GEE se puede consultar en la pestaña de Tasks. Una vez terminado, se puede tener acceso al archivo dentro de la pestaña de Assets. Se sugiere presionar la tecla de refrescar en caso que no aparezca el archivo recién importado.

A continuación, algunas consideraciones para subir un nuevo archivo:

> Para los archivos ráster, solo hace falta seleccionar el archivo correspondiente y dar clic en Upload.

> Para los archivos tipo .shp es necesario subir los archivos auxiliares (dbf, prj, shx, cpg, fix, qix, sbn o shp.xml) con el mismo nombre. Para los archivos .zip es necesario que contengan los archivos auxiliares.

> Para los archivos .csv es necesario que exista una columna de longitud y otra de latitud, las coordenadas deben ser decimales y estar en EPSG:4326 (WGS 84). El nombre de esas columnas se debe indicar en Advanced options / X column - Y column (longitud, latitud). Además, se debe tener absoluta claridad del tipo de delimitador (ejemplo: coma, punto y coma, u otro) y este debe ser especificado en Advanced options / CSV delimited.

# Tipos de Objetos

La gran variedad de objetos que tiene GEE se pueden agrupar en dos grandes rubros: objetos del lado del cliente y objetos del servidor. Esto según el lado donde se va a llevar a cado el procedimiento deseado.

## Objetos del cliente y del servidor

Existen dos lados de la programación de la API de GEE: el del servidor y el del cliente o usuario. Un objeto puede ser convertido entre los dos tipos. 

Cliente: 'cadena'  
Servidor: ee.String('cadena')  

Cliente: 1 + 2  
Servidor: ee.Number(1).add(ee.Number(2))  

En la mayoría de los casos se va a utilizar la programación del lado del servidor, ya que es la que permite hacer todo el procesamiento en GEE. Por ejemplo, para el caso de las condiciones se sugiere utilizar en lugar de if y else, la función ee.Algorithms.If. Sin embargo, cabe aclarar que algunas funciones solo funcionan del lado del cliente. Por ejemplo, las funciones de la interfaz del usuario, utilizadas para exportar la
información a algún archivo ee.Export (ya sea un ráster, un vector o una tabla), agregar una capa a la pantalla del mapa Map.addLayer o crear gráficos, así como imprimir información en la consola print.

## Tipos de objetos del lado del cliente

**Cadenas de texto:** cualquier cadena de caracteres que se encuentren entre un par de comillas dobles ("") o sencillas ('').
**Números:** objetos numéricos que indican un valor, se utiliza el prunto decimal para números decimales.
**Listas:** objetos que contienen varias entradas que pueden ser numericas, cadenas de texto o incluso otras listas.  
**Diccionarios:** objetos que contienen claves (entradas) y valores asociados a estas claves (definiciones). 
**Funciones:** la sintaxis para definir funciones en GEE es la que se presenta a continuación:

```
// MaskIm es una nueva función, su argumento de entrada es objeto
var maskIm = function(objeto){
// Se define qué objeto es una imagen ráster y se guarda en la
// variable image
var image = ee.Image(objeto);
// Se selecciona solamente la banda de nombre 'pixel_qa' de
// la imagen image
var qaImage = image.select('pixel_qa');
// Se crea una nueva imagen binaria donde será 1 los
// píxeles con valor igual a 322, y 0 los demás
var clearData = qaImage.eq(322);
// Se crea una máscara con la imagen binaria, y se entrega la
// imagen original enmascarada
return image.updateMask(clearData);
};
```

Aunque las unciones son propiamente objetos del lado del cliente, deben contener únicamente métodos del lado del servidor para que funcionen apropiadamente al trabajar en GEE.

> Las funciones que se ejecuten sobre una colección de imágenes o vectores solo funcionan si regresan un objeto de tipo ee.Feature, ee.FeatureCollection, ee.Image o ee.ImageCollection, por lo cual, a veces, se deben realizar ciertas conversiones para evitar un error.

## Tipos de objetos del lado del servidor

Los objetos del lado del servidor tienen asociados una serie de métodos particulares por tipo de objeto, es decir, métodos distintos a los del lado del cliente.
Los objetos del lado del servidor cuentan todos con el prefijo ee. seguido del nombre del tipo de objeto con inicial mayúscula.