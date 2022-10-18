# CI

Estudio de microcontroladores

## Grupo 32

### Profesor teoría

toni.benedico@upc.edu
Omega 308

## Grupo 43

### Profesor lab

Manueh

## Cosas importantes

El PIC tiene que ir a 8MHz en la simulaciones de Proteus.

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

### Program Counter

El program counter del microcontrolador es de 21 bits, almacenándose este entre
tres registros distintos: PCU (Program Counter Up), PCH (Program Counter High)
y PCL (Program Counter Low). Además, solo el PCL es accesible para los
usuarios. Y tanto PCl como PCH sol de 8 bits, en cambio, PCU es de 5 bits al
ser el PC de 21 bits y no 24.

### Organización de la memoria

Al tener muchas instrucciones una limitación de 8 bits para poner la dirección
de los datos para acceder se hizo que la memoria se almacenara en bancos de 256
posiciones de memoria, de forma que a través de estos 8 bits de
direccionamiento y un registro para selecionar este banco. El modelo de
microcontrolador que usamos en CI específicamente tiene 7 bancos de memoria, 6
basados en Registros de Propósito General (GPR en inglés) y 1 de Registros de
Propósito Especial (SFR en inglés). Los primeros tienen una función de
almacenar datos dinámicos y los segundos de controlar las operaciones de las
funciones con los periféricos.

Esto provoca una limitación al solo tener un banco activo a cada momento, de
forma que si es necesario acceder a la información de otro banco hay que hacer
la modificación en el Registro de Selección de Banco (BSR en inglés).

Debido a esto, además de los bancos de memoria, tenemos la posibilidad de
acceder a cierta información usando el Banco de Acceso que consiste en los 96
bytes más bajos de los registros de propósito general y los 160 bytes más altos
de los registros de próposito especial. Estos registros no necesitan ningún
tipo de cambio en ningún registro para acceder a ellos, se permite siempre
acceder a esta información.

#### Selección del Banco de Acceso o los BSR

Para seleccionar si se usar el Banco de Acceso o los BSR en las instrucciones
su usa el parámetro `a`, de forma que en caso de que `a` sea 0 (`A`) el BSR es
ignorado y se usa el Banco de Acceso. En cambio, en caso contrario, (`BANKED`)
El BSR especifica el banco que se usa en la instrucción.

#### Parámetro `d`

Este parámetros se usa para marcar el destino del dato que estamos usando en la
instrucción, de forma que en caso de que este sea 1 (`F`) se indica que se va a
guardar en la posición de memoria marcada en la instrucción, en caso contrario,
es decir, que sea 0 (`W`) se indica que se va a guardar en el registro WREG.

De esta manera, la instrucción `addwf 0x20, F, A` añade el valor guardado en
WREG al que está almacenado en 0x20 del Banco de Acceso y lo guarda en esta
misma posición de memoria. La instrucción `subwf 0x30, F, BANKED` resta el
valor de WREG del que está guardado la posición 0x30 del banco indicado por BSR
y el resultado de esta operación se guarda en esa misma posición de memoria.
Finalmente, la instrucción `addwf 0x40, W, A` suma el contenido de WREG con el
almacenado en la posición 0x40 del Banco de Acceso y guarda el resultado en
WREG.

## Pipelining

Las instrucciones de PIC18 se dividen en dos fases: fetch y ejecución.

Al mismo tiempo se ejecuta la ejecución de una instrucción y la vez el fetch de
la siguiente.

Las instrucciones de salto al tener que rehacer el fetch de la siguiente
instrucción por el cambio de dirección de memoria tienen un ciclo de ejecución
de NOP.

## Direccionamiento

Hay 4 tipos de direccionamiento:

- Inherente: no necesita ningún argumento `sleep`, `reset`
- Literal: requiere un argumento explícito en el código de operación `movlw
  0x30    ; carga el valor 0x30 en WREG`
- Directo: Se especifica dirección de origen/destino `movwf 0x20 ; el valor
  0x20 es un registro en modo directo`
- Indirecto: Se accede a una dirección sin dar una dirección fija en la
  instrucción. Se usan registros FSR como punteros.

### Direccionamiento directo

