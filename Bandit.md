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

