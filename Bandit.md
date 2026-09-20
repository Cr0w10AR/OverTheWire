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

Para este reto debiamos leer el archivo cifrado en **ROT13**

### Detalles a considerar
Si divides el alfabeto a la mitad, obtienes dos bloques de 13 letras:

1. Primera mitad: A, B, C, D, E, F, G, H, I, J, K, L, M

2. Segunda mitad: N, O, P, Q, R, S, T, U, V, W, X, Y, Z

La regla de ROT13 es mover cada letra 13 lugares hacia adelante.

- Si tomas la A (posición 1) y sumas 13, llegas a la N (posición 14).

- Si tomas la N (posición 14) y sumas 13, llegas a la posición 27. Como el alfabeto termina en 26, "das la vuelta" y llegas nuevamente a la A (posición 1).

---

en este nivel usaremos el comando **"tr"** que nos permite traducir los caracteres, este comando recibe atraves de tuberias ("<" o "|" )

lo hice de la siguiente manera:

```bash
bandit11@bandit:~$ cat data.txt | tr 'A-Za-z' 'N-ZA-Mn-za-m'
```
1. El Conjunto 1 (Alfabeto normal):
Le decimos que busque todas las letras, de la A a la Z (mayúsculas) y de la a a la z (minúsculas).
Se escribe así: 'A-Za-z'

2. El Conjunto 2 (Alfabeto desplazado):
Le decimos que las primeras (A-M) se vuelven (N-Z), y las segundas (N-Z) se vuelven (A-M). Lo mismo para minúsculas.
Se escribe así: 'N-ZA-Mn-za-m'

Contraseña para Bandit 12: **GROozWPO8QyN0mGrjUkID0WCYkZiQxrN** 

## Bandit 12

Para conseguir la contraseña, el objetivo fue revertir un volcado hexadecimal y quitar múltiples capas de compresión ocultas. El proceso fue:

1. Reversión inicial: Usé **xxd -r** para convertir el texto hexadecimal (data.txt) de vuelta a un archivo binario funcional.

2. Ciclo de descompresión: Apliqué un bucle de tres comandos repetitivamente sobre cada nuevo archivo generado:

 - file: Para descubrir el formato real (gzip, bzip2 o tar).

 - mv: Para renombrar el archivo y agregarle la extensión obligatoria (.gz, .bz2 o .tar).

 - gzip -d / bzip2 -d / tar -xf: Para quitar la capa de compresión o desempaquetar.

3. Resultado: Repetí este proceso a través de ***8 capas distintas*** hasta que el comando file indicó finalmente texto ASCII. Al hacer cat sobre este último archivo, encontré la contraseña plana.

```bash
bandit12@bandit:/tmp/tmp.SAgZRjPmUT$ file data8
data8: ASCII text
bandit12@bandit:/tmp/tmp.SAgZRjPmUT$ cat data8
The password is qQYQiHOBPR8zR61qxYqX45quvihF2uzk
bandit12@bandit:/tmp/tmp.SAgZRjPmUT$ 
```

Contraseña para Bandit 13: **qQYQiHOBPR8zR61qxYqX45quvihF2uzk** 

## Bandit 13

para el siguiente reto debemos entender como funciona **SSH y Criptografia**:

Luego de entrar en bandit 13 nos encontramos con que habia una llave privada:
```bash
andit13@bandit:~$ cat sshkey.private 
-----BEGIN OPENSSH PRIVATE KEY-----
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
-----END OPENSSH PRIVATE KEY-----
```

el reto nos decia que para pasar al siguiente nivel NO HABIA CONTRASEÑA, por lo que debemos usar esta llave privada para pasar al novel 14.

1. Copie todo el texto de la llave privada y me deslogee de bandit13
2. cree un archivo en mi computodara llamada llave_bandit14.key y le di permisos con **"chmod 600 llave_bandit14.key"**
3. Utilice el siguiente comando para entrar a bandit14:
```bash
tomas@Cuervito:~$ ssh -i llave_bandit14.key bandit14@bandit.labs.overthewire.org -p 2220
```
>  Para decirle a SSH "no me pidas contraseña, usa este archivo de llave", debemos usar la bandera -i (de identity) seguida del nombre de tu archivo.