Se decide a partir del bit `d` de la instrucción siguiendo lo explicado en el
apartado del parámetro `d`. Las instrucciones sin este parámetro tienen el
destino implícito en el código de la instrucción misma.

### Direccionamiento indirecto

Los Registros de Selección de Archivo (FSRx) son usados como punteros a
registros de datos. Los registros para un direccionamiento indirecto sol
implementados con los Operadores de Archivo Indirectos, que permiten la
manipulación automática del puntero autoincrementando, autodecrementando o
haciendo un offset con otro valor.

---

## De aquí para abajo todavía no está completo el temario

---

## I/O

1. ANSEL se usa para ver si se usa en digital (0) o analógico (0)
2. TRIS se selecciona en 1 si es input y 0 si es output
3. Si se lee por el pin se usa el registro PORTx, en caso de escribir se hace
por LATx

### Display de 7 segmentos

En caso de ser de ánodo común (solo una entrada de 5V) los pines que controlan
los segmentos deben conectarse a pin de INPUT que debe ponerse a 0V para
permitir el paso de la corriente desde el display y su encendido.

En el EasyPic al tener 4 displays de 7 segmentos para poder usarlos sin tener
uq eusar 32 bits se usan 12: 8 para el segmento y 4 para abrir el cátodo común
de los displays, de forma que a cada ciclo de renderizado del número solo sale
uno.

### Teclados

#### Membrana

Una membrana de plástico que aprieta un conductor con otro.

#### Capacitivo

Dos placas paralelas que al apretar la tecla la capacitancia de la tecla
cambia.

#### Efecto hall

La modificación del campo magnético al apretar se detecta como boltaje entre
las dos caras del cristal de la tecla.

#### Mecánico

Dos placas metálicas que se tocan completando el circuito eléctrico.



## Interrupciones



### Mecánismos de interrupción

A la hora de programar la rutina de interrupción hay que hacerlo de esta
manera:

```C
void interrupt RSIHigh() {
    ...
}
```

Cuando se llama a una RSI se tienen que guardar los registros `STATUS`, `WREG`
y `BSR`.

Cuando se produce una interrupción hay que asignarla a alta o baja prioridad,
por ejemplo: Timer a HIGHPrio y C A/D a LOWPrio. De esta manera hay que
identificar qué dispositivo ha generado la interrupción. Como se puede deducir,
en caso de que se esté tratando una interrupción de baja prioridad y se produza
una de alta prioridad esta se interrumpe y se ejecuta la alta.

Si la RSI tiene que ejecutar alguna cosa con salidas se guarda algún valor en
una variable global para saber qué debe hacerse.

### Tipos de interrupciones

- Desde el chip:
    - Serial Internal Module
    - Overflow de un timer
    - ADC
- Externos
    - Puertos de input
    - Línea IRQ
- Software
- Excepciones
    - Opcode Trap
    - Stack Overflow
    - Divide by Zero

### RCON e ICON

Leer de la documentación

## Timer

Permite realizar interrupciones periódicas. Permiten medir la amplitud y
duración de señales periódicas.

El PIC18... tiene 7 timers: desde el 0 al 6. Los 0, 1, 3 y 5 son de 16 bits;
los 2, 4 y 6 son de 8 bits.

Cuando acaba el tiempo del timer se genera una interrupción.

2, 4 y 6 usan el reloj del ciclo de instrucciones como su reloj mientrsa que el
resto pueden usar relojes externos.

0 está designado para actuar como core interrupt muentras que el resto están el
el grupo de periféricos.

0, 1, 3, 4 funcionan a partir del overflow. 2, 4 y 6 funcionan a partir de
latch con PRx, de forma que si el valor del contador es igual al del registro
PRx se lanza la interrupción.

### Timer0

Puede ser configurado como de 8 o 16 bits. Puede ser timer si funciona con el
oscilador interno o contador si se hace con uno externo.

---

En los timers de 16 bits hay que leer primero la parte baja y después la alta.
En caso de producirse una interrupción en medio de las lecturas, de forma que
el clock haya ido aumentando.Esto se arregla usando un buffer en el que se
guarda el valor del high.

### 2/4/6

Tienen clock Fcy (Fosc/4) y prescaler y postscaler que permite reducir todavía
más los ticks recibidos por el timer.
