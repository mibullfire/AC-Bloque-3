# Ejercicio 13
Sea un dispositivo para el control de nivel de agua que consta de 4 bombas de agua y 4 sensores: sensor inteligente que cambia a 1 cuando está lleno y cambia a 0 cuando está vació. Cada sensor y cada bomba está instalado en un depósito, de forma que se pueden controlar 4 depósitos. El dispositivo es manejado a través de los siguientes registros: 

### `Registro de Control (puerto 0x30)`

![301](/images/ejercicio131.png)

- LEER_S*: Solicita la lectura (0) del sensor de agua indicado en los bits S1-S0.  
- S1-S0: Sensor de agua a leer.  
- B1-B0: Bomba a activar.  
- ACTIVAR_B: Activa (1) o desactiva (0) la bomba de agua indicada en los bits B1-B0.

### `Registro de Estado (puerto 0x31)`

![301](/images/ejercicio132.png)

- LEÍDO*: Se activa (0) cuando finaliza la petición de lectura. 
- NIVEL: Una vez finalizada la petición de lectura del sensor, indicará con 1 si el último estado fue lleno, y 0 si el último estado fue vacío. 

## Apartado A
Usando E/S por polling, realice en C una función que reciba como parámetro el depósito (posibles valores: 0, 1, 2, 3) del que se quiere leer el sensor y devuelva 1 si el último estado fue lleno y 0 en caso contrario. 

```c
unsigned char leerSensor(int sensor) {

    unsigned char res;

    unsigned char S0 = (sensor & 0x01) << 1;
    unsigned char S1 = (sensor & 0x02) << 2;
    
    outb (inb (REGISTRO_CONTROL) | S0, REGISTRO_CONTROL);
    outb (inb (REGISTRO_CONTROL) | S1, REGISTRO_CONTROL);

    while (inb (REGISTRO_CONTROL) & 0x01 != 0);

    res = (inb (REGISTRO_ESTADO) & 0x80) >> 7;

    outb (inb (REGISTRO_CONTROL) | 0x01, REGISTRO_CONTROL);
    outb (inb (REGISTRO_ESTADO) | 0x04, RESGISTRO_ESTADO);

    return res;

}
```

## Apartado B
Utilizando E/S por polling realice en C la función principal que verifique continuamente si algún depósito está vacío usando la función anterior. En dicho caso, debe activar la bomba de dicho depósito y esperar hasta que se detecte lleno. En el momento que se detecte lleno, debe apagar la bomba y continuar con el control del resto de depósitos. 

```c
#include <stdio.h>

#define REGISTRO_CONTROL 0x30;
#define REGISTRO_ESTADO = 0x31;

void main() {

    while (1) {
        int CNT = 0;

        while (CNT <= 3) {
            estado = leerSensor(CNT);

            if (estado == 0) {
                // Activamos la bomba según el sensor que ocupemos en el contador (CNT).
                unsigned char B0 = (CNT & 0x01) << 6;
                unsigned char B1 = (CNT & 0x02) << 7;
                
                outb (inb (REGISTRO_CONTROL) | B0, REGISTRO_CONTROL);
                outb (inb (REGISTRO_CONTROL) | B1, REGISTRO_CONTROL);

                // Activar bomba.
                outb (inb (REGISTRO_CONTROL) | 0x20, REGISTRO_CONTROL);

                // Esperar a que el tanque se llene.
                while (leerSensor(CNT) != 0)

                // Desactivar bomba.
                outb (inb (REGISTRO_CONTROL) & 0xEF, REGISTRO_CONTROL);
            }

            // Con esto nos aseguramos que el contador vuelva a empezar, y compruebe nuevamente el Sensor 0.
            if (CNT == 3) {
                CNT = 0;
            }
        }
    }
}