# Links de interés: 

https://developers.google.com/earth-engine/apidocs : Link para revisar todas las funciones disponibles para la interfaz del usuario. Se indican los argumentos que acepta cada función, así como el tipo de objeto de la salida.

https://epsg.io/ : Link para consultar los codigos EPSG.


# Consideraciones de GEE:

> Google Earth Engine utiliza una versión anterior de JavaScript a ES6, de modo que para declarar variables no se utiliza la declaratoria const o let, solo es válida la declaratoria var.

> En algunas ocasiones, al intentar cargar objetos en la pantalla de mapa, que sean producto de procesamientos demandantes, la consola puede mostrar un error indicando que se superó el tiempo de espera o el límite de memoria disponible. En estos casos, se sugiere exportar el resultado en lugar de tratar de cargarlo en la pantalla de mapa y después visualizarlo de manera local en algún SIG o software similar (por ejemplo, QGIS).

> La proyección por defecto para mostrar la información en la pantalla de mapa corresponde a EPSG:3857. 

# Interaccion con la pantalla de mapa

Grupo de funciones que periten agregar objetos al lado del servidor a la pantalla de mapa, así como controlar algunos parámetros de dicha pantalla. La bilbioteca **Map** contiene todas las funciones encargadas del manejo de la pantalla de mapa.

## ee.Feature

Objetos de tipo vector.

## ee.Image

Objetos de tipo imagen.

## Map.addLayer

Permite mostrar elementos en la pantalla de mapa de la API. Permite mostrar objetos de tipo vector o imagen.

El siguiente es un código de ejemplo que muestra el uso de esta función sobre una imagen. Se muestran como argumentos las bandas, valores mínimos y máximos para visualizar la info y la opción de visualizar en RGB o modo monobanda.

```
Map.addLayer(image, {bands: ['B4', 'B3', 'B2'], min: 0,
                    max: 2000},
                    'RGB');
```