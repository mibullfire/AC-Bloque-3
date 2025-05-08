# Ejercicio 30
Se pretende implementar el sistema de protección de un procesador frente al aumento de la temperatura. Para ello, tendremos un periférico que se encarga de registrar las mediciones del sensor de temperatura interno al chip. El sistema de entrada se compone de un sensor de temperatura digital que es capaz de registrar y almacenar medidas (previa petición) en un registro de 8 bits (el sensor no registra temperaturas negativas).

Por otro lado, para proteger al procesador de este aumento de temperatura, tenemos acceso al registro que controla el divisor del reloj del sistema. De esta forma, si la temperatura sobrepasase ciertos límites, podríamos aumentar el valor de dicho registro e, inmediatamente, se reduciría la frecuencia de reloj del sistema. Obviamente, esto provoca una reducción en las prestaciones, pero disminuiría la disipación de calor del procesador.

### `SENSOR DE TEMPERATURA`

`[0xA0] Registro de estado` (bits en cursiva) y `control` (bits en negrita): 

![301](/images/ejercicio301.png)

- IRQ_EN: permite configurar el método de E/S. 0: por polling; 1: con interrupciones. 
- RST: permite dar un reset al sistema, si no está “READY”. 
- REQ: (=1) solicitud de dato al sensor de temperatura (control por polling). 
- BUSY: (=1) indica si está ocupado. Antes de configurar o enviar una orden al dispositivo, hay que comprobar que no esté ocupado. 
- ERROR: (=0) indica que todo va bien. 
- NEW: (=0) indica que se ha producido una nueva lectura y se podrá leer en el reg. de datos. 
- READY: (=1) indica que el dispositivo está funcionando. 

`[0xA1] Registro de datos`: Dato de 8 bits (sin signo) que codifica la temperatura leída.

El proceso de lectura en polling es: 
- Esperar si está “BUSY”.
- Realizar una petición de dato. 
- Esperar que nos avise de dato nuevo con “NEW”. 
- Leer dato del registro de datos. 

### `DIVISOR DE RELOJ DEL SISTEMA`
`[0xF0] Registro de estado` (bits en cursiva) y `control` (bits en negrita): 

![302](/images/ejercicio302.png)

- UP/DOWN: para indicar si aumentamos o disminuimos el divisor de reloj. Si queremos disminuir la frecuencia (para disminuir la temperatura) tendremos que aumentar el divisor (y viceversa). 0: aumentar; 1: disminuir. 
- MOD: indicarle que modifique el divisor. 
- PRIOR: (=1) hay una operación prioritaria ejecutándose y, mientras esté activa, no se puede modificar el divisor del reloj. 

El proceso de modificación en `polling` es: 
- Esperar a que no haya operación prioritaria ejecutándose (“PRIOR”).
- Indicar si va a ser una modificación de aumento o disminución del divisor (“UP/DOWN”) Activar bit “MOD”.

## Apartado A
Implementar la función de inicialización del sensor de temperatura. Deberá comprobar que está “READY” (si no, hará un “RESET” hasta que esté “READY”) e indicarle el modo de funcionamiento. Luego, comprobará si ha habido algún error en la inicialización y devolverá ese resultado (0: no error, 1: error). Esta función recibe como parámetro de entrada un entero que indica si la E/S será por interrupciones (1) o por polling (0).

```c
#include <stdio.h>

#define REG_ESTADO 0xA0;
#define REG_DATOS 0xA1;

int inicializar_sensor_temperatura(int modo) {
    // Comprobar que está “READY” (si no, hará un “RESET” hasta que esté “READY”):
    if (inb (REG_ESTADO) & 0x40 != 0) {
        outb (inb (REG_ESTADO) | 0x02, REG_ESTADO);
        while (inb (REG_ESTADO) & 0x40 != 0) 
    }

    // Indicarle el modo de funcionamiento:
    outb (inb (REG_ESTADO) | (modo & 0x01, REG_ESTADO));

    // Devolver si ha habido un error:
    return (inb (REG_ESTADO) & 0x10) >> 4;
}
```

## Apartado B
Implemente la función principal del sistema para controlarlo por polling. El sistema deberá: - 
- Iniciar el sensor de temperatura al inicio 
- Repetidamente → Leer la temperatura 
    - Si temperatura > 150º → aumentar el divisor de reloj. 
    - Si temperatura < 100º → disminuir el divisor de reloj. 

```c
#define REG_DIVISOR_CLK;
// ... a continuación del apartado A.

void main() {
    unsigned char temperatura;
    int dev:

    dev = inicializar_sensor_temperatura(0); // El modo 0 indica polling.

    if (dev != 0) {
        printf("Ha habido algún error");
    } else {

        while (1) {
            temperatura = leer_temperatura();

            if(temperatura > 150) {
                modificar_divisor_reloj(0);
            } else if (temperatura < 100) {
                modificar_divisor_reloj(1);
            }
        }
    }
}

unsigned char leer_temperatura() {
    /*
    Esperar si está “BUSY”.
    Realizar una petición de dato.
    Esperar que nos avise de dato nuevo con “NEW”.
    Leer dato del registro de datos.
    */

    while (inb (REG_ESTADO) & 0x08 != 0);

    outb (inb (REG_ESTADO) | 0x04, REG_ESTADO);

    while (inb (REG_ESTADO) & 0x20 != 0);

    return inb (REG_DATOS);


}

void modificar_divisor_reloj(int sentido) {
    /*
    Esperar a que no haya operación prioritaria ejecutándose (“PRIOR”).
    Indicar si va a ser una modificación de aumento o disminución del divisor (“UP/DOWN”) Activar bit “MOD”.
    */
    char estado_control;

    while (inb (REG_DIVISOR_CLK) & 0x40 !=0);

    estado_control = inb (REG_DIVISOR_CLK) & 0xFD;

    outb (estado_control | (sentido << 1), REG_DIVISOR_CLK);

    outb (inb (REG_DIVISOR_CLK) | 0x04, REG_DIVISOR_CLK);
}


```