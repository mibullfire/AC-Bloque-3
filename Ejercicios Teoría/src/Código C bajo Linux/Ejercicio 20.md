# Ejercicio 20
### Ejercicio con Entradas y Salidas Separadas (Polling)
Sea un computador conectado a dos dispositivos, uno Emisor y otro Receptor. El computador recibirá del dispositivo Emisor una dirección y un dato, y el computador deberá reenviar el dato al registro del dispositivo Receptor cuya dirección coincide con la proporcionada por el Emisor. Por ejemplo, si recibe del Emisor la dirección 4, enviará el dato recibido al registro de datos 4 del Receptor. El protocolo de comunicación para transferir un dato es el siguiente:

![20](/images/ejercicio20.png)

Tenga en cuenta que el Receptor confirma la grabación del Dato estableciendo Pend=0 y Verify=1 simultáneamente. Implemente en el Computador un programa en C mediante E/S separada que retransmita continuamente los datos recibidos del emisor al receptor según el cronograma anterior.

```c

void main () {

    // Espera continua del programa
    while (1) {
        while (inb (100) & 0x04 == 0); // Espera activa con una máscara para el BIT listo

        // Creamos dos variables para la dirección y el dato, aplicando las máscaras y los desplazamientos correspondientes:
        unsigned char dir = (inb (100) & 0xE0) >> 5;
        unsigned char dato = ((inb (100) & 0x18) >> 1 ) | (inb (100) & 0x02);

        // Escribimos los datos en el receptor
        outb (dato | 0xF0, 200 + dir);
        outb (inb (208) & 0xDF, 208);

        // Espera activa al BIT Pend y al Verify: Pend==0 y Verify==1
        while (inb (208) & 0x42 != 2);

        outb (inb (100) & 0xFB, 100);
    }
}


// printf("Ibra cabrón");
```