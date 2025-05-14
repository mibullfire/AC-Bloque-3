# Ejercicios Resueltos del Laboratoio

## Cositas
- %d o %i: Especifican un entero con signo.
- %u     : Especifican un entero sin signo.
- %p     : Direccion de un puntero u dato.
- %lld   : Especifica un entero largo (long long). [*]
- %llu   : Entero largo sin signo (unsigned long long) [*]
- %s     : Especifica que el parametro es un puntero a un arreglo de caracteres.
- %c     : Un caracter.
- %x     : Especifica un valor hexadecimal.
- %%     : Muestra un literal de porcentaje.
- %f     : Imprime un float o double.

## Ejercicio 4
Rellene el siguiente programa en C para que almacene en un vector (v2) los elementos 
pares de otro vector (v1) y, finalmente, muestre por pantalla el resultado. 

```c
#include <stdio.h> 
int main() { 

    int v1[10] = {3, 8, 2, 0, 18, 5, 9, 4, 10, 1}, v2[10], j = 0; 

    for (int i = 0; i < 10; i++) {
        if (v1[i] % 2 == 0) {
            v2[j++] = v1[i];
        }
    }

    printf("Elementos pares en v2: ");
    for (int i = 0; i < j; i++) {
        printf("%d ", v2[i]);
    }
 
    return 0; 
}
```

## Ejercicio 5
Realice un programa que pida por teclado un número entero sin signo y, posteriormente, 
lo muestre por pantalla utilizando varios modificadores de tipos sobre la función “printf”: %d (para 
mostrarlo como entero), %u (para mostrarlo como entero sin signo) y %x (para mostrarlo como 
hexadecimal). 

```c
#include <stdio.h>

int main() {

    unsigned int dato;
    scanf("%u", &dato);

    printf("Como entero: %d\nComo entero sin signo: %u\nComo hexadecimal: %X", dato, dato, dato);

    return 0;
}
```

## Ejercicio 6
Utilice los operadores de desplazamiento de bits que aporta C para, con el dato recibido 
por teclado del ejercicio anterior, mostrar por pantalla el resultado de desplazar dicho número dos 
bits a la izquierda y dos bits a la derecha. 

```c
#include <stdio.h>

int main() {
    unsigned int dato;
    unsigned int derecha;
    unsigned int izquierda;
    scanf("%u", &dato);

    derecha = dato << 2; // Desplazamiento de 2 bits.
    izquierda = dato >> 2;

    printf("A la derecha: %u\nA la izquierda: %u", derecha, izquierda);
}
```

## Ejercicio 7 
Realice un programa que lea de teclado un número entero sin signo, y pinte por pantalla este mismo entero, pero mostrando solo la parte baja de la palabra (16 bits menos significativos). 

```c
#include <stdio.h>

int main() {

    unsigned int dato; 
    scanf("%u", &dato);

    dato = dato & 0x0F; // Le aplicamos una mascara de 0000 1111, quedándonos con la parte baja

    printf("El resultado es: %x", dato);

    return 0;
}
```

## Ejercicio 8
Repita el ejercicio anterior pero, en este caso, para la parte alta. 

```c
#include <stdio.h>

int main() {

    unsigned int dato;
    scanf("%u", &dato);

    dato = dato & 0xF0;

    printf("El resultado es: %X", dato);

    return 0;
}
```

## Ejercicio 9
Combine el ejercicio 7 y el 8, de forma que el programa deberá mostrar en una línea la parte alta y en la siguiente la parte baja del número introducido desde teclado. 

```c
#include <stdio.h>

int main() {

    unsigned int dato;
    unsigned int dato2;
    scanf("%u", &dato);

    dato2 = dato & 0xF0;
    dato = dato & 0x0F;

    printf("Los resultados son:\n%X\n%X", dato, dato2);

    return 0;
}
```

## Ejercicio 10
Realice un programa que recoja de teclado un número entero sin signo de 8 bits 
(“unsigned char”, utilizando el identificador “%hhu” de la función ‘scanf’). Seguidamente, modificará 
el bit número 3 (cuarto bit menos significativo) a ‘1’ y mostrará el resultado por pantalla (utilizando 
la representación en hexadecimal “%x”). Nota: intente que el número introducido no tenga un ‘1’ en 
binario en la posición 3 para poder ver los resultados. Se recomienda probar con el número 4 que, 
tras la modificación, debería salir por pantalla 0xC (12 si lo pinta como número entero).

```c
#include <stdio.h>

int main() {

    unsigned char dato;
    scanf("%hhu", &dato);

    dato = dato | 0x8; // Aplicamos la máscara 0000 1000 con una OR

    printf("El resultado es: %X", dato);

    return 0;
}
```

