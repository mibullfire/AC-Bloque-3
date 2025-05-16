# Plantilla Interrupciones

```c
void main () {
    int a;
    a = request_irq (num_IRQ, rutina_int, "/dev");
}

void rutina_int () {
    disable(); // Evita ser interrumpido
    // ...
    enable(); // Habilita nuevamente las interrupciones.
}
```