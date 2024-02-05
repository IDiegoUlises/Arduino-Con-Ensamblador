# Arduino Con Ensamblador

<img src="https://github.com/IDiegoUlises/Arduino-Con-Ensamblador/blob/main/imagenes/Comparacion-de-memoria.png" />

Que ventajas tiene escribir codigo en ensamblador las principales ventajas es que las instrucciones como digitalwrite se demoran mientras que con ensamblador se hace mucho mas rapido aumenta la velocidad de las instrucciones y ademas ocupa menos memoria el programa

* Se tienen que crear dos archivos con el mismo nombre pero con diferentes extensiones
* Un archivo con la extension led.ino que contiene el programa principal y el segundo led.S que contiene el codigo en ensamblador
* La extension de led.S es en mayusculas en caso que se escriba en minusculas no reconocera el archivo 

# Codigo principal(led.ino)
```c++
//Codigo en C para parpadeo de un led
extern "C"
{
  void start();
  void led(byte);
}

void setup()
{
  start();
}

void loop()
{
  led(1);
  led(0);
}
```

# Codigo en Ensamblador(led.S)
```c++
;---------------
;Codigo en Ensamblador
;---------------
#define __SFR_OFFSET 0x00
#include "avr/io.h"
;------------------------
.global start                 ;variables globales para ser llamadas desde la funcion principal                
.global led
;------------------------
start:
    SBI   DDRB, 4             ;configurar PB4(D12) como salida digital
    RET                       ;volver a la función setup()
;---------------------------------------------------------------------------
led:
    CPI   R24, 0x00           ;valor en R24 actual por el valor que llama en comparación con 0
    BREQ  ledOFF              ;saltar (branch) si es igual a la subrutina ledOFF
    SBI   PORTB, 4            ;establecer D12 en alto(HIGH)
    RCALL myDelay             ;llama a la funcion de retardo
    RET                       ;volver a la función loop()
;---------------------------------------------------------------------------
ledOFF:
    CBI   PORTB, 4            ;establece D12 en bajo
    RCALL myDelay             ;llama a la funcion de retardo
    RET                       ;volver a la función loop()
;---------------------------------------------------------------------------
.equ  delayVal, 10000         ;valor de recuento inicial para el bucle interno
;---------------------------------------------------------------------------
myDelay:
    LDI   R20, 100            ;valor de conteo inicial para el bucle externo
outerLoop:
    LDI   R30, lo8(delayVal)  ;byte low de delayVal en R30
    LDI   R31, hi8(delayVal)  ;byte high de delayVal en R31
innerLoop:
    SBIW  R30, 1              ;restar 1 del valor de 16 bits en R31, R30
    BRNE  innerLoop           ;saltar si countVal no es igual a 0
    ;--------------
    SUBI  R20, 1              ;restar 1 de R20
    BRNE  outerLoop           ;saltar si R20 no es igual a 0
    RET
;---------------------------------------------------------------------------
```

# Funcionamiento
![](https://github.com/IDiegoUlises/Arduino-Con-Ensamblador/blob/main/imagenes/Arduino-Con-Ensamblador.gif)
