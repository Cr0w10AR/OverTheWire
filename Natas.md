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

La aplicación web utiliza un esquema de enrutamiento dinámico mediante la URL (index.php?page=home). Al inspeccionar el comportamiento, se identificó que el backend toma el valor del parámetro GET page y lo pasa directamente a una función de inclusión de PHP (como include() o require()) para renderizar el contenido en la vista principal. Al no existir una validación de entrada, sanitización, ni un mapeo seguro de directorios permitidos, la función es vulnerable a la inyección de rutas absolutas del sistema operativo subyacente.

Se detectó un comentario en el código fuente que revelaba la ruta absoluta de la credencial objetivo (/etc/natas_webpass/natas8). Utilizando curl, se manipuló la petición GET sustituyendo el nombre de la página legítima por la ruta del archivo del sistema. El servidor web procesó la directiva incluyó el contenido del archivo de contraseñas de Linux en el flujo HTML de respuesta, exponiendo la flag en texto plano.

```bash
# Explotación de LFI manipulando el parámetro GET 'page'
curl -u natas7:[PASSWORD] "http://natas7.natas.labs.overthewire.org/index.php?page=/etc/natas_webpass/natas8"
```

Contraseña Natas 8:**ugXL95KQmUAJJj6bMezOlBNDyI9Imwkc**

## Natas 8

El código fuente expuesto reveló una variable $encodedSecret codificada de forma rígida (hardcoded) y una rutina de validación que ofusca la entrada del usuario antes de la comparación lógica. La vulnerabilidad radica en que la función utilizada (bin2hex(strrev(base64_encode($secret)))) no aplica un algoritmo de hashing unidireccional y criptográficamente seguro (como bcrypt o SHA-256), sino una secuencia de transformaciones de formato totalmente reversibles. Esta falla de diseño de "Seguridad por Oscuridad" permite a cualquier atacante desandar el camino y recuperar el texto original.

Se examinó la rutina de ofuscación de la aplicación web y se estructuró la cadena de comandos inversos en la terminal. Utilizando tuberías (pipes) con herramientas nativas de Linux (xxd, rev, base64), se transformó la variable estática desde su estado hexadecimal a texto, se invirtió la cadena de caracteres y finalmente se decodificó su base64. El secreto en texto plano resultante (oubWYf2kBq) fue inyectado posteriormente mediante una petición POST para eludir la validación del servidor y capturar la flag del nivel 9.

```bash
# 1. Ingeniería inversa del secreto ofuscado mediante CLI
echo "3d3d516343746d4d6d6c315669563362" | xxd -r -p | rev | base64 -d
# Resultado: oubWYf2kBq

# 2. Envío del secreto decodificado simulando el formulario web
curl -u natas8:[PASSWORD] --data "secret=oubWYf2kBq&submit=1" http://natas8.natas.labs.overthewire.org/
```

Contraseña para Natas 9: **UdxmI27dTaXmnd1rxKQTfws6jihTdcQ9**

# Natas 9

La revisión del código fuente evidenció el uso de la función passthru() de PHP para ejecutar una instrucción de búsqueda en la terminal del sistema subyacente (grep -i $key dictionary.txt). La aplicación asigna el valor del parámetro HTTP needle (capturado vía $_REQUEST) directamente a la variable $key. Al no implementar mecanismos de validación ni escape de comandos (como escapeshellcmd() o escapeshellarg()), el flujo de ejecución es vulnerable a la inyección de metacaracteres de control de bash (como el separador de comandos ;), lo que permite forzar al servidor a ejecutar instrucciones arbitrarias.

Se construyó una petición HTTP POST mediante curl, mapeando correctamente el parámetro esperado por la aplicación web (needle). Se inyectó un vector de ataque que cerraba el comando grep inicial y concatenaba una instrucción de lectura (cat /etc/natas_webpass/natas10). El servidor procesó la entrada, ejecutó los comandos de forma secuencial e imprimió el contenido del archivo de contraseñas de Linux directamente en la respuesta HTML, exponiendo la flag del siguiente nivel.

```bash
# Explotación de OSCI concatenando comandos en el parámetro 'needle'
curl -u natas9:[PASSWORD] -d "needle=; cat /etc/natas_webpass/natas10" http://natas9.natas.labs.overthewire.org/
```
Contraseña Natas 10: **EgjlkzB6E8LJyf2Obt4q7q4ewt5ZWSNv** 

## Natas 10

