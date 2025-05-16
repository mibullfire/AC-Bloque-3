# Ejercicio Propuesto para Clase
Disponemos de 10 sensores de termperatura idénticos {$0-9} conectados a un computador a través de la misma línea de interrupción (IRQ 0x7). Cada sensor posee un solo registro de 16 bits cuya descripción y comportamiento es el siguiente:

![Ejercicio Clase](/images/ejercicioclase.png) `Nota`: la imagen no está subida.

El registro de E/S de cada sensor está mapeado en puertos consecutivos desde la dirección 500 a 509.

a) Implemente una función en C utilizando E/S separada que, tras recibir por parámetros el número del sensor que va desde 0 a 9, y una cadena con el nombre del sensor, realice la configuración necesaria para actuar mediante interrupciones. Si produce un error devolverá 0, en caso contrario 1.

```c
int configura_sensor (unsigned char numSensor, char nombreSensor) {
    int a = request_irq (0x7, mi_rsi, "/dev/nombreSensor");

    if (a != 0) {
        printf ("Error\n");

        return 0;
    } else {
        out (in (500 + numSensor) & 0xEFFF, 500 + numSensor);
        enable_irq (0x7);

        return 1;
    }
}
```

b) ENUNCIADO

```c
void mi_rsi() {
    disable();

    for (int i = 0, i < 10, i++) {
        if (in (500 + i) & 0x0002 != 0) {
            valoresSensor[i] = (in (500 + i) & 0x07F8) >> 3;   
        }
        // Comprobación de si se ha activado por error:
        if (in (500 + i) & 0x4000 != 0) {
            valoresSensor[i] = -1;
        }

        // Importante pedir el siguiente dato una vez lea el dato que queremos, le tenemos que decir al dispositivo que se ponga a leer más datos:
        out (in (500 + i) & 0x7FFF, 500 + i);
    }
    enable();
}