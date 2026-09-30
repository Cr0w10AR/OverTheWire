# Natas - Over The Wire

## Natas 0

La vulnerabilidad explotada es una Divulgación de Información (Information Disclosure) básica. Es una mala práctica común en desarrollo web dejar datos sensibles, como notas de depuración o credenciales temporales, dentro de etiquetas de comentarios HTML (<!-- -->). 
Dado que el servidor envía todo el documento HTML al navegador para renderizar la página, este código fuente es completamente visible y accesible para cualquier usuario que lo inspeccione.

Se utilizaron las herramientas para desarrolladores del navegador (Inspector) para examinar la estructura de la página. Al revisar el bloque 
```
<div id="content" >
```
se descubrió la contraseña del siguiente nivel expuesta directamente en un comentario:

```bash
<!--The password for natas1 is scfWG6qNEIdzqVyfRwEGXyNUfFZkZeQ7-->
```

Contraseña para Natas 1: **scfWG6qNEIdzqVyfRwEGXyNUfFZkZeQ7**

## Natas 1

El reto implementa una medida de seguridad superficial del lado del cliente utilizando el evento oncontextmenu en la etiqueta "< body >" para retornar false y mostrar una alerta, bloqueando así el uso del clic derecho del ratón. Este tipo de controles son completamente ineficaces, ya que todo el documento HTML ya ha sido transmitido y renderizado por el navegador local del usuario.
El código fuente siempre puede ser accedido mediante atajos de teclado (como F12 o Ctrl+Shift+I para abrir las herramientas de desarrollador) o desde el menú de opciones del navegador.   

Se utilizó el Inspector de las herramientas para desarrolladores del navegador, evadiendo la restricción de JavaScript sin ningún problema. Al examinar la estructura del DOM en el bloque 

```bash
<div id="content">
```

se volvió a identificar la contraseña del siguiente nivel ofuscada como un comentario HTML estándar:

Contraseña para Natas 2: **vsDOxoXyq3wckCP1ZmTZ71ngIA606odB**

## Natas 2

Al inspeccionar el código fuente HTML, se identificó que el único recurso de la página (pixel.png) se estaba cargando desde el directorio /files/. En servidores web mal configurados (por ejemplo, Apache sin la directiva Options -Indexes), si no existe un documento índice predeterminado (como index.html o index.php), el servidor responde generando automáticamente una página con el listado de todos los archivos contenidos en esa carpeta.
Esta vulnerabilidad permite descubrir información sensible o archivos de configuración que no están enlazados públicamente.

Se manipuló la URL en el navegador, eliminando el nombre de la imagen para forzar el acceso a la ruta padre (/files/). El servidor web procesó la petición y expuso el índice del directorio, revelando la existencia de un archivo de texto no protegido llamado users.txt. 
Al acceder directamente a este archivo, se recuperó la credencial en texto plano correspondiente al siguiente nivel.

```bash
# username:password
alice:BYNdCesZqW
bob:jw2ueICLvT
charlie:G5vCxkVV3m
natas3:K30JrSRHzjxq3paUQuwozY4MNvmNFyhI
eve:zo4mJWyNj2
mallory:9urtcpzBmH

```

Contraseña Natas 3:**K30JrSRHzjxq3paUQuwozY4MNvmNFyhI**

## Natas 3

El archivo robots.txt es un estándar utilizado para gestionar el tráfico de los crawlers de motores de búsqueda, indicándoles mediante la directiva Disallow qué áreas del sitio no deben ser indexadas. Sin embargo, este archivo es de acceso público. Un error común de seguridad por oscuridad (Security by Obscurity) es utilizar este archivo para ocultar paneles de administración o directorios sensibles, lo que irónicamente proporciona a los atacantes un mapa exacto de dónde buscar.

Se consultó el archivo /robots.txt en la raíz del servidor, revelando una directiva que prohibía la indexación del directorio /s3cr3t/. Al navegar manualmente a esa ruta, se constató que también sufría de la misma vulnerabilidad de Listado de Directorios (Directory Indexing) explotada en el nivel anterior. Esto permitió acceder al archivo de texto alojado en su interior y recuperar la contraseña del nivel 4.

```bash
natas4:JDrPnuZAKyl6MkiqQGFIddrqpvgOASth
```

Contraseña Natas 4: ****

