# Plantilla Interrupciones
Los ejercicios por interrupciones siempre siguen la estructura que se representa a continuación. Más documentado en `Tema 3 - Anexo.pdf`, carpeta "./Ejercicios Teoría/doc".
```c
void main () {
    int a;
    a = request_irq (num_IRQ, rutina_int, "/dev/midisp");
    
    if (a != 0) {
        printf ("Error"); // Lanzamos un error o cambiamos algo de código.
    } else {
        inicializacion_dispositivo();
        enable_irq (num_IRQ); // Habilitamos la interrupción específica
    }
}

void rutina_int () {
    disable();  // Evita ser interrumpido
    // ...      // Realizar la transferencia
    enable();   // Habilita nuevamente las interrupciones
}

void inicializacion_dispositivo() {
    // Configurar y habilitar interrupcion en el dispositivo.
}
```
`Nota`: el valor num_IRQ nos lo tiene que dar el enunciado.