# Ejercicio 12
Se quiere construir un sistema de comunicación serie síncrona entre dos dispositivos, usando dos líneas: 
R y D. Los datos se reciben bit a bit por la línea D, con un nuevo bit por cada flanco de bajada del reloj R, y 
comenzando por el más significativo. Si no se reciben datos, la línea de reloj permanece a 1. Por ejemplo, el dato 
11000100 (en binario): 

![12](/images/ejercicio12.png)

Esta información es mandada por el dispositivo origen, mientras que el dispositivo destino se encarga de 
controlar los valores del reloj e ir leyendo de la línea de datos cuando sea oportuno. El receptor, dispone de un 
controlador de E/S que recoge el valor de estas dos señales de manera constante y lo guarda en un registro de 
datos (al guardar un nuevo valor, sustituye el valor anterior del registro anteriormente mencionado), al que se 
puede acceder mediante una lectura a la dirección 0x1000. En este registro de 8 bits, el bit 0 se encuentra 
conectado a la señal de reloj (R) y el bit 1 a la señal de datos (D). 

Además, tiene un registro de control (accesible en la dirección 0x1001) mediante el que se controla la 
activación del dispositivo: El bit 0 habilita el dispositivo si está a uno, y el bit 1, habilita la generación de 
interrupciones). Si el dispositivo genera interrupciones, producirá una interrupción al procesador cuyo vector 
de interrupción es el 27). 

a) Diseñe un programa que inicialice el periférico para la adquisición de datos por E/S por polling. El 
programa ha de recoger 1024 bytes y después, desactivar el dispositivo. Recuerde que, por cada byte, 
tendrá que hacer 8 lecturas de la señal D (una por bit), esperando los flancos de bajada de la señal R. 
Además, tendrá que concatenar estos bits mediante desplazamientos para obtener un byte. Cada vez 
que forme un byte, deberá almacenarlo en un vector de 1024 posiciones (unsigned char v[1024];). 

b) Realice el apartado anterior usando interrupciones. Para ello, la interrupción se disparará ante un flanco 
de bajada de la señal R. Deberá hacer una única lectura de D y controlar el número de bits (de los 8 del 
byte) que lleva leídos, así como el total de 1024 bytes límite a leer. 

## Apartado A (Por Polling)

```c
#include <stdio.h>

#define REG_DATOS 0x1000;
#define REG_CONTROL 0x1001;
#define numIRQ 27;

// Nota: int set_irq es el valor que indica si hay interrupciones o no. Si está desactivado se hace por Polling

int main() {

    unsigned char v[1024];
    int CNT_byte = 0;

    inicializar_dispositivo(0);

    while (CNT_byte < 1024) {
        v[CNT_byte] = leer_byte();
        cont_byte++;
    }

    desactivar_dispositvo(0);

    return 0
}

// Implementación de funciones

int inicializar_dispositivo(int set_irq) {

    int fallo, dev = 0;

    if (set_irq == 0) {
        outb(inb (REG_CONTROL) | 0x01, REG_CONTROL);
    } else {
        fallo = request_irq (numIRQ, rutinaISR, "/dev/dispositivo");
        if(fallo) {
            printf("Error al establecer la interrupción"\n);
            dev = -1;
        } else {
            outb(inb (REG_CONTROL) | 0x03, REG_CONTROL);
            enable_irq(numIRQ);
        }
    }

    return dev;
}

unsigned char leer_byte() {

    int CNT_bit = 0;
    unsigned char_dato = 0;

    while (CNT_bit < 8) {
        // Se espera flanco de bajada de R
        while (inb (REG_DATO) & 0x02 != 0);
        dato = (dato << 1) | inb(REG_DATO) & 0x01;
        CNT_bit++;
        
        // Se espera al flanco de bajada de R
        while (inb (REG_DATO) & 0x02 == 0);
    }

    return dato;
}

void desactivar_dispositivo(int set_irq) {
    if (set_irq == 0){ 
        //Desactivo dispositivo 
        outb(inb (REG_CONTROL) & 0xFE, REG_CONTROL); 
    } else { 
        //Desactivo dispositivo, deshabilito interrupción  
        outb(inb (REG_CONTROL) & 0xFC, REG_CONTROL); 
        disable_irq(numIRQ); 
    } 
}
```

## Apartado B (Por Interrupciones)

```c
#include <stdio.h>

#define REG_DATOS 0x1000;
#define REG_CONTROL 0x1001;
#define numIRQ 27;

//Declaro variables globales que se usaran en la RTI 
unsigned char v[1024]; 
int character_leido=0; 
unsigned char dato=0; 
int cont_bit=0; 
int cont_byte=0; 

int main() { 
    int ret; 
    ret = inicializar_dispositivo(1); //E/S interrupción 
    if (ret != 0) { 
        printf (“No se pudo iniciar la operación\n”); 
    } 
    return 0; 
}

void rti () { 
    disable(); 
    dato = (dato << 1) | inb (REG_DATO) & 0x01; 
    cont_bit++; 

    if (cont_bit == 8){ 
        v[cont_byte] = dato; 
        cont_bit = 0; 
        dato = 0; 
        cont_byte++; 

        if(cont_byte==1024) { 
        desactivar_dispositivo(1); 
        } 
    } 
    enable(); 
}
```