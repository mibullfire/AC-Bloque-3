# Ejercicio 10

Para un procesador que posee un sistema de E/S separada, escriba el código C que realice las tareas indicadas en cada apartado para el siguiente registro:

![10](/images/ejercicio10.png)

a) Modifique C a ‘1’.

b) Modifique E a ‘0’. 

c) Consulte el valor de B. En caso de ser ‘0’, escriba un ‘1’ en G y, en caso de ser ‘1’, escriba un ‘0’ en F. 

d) Ponga G a ‘1’ si C vale ‘1’ y F vale ‘0’. 

e) Espere a que D valga ‘0’ y, tras ello, copie el contenido de C a E. 

f) Escriba el valor de la variable “unsigned char c” (que solo puede abarcar valores de 0 a 3), en A. 

`Nota`: se ha tomado la dirección como un unsigned char, para realizar máscaras como si fuera un número. En los dos últimos apartados, se ha tomado como si cada elemento fueran variables.

```c
#include <stdio.h>

int main() {

    #define dir;

    unsigned char c;

    // Modifique C a 1
    dir = dir | 0x10;

    // Modifique E '0'
    dir = dir & 0xFC;
    
    // Consulte el valor de B. En caso de ser ‘0’, escriba un ‘1’ en G y, en caso de ser ‘1’, escriba un ‘0’ en F. 
    if ((inb (dir) & 0x20) == 0) {
        outb (inb (dir) | 0x01, dir)
    } else {
        outb (inb (dir) & 0xFE, dir)
    }

    // Ponga G a ‘1’ si C vale ‘1’ y F vale ‘0’.
    if (((inb (dir) & 0x10) != 0) && ((inb (dir) & 0x02) == 0)) {
        outb (inb (dir) | 0x01, dir)
    }

    // Espere a que D valga ‘0’ y, tras ello, copie el contenido de C a E.
    while ((inb (dir) & 0x08) != 0)

    if (inb (dir) & 0x10 == 0) {
        outb (inb (dir) & 0xFB, dir);  
    } else {
        outb (inb (dir) | 0x04, dir);
    }

    // Escriba el valor de la variable “unsigned char c” (que solo puede abarcar valores de 0 a 3), en A.
    /*
    if (c >= 0 && c <= 3) {
        outb (inb (dir) )
    } else {
        printf("El valor c no está dentro del rango permitido");
    }
    */


    return 0;
}
```