## Ejercicio 11
Realice un programa que recoja de teclado un número entero sin signo de 8 bits 
(“unsigned char”, utilizando el identificador “%hhu” de la función ‘scanf’)., modificará el bit número 
5 (sexto bit menos significativo) a ‘0’ y mostrará el resultado por pantalla (utilizando la 
representación en hexadecimal “%x”). Nota: intente que el número introducido SÍ tenga un ‘1’ en 
binario en la posición 5 para poder ver los resultados. Se recomienda probar con el número 32 en 
decimal que, tras la modificación, debería salir por pantalla 0. 

```c
#include <stdio.h>

int main() {

    unsigned char dato;
    scanf("%hhu", &dato);

    dato = dato & 0xDF; // Aplicamos las máscara 1101 1111

    printf("El resultado es: %X", dato);

    return 0;
}
```

## Ejercicio 12
Realice un programa que recoja de teclado un número entero sin signo de 8 bits 
(“unsigned char”, utilizando el identificador “%hhu” de la función ‘scanf’) y que consulte el estado del 
bit número 2 (tercer bit menos significativo). Si dicho bit tiene el valor ‘0’ deberá ponerlo a ‘1’, y si 
tiene el valor ‘1’ deberá ponerlo a cero. Muestre el resultado por pantalla utilizando la representación 
hexadecimal. 

```c
#include <stdio.h>

int main() {

    unsigned char dato;
    scanf("%hhu", &dato);

    if ((dato & 0x04) == 0) {
        dato | 0x04;
    } else {
        dato & 0xFB;
    }

    printf("El resultado es: %X", dato);

    return 0;
}
```

## Ejercicio 13
Realice un programa que recoja continuamente valores enteros sin signo de 8 bits 
desde teclado hasta que el usuario introduzca uno cuyo bit 7 (octavo bit menos significativo) sea ‘1’. 
Para ello, programe un bucle “while” en el que continuamente realice llamadas a “scanf”, y que 
compruebe la condición en cada iteración. Si se cumple, saldrá del buble y finalizará el programa; si 
no es el caso, seguirá solicitando números al usuario. Nota: un número únicamente con el bit 7 a ‘1’ 
es el 128 (en decimal); por lo tanto, para que se cumpla la condición en un número de 8 bits sin signo, 
el valor debe ser superior o igual a 128. 

```c
#include <stdio.h>

int main() {

    unsigned int dato;
    scanf("%u", &dato);

    while (dato < 0x80) {
        scanf("%u", &dato);
    }

    printf("El dato cuyo bit 7 es 1, es: %X", dato);

    return 0;
}

```

## Ejercicio 14
Haciendo uso del VisualRISCV utilice el siguiente fragmento de código para añadir 2 dispositivos board con componentes básicos y realice los siguientes apartados:
```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
```

### Apartado B
Realice un programa que, al accionar el interruptor rojo, se enciendan todos los leds del panel2 y se apaguen al desactivar el interruptor.

```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0xFF

    bucle:
    lbu x10, 0(x1)
    andi x10, x10, 0x01

    bne x10, x0, encender
    sb x0, 1(x1)
    jal x0, bucle
    
    encender: 
    sb x2, 1(x1)
    jal x0, bucle
```
Mismo código en c:
```c
while(1){
    if (inb (dirPanel1) && 0x01 == 1) {
        outb (0xFF, dirPanel2);
    } else {
        outb (0x00, dirPanel2);
    }
}
```

### Apartado C
Modifique el código anterior para que se enciendan todos los leds del panel2 si el interruptor rojo está inactivo y el azul activo. Ante cualquier otra combinación, los leds deberán permanecer apagados.

```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0xFF

    bucle:
    lbu x10, 0(x1)
    
    andi x11, x10, 0x01
    andi x12, x10, 0x40
    
    beq x12, x0, bucle

    bne x11, x0, encender
    sb x0, 1(x1)
    jal x0, bucle
    
    encender: 
    sb x2, 1(x1)
    jal x0, bucle
```

### Aparatdo D
Modifique nuevamente el programa anterior para que se activen los leds si, además, el interruptor verde está desactivado (tenga en cuenta que es activo a nivel bajo).

```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0xFF

    bucle:
    lbu x10, 0(x1)
    
    andi x11, x10, 0x01
    andi x12, x10, 0x40
    andi x13, x10, 0x20
    
    beq x12, x0, apagar
    beq x13, x0, apagar

    bne x11, x0, encender
    sb x0, 1(x1)
    jal x0, bucle
    
    encender: 
    sb x2, 1(x1)
    jal x0, bucle
    
    apagar:
    sb x0, 1(x1)
    jal x0, bucle
```

