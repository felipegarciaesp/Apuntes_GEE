# Links de interés: 

https://developers.google.com/earth-engine/apidocs : Revisar todas las funciones disponibles para la interfaz del usuario. Se indican los argumentos que acepta cada función, así como el tipo de objeto de la salida.

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

# Secciones de la interfaz

**Assets**: Ssección donde el usuario puede subir su información a GEE.


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