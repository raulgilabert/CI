# CI

Estudio de microcontroladores

## Grupo 32

### Profesor teoría

toni.benedico@upc.edu
Omega 308

## Grupo 43

### Profesor lab

Manueh

## Introducción

### Microprocesador vs. Microcontrolador

Un microporcesador es una CPU en un solo chip. En cambio, un microcontrolador
contiene todo lo que un PC tendría en la placa base en un solo chip. Obviamente
en este caso todo es menos potente y con menos memoria por esta limitación de
espacio.

#### Aplicación de los microcontroladores

Control desde el interior de sistemas con mútiples entradas de sensores y con
necesidad de cálculo rápido. Por ejemplo el control de un drone.

### Memory mapped I/O

Diseñado por Motorola.

Las direcciones de memoria son un espacio lineal entre 0x00000 y 0xFFFFF. Por
tanto cualquier instrucción que lee o escribe en memoria puede leer o escribir
en I/O.

### Isolated I/O

Diseñado por Intel pero dejado de usar.

Direcciones distintas para memoria e I/O.


## Introdicción a la arquitectura PIC18

### Memoria del programa

Memoria no volátil implementada en una memoria flash.

Bus de direcciones de 31 bits. Instrucciones de 16 bits. 32 KB de un máximo de
2MB.

### Memoria de datos

Memoria volátil para datos durante el trabajo.

Bus de direcciones de 12 bits. Datos de 8 bits. 1536B de 4096B.

---

### Memorias dentro del chip

Contiene una memoria flash, una SRAM y una EEPROM.

En la posición de memoria de Reset Vector se pone una instrucción `GOTO` para
redirigir al inicio del programa.

### Instrucciones en la memoria de programa

La memoria está direccionada en bytes en grupos de 2 o 4. El LSB (Least
Significant Byte) de una palabra de instrucción está guardado en una posixión
de memoria con una dirección par.

Hay instrucciones que ocupan más de un word: `CALL`, `MOVFF`, `GOTO` Y `LFSR`.
En todos estos casos la segunda palabra de la instrucción siempre tiene 1111
como sus 4 bits más significantes, de forma que si se ejecuta solo esta segunda
parte de la instrucción se ejecutará un NOP.

### Organización de la memoria

Aun teniendo direcciones de 12 bits permitiendo almacenar 4096B, el fabricante
solo permite a través de las instrucciones solo se permite acceder a 256B con
estas. La memoria tiene 6 bancos de 256B como memoria de propósito general y
después 200B para SFR al final del todo de la memoria.

La unión de esta memoria accesible se llama Access Bank.

## I/O