### Apartado E
Modifique una vez más el código para que interruptor rojo solo actúe si el interruptor azul está activo. En caso de estar desactivado el interruptor azul, el interruptor rojo es ignorado.

`Nota`: Basicamente el planteamiento que he tomado en el apartado B. 

### Apartado F
Desarrolle un programa que, al activar el interruptor rojo, se enciendan solo los leds rojos del panel2 y que, al desactivarlo, se enciendan solo los leds verdes.
```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0xAA
    addi x3, x0, 0x55

    bucle:
    lbu x10, 0(x1)
    andi x10, x10, 0x01

    bne x10, x0, encenderRojos
    sb x3, 1(x1)
    jal x0, bucle
    
    encenderRojos:
    sb x2, 1(x1)
    jal x0, bucle
```

### Apartado G
Implemente un programa que, al activar el interruptor azul y luego desactivarlo, se enciendan las luces del panel2, pero que, al repetir el proceso sobre el interruptor azul, las luces se apaguen. Este comportamiento deberá repetirse constantemente.
```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0xFF

    bucle:
    
    lbu x10, 0(x1)
    andi x11, x10, 0x40

    bne x11, x0, espera
    
    jal x0, bucle
    
    espera:
    lbu x10, 0(x1)
    andi x10, x10, 0x40
    beq x10, x0, salida
    jal x0, espera
    
    salida:
    lbu x16, 1(x1)
    beq x16, x0, encender
    jal x0, apagar
    
    encender:
    sb x2, 1(x1)
    jal x0, bucle
    
    apagar:
    sb x0, 1(x1)
    jal x0, bucle
```

### Apartado H
Escriba un código que encienda el led del pin0 del panel2. A partir de ese momento, cada vez
que el interruptor rojo cambie de estado debe encenderse la luz inmediatamente a la
izquierda (pin1) y apagar la anterior. Este proceso se repetirá hasta encender el led del pin
7, momento en el que pasará nuevamente al pin 0 con el siguiente desplazamiento.

```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000
    addi x2, x0, 0x01
    
    bucle:
    
    lbu x10, 0(x1)
    andi x11, x10, 0x01

    bne x11, x0, espera
    
    jal x0, bucle
    
    espera:
    lbu x10, 0(x1)
    andi x10, x10, 0x01
    beq x10, x0, salida
    jal x0, espera
    
    salida:
    sb x2, 1(x1)
    li x4, 1
    sll x2, x2, x4
    jal x0, bucle
```

### Apartado I
Realice un programa que encienda el led azul al accionar interruptor azul y lo apague al desactivar el interruptor.

```assembly
.config
    fullaccess on
    proc multi
    adddev board "Panel1" 0x50000 {1,BS,gs,BL,rl,GL,0,RS}
    adddev board "Panel2" 0x50001 {RL,GL,RL,GL,RL,GL,RL,GL}
    
.text
    li x1, 0x50000    # Carga la dirección base del Panel1 en x1
    addi x2, x0, 0x40 # Carga el valor 0x40 en x2 para comparación
    
bucle:
    lbu x11, 0(x1)    # Carga un byte de la dirección almacenada en x1
    add x12, x0, x11  # Copia el valor de x11 en x12
    
    andi x12, x12, 0x40   # Aplica máscara 0x40 para verificar el bit 6
    
    beq x12, x2, encender # Si el bit 6 está activo, salta a 'encender'
    
    # Si el bit 6 no está activo, apagar
    add x13, x11, x0      # Copia el valor de x11 en x13
    addi x13, x13, 0      # Este comando es redundante
    
    andi x13, x11, 0xEF   # Aplica máscara 0xEF para apagar el bit 4
    sb x13, 0(x1)         # Guarda el resultado en la dirección de x1
    jal x0, bucle         # Salta al inicio del bucle
    
encender:
    lbu x11, 0(x1)        # Carga nuevamente un byte de la dirección en x1
    add x12, x0, x11      # Copia el valor de x11 en x12
    ori x13, x12, 0x10    # Activa el bit 4 usando OR con 0x10
    sb x13, 0(x1)         # Guarda el resultado en la dirección de x1
    jal x0, bucle         # Salta al inicio del bucle
```

Nota: me he vuelto loco y lo he tenido que comentar, usa las `ori` para guardar el dato y cambiar los registros en lugar de add, de lo contrario no va a funcionar correctamente y va a causar errores dificiles de corregir.