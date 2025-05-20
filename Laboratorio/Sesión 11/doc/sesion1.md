# Sesión Laboratorio

## Apartado A
Escribir las rutinas necesarias para inicializar, realizar una lectura secuencial de los tres ejes y detectar la caída libre, mediante entrada/salida por polling. Se deben tener en cuenta los siguientes aspectos:
- Antes de escribir en el registro de control hay que consultar/esperar al bit de busy.
- Configurar con Free Fall activado e IRQ desactivado.
- Tras habilitar el acelerómetro (Device Enable), hay que esperar a que el acelerómetro esté listo (bit de Ready).
- Para leer la aceleración de un eje se escribe en el registro de control el número del eje que se desea leer y el bit Read Accel., y esperar a que se active Data Ready.

```c
#define REG_ESTADO 0x4F8
#define REG_CONTROL 0x4F9
#define REG_DATOS_MSB 0x4FA
#define REG_DATOS_LSB 0x4FB

int acel[3];
int freeFall;

// Prototipo de las funciones:
void inicializa();
void leeEjes();
void detectaCaidaLibre();

// Función principal:
void main() {

    inicializa();

    while(1) {
        leeEjes();
        detectaCaidaLibre();
    }

}

// Implementación de las funciones:
void inicializa() {
    // Esperar mientras el dispositivo esta ocupado
    while ((inb (REG_ESTADO) & 0x04) != 0);

    // COnfigurar registro de control
    unsigned char control = inb (REG_CONTROL);

    control = control | 0x04;
    control = control & 0xEF;
    control = control | 0x20;

    oubt (control, REG_CONTROL);

    while ((inb (REG_ESTADO) & 0x08) == 0);
}

void leeEjes() {
    for (int i = 0, i < 3, i++) {
        // Esperar mientras el dispositivo esta ocupado
        while ((inb (REG_ESTADO) & 0x04) != 0);

        unsigned char control = inb (REG_CONTROL);
        control = control & 0xFC | i;
        control = control | 0x04;

        oubt (control, REG_CONTROL);

        while ((inb(REG_ESTADO) & 0x02) == 0);

        acel[i] = (inb (REG_DATOS_MSB) << 8) | (inb (REG_DATOS_LSB));
    }

}

void detectaCaidaLibre() {
    freeFall = inb(REG_ESTADO) & 0x1;
}
```

## Apartado B
Realizar las mismas funciones que en el apartado anterior, pero habilitando y usando la interrupción del acelerómetro. Se debe de tener en cuenta que la interrupción del acelerómetro (con número de interrupción 0x1F) cada vez que se produce un cambio en el registro de estado (salvo señales Busy y Ready).

```c
#define REG_ESTADO 0x4F8
#define REG_CONTROL 0x4F9
#define REG_DATOS_MSB 0x4FA
#define REG_DATOS_LSB 0x4FB

int acel[3];
int freeFall;
int eje;

void inicializa();
void rutina_int();

void main() {
    // Si ocurre la interrupción con ese número y en ese dispositivo, se ejecuta rutina_int
    int a = request_irq (0x1F, rutina_int, "mi_dispositivo");

    if (a != 0) {
        printf("Error");
    } else {
        inicializa();

        estado_anterior_freefall = inb (REG_ESTADO) & 0x01;


        eje = 0;

        while ((inb (REG_ESTADO) & 0x04) != 0);

        unsigned char control = inb (REG_CONTROL);
        control = control & 0xFC | eje;
        control = control | 0x04;

        oubt (control, REG_CONTROL);
        

        enable_irq(0x1F);
    }
}

void inicializa() {
    // Esperar mientras el dispositivo esta ocupado
    while ((inb (REG_ESTADO) & 0x04) != 0);

    // COnfigurar registro de control
    unsigned char control = inb (REG_CONTROL);

    control = control | 0x04;
    control = control | 0x10; // Ahora tenemos que activar el BIT enable
    control = control | 0x20;

    oubt (control, REG_CONTROL);

    while ((inb (REG_ESTADO) & 0x08) == 0);
}

void rutina_int() {
    disable();

    unsigned char estado = inb (REG_ESTADO);

    if ((estado & 0x02) != 0) {
        acel[eje] = (inb (REG_DATOS_MSB) << 8) | (inb (REG_DATOS_LSB));
        eje++;
        if (eje == 3) {
            eje = 0;
        }

         unsigned char control = inb (REG_CONTROL);
        control = control & 0xFC | eje;
        control = control | 0x04;

        oubt (control, REG_CONTROL);

    }

    if ((estado & 0x01) != estado_anterior_freeFall) {
        freeFall = estado & 0x01;
        estado_anterior_freeFall = estado 0x01;
    }

    enable();

}


```