# CI

Estudio de microcontroladores

## Grupo 32

### Profesor

toni.benedico@upc.edu
Omega 308

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


