# Links de interés: 

https://developers.google.com/earth-engine/apidocs : Revisar todas las funciones disponibles para la interfaz del usuario. Se indican los argumentos que acepta cada función, así como el tipo de objeto de la salida.

https://epsg.io/ : Consultar los codigos EPSG.

https://htmlcolorcodes.com/es/ : Consultar códigos hexadecimales de los colores, para poder pasarlos como argumentos.


# Consideraciones de GEE:

> Google Earth Engine utiliza una versión anterior de JavaScript a ES6, de modo que para declarar variables no se utiliza la declaratoria const o let, solo es válida la declaratoria var.

> En algunas ocasiones, al intentar cargar objetos en la pantalla de mapa, que sean producto de procesamientos demandantes, la consola puede mostrar un error indicando que se superó el tiempo de espera o el límite de memoria disponible. En estos casos, se sugiere exportar el resultado en lugar de tratar de cargarlo en la pantalla de mapa y después visualizarlo de manera local en algún SIG o software similar (por ejemplo, QGIS).

> La proyección por defecto para mostrar la información en la pantalla de mapa corresponde a EPSG:3857. 

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