y con esto logramos entrar a ***Bandit14*** y leer el archivo con permisos de badnit 14

Contraseña de Bandit 14: **aaWecNkG4FhxJQxz07uiwzVP6bJiYS65**

## Bandit 14

El reto del nivel dice asi:
>  The password for the next level can be retrieved by submitting the password of the current level to port 30000 on localhost.

lo que utilice para este nivel fue **netcat**

como el servidor y el puerto estaban en modo escucha, lo que hice fue lo siguiente:

```bash
bandit14@bandit:~$ nc localhost 30000 
aaWecNkG4FhxJQxz07uiwzVP6bJiYS65
Correct!
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
```
con esto le dije a netcat que se conente a esta misma maquina y al puerto 30000, al presionar enter, le di la contraseña de bandit 14 lo que el servidor me responde con la contraseña de bandit 15 

Contraseña para bandit 15: **pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7**

## Bandit 15
A diferencia del nivel anterior, el puerto 30001 está protegido con encriptación SSL. Si intentamos usar nc (Netcat) para enviar la contraseña en texto plano, la comunicación fallará, ya que Netcat no está diseñado para realizar el "apretón de manos" (handshake) criptográfico que exige un servidor seguro.

Para interactuar con un servicio cifrado desde la terminal, es necesario utilizar una herramienta capaz de negociar certificados digitales y establecer un túnel seguro. La herramienta estándar en Linux para esto es **OpenSSL**, utilizando su módulo **s_client**.

