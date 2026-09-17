# Nivel Bandit
## Bandit0

tenia que usar un ssh para conectarme directame con la contraseña "bandit0"  

```bash
ssh bandit0@bandit.labs.overthewire.org -p 2220
```
luego al hacer ```ls``` dentro del directorio, habia un archivo "readme" donde contenia la contraseña

Contraseña para bandit 1: **6y2kwnwK6grgvwvpvLaa2T1cpFEKOhNR** (sin las comillas)

## Bandit1

Ahora en este nivel el archivo del directorio es un guin (-), como cat no puede leer simplemente con un ```cat -``` debemos usar el siguiente comando:

```bash
cat ./-
``` 

con "**./**"  decimos a bash "leé el archivo llamado guion que está guardado en la carpeta exacta donde estoy parado"

Contraseña para bandit 2: **PK8fYLZg2hnHSz83plBL1iEPKdD3QToB** 

## Bandit 2

Ahora el arhivo en el directorio se llama "**--spaces in this filename--**" 

lo que hice fue hacer lo mismo que el nivel anterior solo que agregando comillas:

```bash
cat ./"--spaces in this filename--"
```

Contraseña para bandit 3: **7ZZ2LFrykP2zEyvBl4m3clcL7tGYJPME**

## Bandit 3

Dentro del directorio hay una carpeta llamada "inhere", dentro no se puede ver nada con un "**ls**" pero si se puede ver asi:

```bash
ls -a
```
aqui encontramos un archivo oculto llamado "...Hiding-From-You", podemos leerlo asi:

```bash
cat ./...Hiding-From-You
```

Contraseña para bandit 4: **xzTXq1rDJQVVAzdv5cHq1TQytTWufAMq**

## Bandit 4

para este nivel me encontre varios archivos pero solo uno era legible, use el comando **find** de esta manera y me encontre los resultados:

```bash
~/inhere$ file ./-file*
./-file00: data
./-file01: data
./-file02: OpenPGP Secret Key
./-file03: data
./-file04: data
./-file05: data
./-file06: Non-ISO extended-ASCII text, with NEL line terminators
./-file07: ASCII text
./-file08: data
./-file09: data
```

el archivo que contiene la contraseña esta en "./-file07"

Contraseña para bandit 5: **6C7h9GD8M6ai5nr7wo1RonrzFjj9yIrG**

## Bandit 5
el desafio era buscar un archivo entre tantos directorios y archivos  con las siguientes caracteristas:

1. human-readable
2. 1033 bytes in size
3. not executable

Por ello utilice el siguiente comando y sale un archivo con su directorio:

```bash
bandit5@bandit:~$ find . -type f ! -executable -size 1033c
./inhere/maybehere07/.file2
```
- el **f** en el comando es para indicar que es un archivo, si no fuese un archivo podria ser **d** (directorios), **l** (enlaces simbolicos), etc.

Luego aplique cat a ese directorio.

Contraseña para bandit 6: **pXa26xhMWaC2SvDotA4r9EgZkulOeSBW**

## Bandit 6

el desafio es buscar el archivo que pertenece al usuario bandit 7:

> owned by user bandit7  
> owned by group bandit6  
> 33 bytes in size  

para esto, use el siguiente comando:

```bash
bandit6@bandit:~$ find / -user bandit7 -group bandit6 -size 33c 2>/dev/null
/var/lib/dpkg/info/bandit7.password
```

* 2>/dev/null, es el **AGUJERO NEGRO DE LINUX**, con esto hice que todos los errores no se muestren.
* 2: el 2 es la salida de errores (Standard Error o stderr), el 1 es la salida normal (los resultados exitosos)
* ">": con esto le decimos que el resultado se diriga a algun lado
* /dev/null: es un archivo especial del sistema que simplemente destruye cualquier cosa que le envíes.

con esto hice un cat a ese directorio del resultado que nos daba la contraseña.

Contraseña para bandit 7: **Bmnnvf82KzQlfxgAI2d1zYbr1u9pr3E3**

## Bandit 7

Para el siguiente nivel solo use el comando grep que me permite buscar palabras o cadenas de texto especificas dentro de un archivo. en este caso la palabra clave era "millionth" dentro de data.txt:

```bash
bandit7@bandit:~$ grep millionth data.txt 
millionth	VR1ljMayciFxbnUokuQmJFw6QC9VKtub
```
Contraseña para bandit 8: **VR1ljMayciFxbnUokuQmJFw6QC9VKtub**

## Bandit 8
para este nivel use 2 comandos, "**sort**" y "**uniq**", sort me permite ordenar alfabeticamente un archivo, y uniq me permite mostrar las lineas que NO estan repetidas o si estan duplicadas.


```bash
bandit8@bandit:~$ sort data.txt | uniq -u
```
con la "**|**" unificamos comandos para que se trabaje en conjunto para una determinada accion.

contraseña para bandit 9: **EjmOSvuAu7sGAHqHVcBDPirRe9T03kxl**

## Bandit 9

el desafio es buscar la contraseña dentro del archivo data.txt pero dentro hay cadenas de textos ilegibles, la contraseña esta luego de varios signos de igual (=), por ello use el siguiente comando:


```bash
bandit9@bandit:~$ strings data.txt | grep "=========="
```
- strings: me permite ver solo cadenas de textos legibles

Contraseña de Bandit 10: **B0s2khmbT9u0geKuOoVGW3JZKhndE3BG**

## Bandit 10

para este nivel use el comando "**Base64**", por que el texto dentro del archivo esta encodeado en base 64, entonces el comando es:

```bash
bandit10@bandit:~$ base64 -d data.txt 
```
Contraseña para Bandit 11: **pYfOY6HwUsDj5rL9UvyhU7MCmv8vN5Ro**

## Bandit 11
