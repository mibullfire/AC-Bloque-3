# Ejercicio 11
Suponga un dispositivo E/S, con un único puerto de entrada de 8 bits, con el bit más significativo (MSB) normalmente a 1. Cuando baja a 0, indica que ha llegado un nuevo dato por los 7 bits restantes. Al leerlos, automáticamente el MSB vuelve a 1. Escriba una función que espere un dato (de 8 bits) y lo devuelva en una variable char. 

```c
#include <stdio.h>

#define DISPOSITIVO = 0xA0;

unsigned char main() {
    unsigned char respuesta;

    while (inb (DISPOSITIVO) & 0x80 != 0);

    respuesta = inb (DISPOSITIVO);

    outb (inb (DISPOSITIVO) | 0x80, DISPOSITIVO);

    return respuesta;
}
```