```bash
bandit15@bandit:~$ openssl s_client -connect localhost:30001
Connecting to 127.0.0.1
CONNECTED(00000003)
Can't use SSL_get_servername
depth=0 CN=SnakeOil
verify error:num=18:self-signed certificate
verify return:1
depth=0 CN=SnakeOil
verify return:1
---
Certificate chain
 0 s:CN=SnakeOil
   i:CN=SnakeOil
   a:PKEY: RSA, 4096 (bit); sigalg: sha256WithRSAEncryption
   v:NotBefore: Jun 10 03:59:50 2024 GMT; NotAfter: Jun  8 03:59:50 2034 GMT
---
Server certificate
-----BEGIN CERTIFICATE-----
MIIFBzCCAu+gAwIBAgIUBLz7DBxA0IfojaL/WaJzE6Sbz7cwDQYJKoZIhvcNAQEL
BQAwEzERMA8GA1UEAwwIU25ha2VPaWwwHhcNMjQwNjEwMDM1OTUwWhcNMzQwNjA4
MDM1OTUwWjATMREwDwYDVQQDDAhTbmFrZU9pbDCCAiIwDQYJKoZIhvcNAQEBBQAD
ggIPADCCAgoCggIBANI+P5QXm9Bj21FIPsQqbqZRb5XmSZZJYaam7EIJ16Fxedf+
jXAv4d/FVqiEM4BuSNsNMeBMx2Gq0lAfN33h+RMTjRoMb8yBsZsC063MLfXCk4p+
09gtGP7BS6Iy5XdmfY/fPHvA3JDEScdlDDmd6Lsbdwhv93Q8M6POVO9sv4HuS4t/
jEjr+NhE+Bjr/wDbyg7GL71BP1WPZpQnRE4OzoSrt5+bZVLvODWUFwinB0fLaGRk
GmI0r5EUOUd7HpYyoIQbiNlePGfPpHRKnmdXTTEZEoxeWWAaM1VhPGqfrB/Pnca+
vAJX7iBOb3kHinmfVOScsG/YAUR94wSELeY+UlEWJaELVUntrJ5HeRDiTChiVQ++
wnnjNbepaW6shopybUF3XXfhIb4NvwLWpvoKFXVtcVjlOujF0snVvpE+MRT0wacy
tHtjZs7Ao7GYxDz6H8AdBLKJW67uQon37a4MI260ADFMS+2vEAbNSFP+f6ii5mrB
18cY64ZaF6oU8bjGK7BArDx56bRc3WFyuBIGWAFHEuB948BcshXY7baf5jjzPmgz
mq1zdRthQB31MOM2ii6vuTkheAvKfFf+llH4M9SnES4NSF2hj9NnHga9V08wfhYc
x0W6qu+S8HUdVF+V23yTvUNgz4Q+UoGs4sHSDEsIBFqNvInnpUmtNgcR2L5PAgMB
AAGjUzBRMB0GA1UdDgQWBBTPo8kfze4P9EgxNuyk7+xDGFtAYzAfBgNVHSMEGDAW
gBTPo8kfze4P9EgxNuyk7+xDGFtAYzAPBgNVHRMBAf8EBTADAQH/MA0GCSqGSIb3
DQEBCwUAA4ICAQAKHomtmcGqyiLnhziLe97Mq2+Sul5QgYVwfx/KYOXxv2T8ZmcR
Ae9XFhZT4jsAOUDK1OXx9aZgDGJHJLNEVTe9zWv1ONFfNxEBxQgP7hhmDBWdtj6d
taqEW/Jp06X+08BtnYK9NZsvDg2YRcvOHConeMjwvEL7tQK0m+GVyQfLYg6jnrhx
egH+abucTKxabFcWSE+Vk0uJYMqcbXvB4WNKz9vj4V5Hn7/DN4xIjFko+nREw6Oa
/AUFjNnO/FPjap+d68H1LdzMH3PSs+yjGid+6Zx9FCnt9qZydW13Miqg3nDnODXw
+Z682mQFjVlGPCA5ZOQbyMKY4tNazG2n8qy2famQT3+jF8Lb6a4NGbnpeWnLMkIu
jWLWIkA9MlbdNXuajiPNVyYIK9gdoBzbfaKwoOfSsLxEqlf8rio1GGcEV5Hlz5S2
txwI0xdW9MWeGWoiLbZSbRJH4TIBFFtoBG0LoEJi0C+UPwS8CDngJB4TyrZqEld3
rH87W+Et1t/Nepoc/Eoaux9PFp5VPXP+qwQGmhir/hv7OsgBhrkYuhkjxZ8+1uk7
tUWC/XM0mpLoxsq6vVl3AJaJe1ivdA9xLytsuG4iv02Juc593HXYR8yOpow0Eq2T
U5EyeuFg5RXYwAPi7ykw1PW7zAPL4MlonEVz+QXOSx6eyhimp1VZC11SCg==
-----END CERTIFICATE-----
subject=CN=SnakeOil
issuer=CN=SnakeOil
---
No client certificate CA names sent
Peer signing digest: SHA256
Peer signature type: rsa_pss_rsae_sha256
Negotiated TLS1.3 group: X25519MLKEM768
---
SSL handshake has read 3191 bytes and written 1613 bytes
Verification error: self-signed certificate
---
New, TLSv1.3, Cipher is TLS_AES_256_GCM_SHA384
Protocol: TLSv1.3
Server public key is 4096 bit
This TLS version forbids renegotiation.
Compression: NONE
Expansion: NONE
No ALPN negotiated
Early data was not sent
Verify return code: 18 (self-signed certificate)
---
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 24B69B4057598E097BDB529385D72DAE7C71298E21039F68E78EA17B299C628F
    Session-ID-ctx: 
    Resumption PSK: A4F6253F0DB91232E15F9FD547DF2D1108F134DEDCEA084704A40FAEC0392D85BEB4BFF856E979089FD42F50B5B2479F
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 300 (seconds)
    TLS session ticket:
    0000 - d3 36 31 7d b1 9c 35 85-7c 6b 2c a8 28 1c e7 d8   .61}..5.|k,.(...
    0010 - 50 05 c4 ab 53 11 ad b0-b3 ae c7 10 f0 a2 29 44   P...S.........)D
    0020 - f4 b8 9b 8a 15 6f 38 95-82 68 26 17 dc c9 b0 bb   .....o8..h&.....
    0030 - 9c 8b db 5a 0d d7 9d 16-fa 50 62 9f d7 9e c7 1d   ...Z.....Pb.....
    0040 - f3 f4 62 bd 89 49 8c 7c-0b 44 3d 70 30 7f f5 da   ..b..I.|.D=p0...
    0050 - 45 bf 15 d5 3f 37 25 0e-6f 64 fa 8e 32 2c d4 06   E...?7%.od..2,..
    0060 - 01 d7 2d 29 bc 25 83 31-23 32 89 32 df 8e 21 48   ..-).%.1#2.2..!H
    0070 - 65 d8 82 c4 00 a9 02 31-7e 4b 56 02 36 ad aa 0e   e......1~KV.6...
    0080 - 8f 85 90 65 bd 1b 4f 69-d3 2e 16 01 1b 38 19 b4   ...e..Oi.....8..
    0090 - af 05 dd 3b 44 3f 1c c6-b5 59 7d bb b2 cf 0a 2f   ...;D?...Y}..../
    00a0 - df 7d e6 af 95 c5 72 43-3a 35 40 c6 d5 71 ed f1   .}....rC:5@..q..
    00b0 - d6 4f e7 74 c3 2b b1 f1-b7 b3 ee a5 f6 e2 d1 28   .O.t.+.........(
    00c0 - 4a d7 d6 4f 74 d2 73 42-7f b0 d3 24 ff b6 bb cf   J..Ot.sB...$....
    00d0 - 2d b1 84 3a f2 6b 00 16-ea bd e4 f4 f1 a8 fd 77   -..:.k.........w

    Start Time: 1789932487
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
---
Post-Handshake New Session Ticket arrived:
SSL-Session:
    Protocol  : TLSv1.3
    Cipher    : TLS_AES_256_GCM_SHA384
    Session-ID: 4461A8727253EABE9BEB822C881C3CEEDF854704B01F75B14B432430DD186BD8
    Session-ID-ctx: 
    Resumption PSK: 84A35C884FC0B9A6E83CA465BBD4D6E283AE7C6173CE90D302452AA42A1FB81036F939F553D4AFECB2790BAFB2345592
    PSK identity: None
    PSK identity hint: None
    SRP username: None
    TLS session ticket lifetime hint: 300 (seconds)
    TLS session ticket:
    0000 - d3 36 31 7d b1 9c 35 85-7c 6b 2c a8 28 1c e7 d8   .61}..5.|k,.(...
    0010 - 93 c0 d4 cf b2 81 4b 38-0e 45 65 7c 23 09 7b aa   ......K8.Ee|#.{.
    0020 - 05 82 b8 b7 36 4b db 4d-9b 25 34 a3 21 ff 7a 8e   ....6K.M.%4.!.z.
    0030 - f8 0e 37 0c 1b f7 b8 6e-2f 95 e6 1b 77 f3 4b 67   ..7....n/...w.Kg
    0040 - ac 10 a3 e9 3e 44 54 c1-03 ca 84 02 d1 f8 ac a8   ....>DT.........
    0050 - ed 5b 26 1a 7a c5 93 6b-52 f2 ef c4 fb 06 6b 0c   .[&.z..kR.....k.
    0060 - 29 16 5a 76 c4 3a bc 69-c3 bf ba 30 82 b9 72 27   ).Zv.:.i...0..r'
    0070 - 81 3a 14 89 99 2d 4d d2-2c a3 5c f6 9b 21 95 94   .:...-M.,.\..!..
    0080 - 1c de 60 c1 a2 05 44 2b-18 4d 41 14 2c 0d af 27   ..`...D+.MA.,..'
    0090 - 52 d3 14 91 04 5b 3d dc-cd 5d b2 a3 d7 15 10 54   R....[=..].....T
    00a0 - c4 17 9c a7 f8 b7 98 53-7c 16 24 12 da c1 e9 a5   .......S|.$.....
    00b0 - da af 2e 6c 6c de cb 18-4f b5 83 90 6d 69 22 25   ...ll...O...mi"%
    00c0 - 37 14 98 1d 72 53 c1 04-f0 37 4e 15 5c 51 25 f6   7...rS...7N.\Q%.
    00d0 - d7 94 15 c0 a2 09 69 9f-3c 9d c4 f0 10 f5 72 d6   ......i.<.....r.

    Start Time: 1789932487
    Timeout   : 7200 (sec)
    Verify return code: 18 (self-signed certificate)
    Extended master secret: no
    Max Early Data: 0
---
read R BLOCK
pbLYuZtTg4MgaqfJx8jbA9gKKGqM68A7
Correct!
kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V

closed
bandit15@bandit:~$ 
```
Contraseña del nivel Bandit 16: **kS0Hf0u5HiXFwKMKFqXvPdOTNGGa0X8V**

## Bandit 16