## Natas 4

La aplicación web implementa una restricción de acceso verificando el origen de la petición HTTP mediante el encabezado Referer, exigiendo que el tráfico provenga exclusivamente de [http://natas5.natas.labs.overthewire.org/](http://natas5.natas.labs.overthewire.org/). Utilizar cabeceras controladas por el cliente como mecanismo de control de acceso (Broken Access Control) es una vulnerabilidad de diseño crítica, ya que cualquier dato enviado desde el navegador del usuario puede ser interceptado y falsificado de manera trivial.

Se manipuló la petición HTTP saliente (utilizando un proxy de interceptación o herramientas de línea de comandos como cURL) para inyectar un encabezado Referer falsificado con el valor requerido por el servidor. Al recibir la petición modificada, el backend confió en el origen falsificado, autorizó la transacción y reveló la credencial del siguiente nivel.

```bash
❯ curl -u natas4:JDrPnuZAKyl6MkiqQGFIddrqpvgOASth -H "Referer: http://natas5.natas.labs.overthewire.org/" http://natas4.natas.labs.overthewire.org/
```

Contraseña Natas 5: **e4z2Noy3oqwPJUWzJH0dseN67Cn1sy2M**

## Natas 5


La aplicación web determina la autorización del usuario evaluando exclusivamente el valor de la cookie loggedin. Este es un escenario típico de Broken Authentication y validación de entrada insuficiente. Al no implementar validación en el servidor (como tokens de sesión en bases de datos) ni firmas criptográficas para garantizar la integridad del dato (como HMAC o JWT), el servidor confía ciegamente en cualquier parámetro enviado por el cliente. Esto permite escalar privilegios alterando el valor en texto plano de la cookie.

Se inspeccionó el almacenamiento local mediante las herramientas de desarrollador, identificando la cookie loggedin con un valor booleano inicial de 0 (no autenticado). Se manipuló este vector de ataque modificando el valor a 1 (autenticado). Al retransmitir la petición HTTP con la cookie alterada, la aplicación web procesó el estado como válido, eludiendo la restricción de acceso y revelando la credencial del nivel 6.

```bash
╰─ curl -u natas5:e4z2Noy3oqwPJUWzJH0dseN67Cn1sy2M --cookie "loggedin=1" http://natas5.natas.labs.overthewire.org/

## Resultado 
Access granted. The password for natas6 is 7mhjtShJAcld2NYbKHEadnhEwRn2P8VT</div>
```

Contraseña Natas 6: **7mhjtShJAcld2NYbKHEadnhEwRn2P8VT**

## Natas 6

La aplicación web permite visualizar su código fuente en PHP (index-source.html), evidenciando el uso de la directiva include "includes/secret.inc" para importar una variable secreta. La validación del backend compara la entrada del usuario recibida por método POST ($_POST['secret']) con esta variable interna. El fallo de seguridad radica en que el servidor web no restringe el acceso directo al directorio /includes/, permitiendo la lectura del archivo .inc en texto plano para extraer credenciales codificadas en duro (hardcoded).

Se identificó la ruta del archivo incluido mediante el análisis de caja blanca (revisión de código fuente). Utilizando curl, se realizó una petición GET directa a /includes/secret.inc para extraer el valor de la variable de validación (FOEIUWGHFEEUHOFUOIU). Finalmente, se estructuró una petición POST inyectando este valor en los parámetros esperados por el formulario web (secret y submit), satisfaciendo la condición lógica y recuperando la flag.

```bash
# 1. Consultar el archivo expuesto para extraer la variable $secret
curl -u natas6:[PASSWORD] http://natas6.natas.labs.overthewire.org/includes/secret.inc
# Resultado: <? $secret = "FOEIUWGHFEEUHOFUOIU"; ?>

# 2. Enviar el formulario web vía POST con la estructura de variables correcta
curl -u natas6:[PASSWORD] --data "secret=FOEIUWGHFEEUHOFUOIU&submit=1" http://natas6.natas.labs.overthewire.org/

# 3. Extraer la credencial de la respuesta HTTP
# Access granted. The password for natas7 is B1szg95UcTnrzwnF3i3TzYHlyYh8iBV0
```

Contraseña Natas 7: **B1szg95UcTnrzwnF3i3TzYHlyYh8iBV0**

## Natas 7

