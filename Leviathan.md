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


