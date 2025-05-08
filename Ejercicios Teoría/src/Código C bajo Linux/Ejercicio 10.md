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

    unsigned char dir; // No es un char, valor especulativo para hacer los cálculos, representa un registro
    unsigned char c;

    // Modifique C a 1
    dir = dir | 0x10;

    // Modifique E '0'
    dir = dir & 0xFC;
    
    // Consulte el valor de B. En caso de ser ‘0’, escriba un ‘1’ en G y, en caso de ser ‘1’, escriba un ‘0’ en F. 
    if ((dir & 0x20) == 0) {
        dir = dir | 0x01;
    } else {
        dir = dir & 0xFE;
    }

    // Ponga G a ‘1’ si C vale ‘1’ y F vale ‘0’.
    if (((dir & 0x10) != 0) && ((dir & 0x02) == 0)) {
        dir = dir | 0x01;
    }

    // Espere a que D valga ‘0’ y, tras ello, copie el contenido de C a E.
    while ((dir & 0x08) != 0)

    C = E;

    // Escriba el valor de la variable “unsigned char c” (que solo puede abarcar valores de 0 a 3), en A.
    if (c >= 0 && c <= 3) {
        A = c;
    } else {
        printf("El valor c no está dentro del rango permitido");
    }


    return 0;
}
```