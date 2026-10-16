# Proyecto Parte 1 — Control remoto del carrito

## Qué debía lograr hoy

- [✅] Conectar un control de Xbox al ESP32 por Bluetooth
- [✅] Mover el carrito con el joystick izquierdo
- [✅] Cambiar la velocidad con los botones
- [✅] Detener el carrito si se pierde la conexión

## Qué usé

- ESP32 con Bluetooth integrado
- Control de Xbox con Bluetooth
- Librería `BLEGamepadClient`
- Programa del carrito

## Qué hice y qué pasó (evidencia)

El ESP32 funciona como receptor Bluetooth. Puse el control de Xbox en modo de emparejamiento (manteniendo presionado el botón de sincronización) y el ESP32 se conecta automáticamente con `controller.begin()`. En cada vuelta del `loop` se leen el joystick y los botones.

| Entrada del control | Acción del carrito |
| ------------------- | ------------------ |
| Stick izquierdo ⬆️ | Avanza |
| Stick izquierdo ⬇️ | Retrocede |
| Stick izquierdo ➡️ | Gira a la derecha sobre su eje |
| Stick izquierdo ⬅️ | Gira a la izquierda sobre su eje |
| Stick suelto | Se detiene |
| Botón A | Velocidad lenta (170) |
| Botón B | Velocidad media (210) |
| Botón X | Velocidad rápida (255) |
| Control desconectado | Se detiene (failsafe) |



## Qué falló y cómo lo resolví

- **Síntoma:** El control no se conectaba al ESP32.
- **Cómo lo encontré:** El botón Xbox del control parpadeaba sin quedarse fijo.
- **Solución:** Puse el control en modo de emparejamiento con el botón de sincronización justo después de encender el ESP32.

- **Síntoma:** Al perder la conexión, el carrito podía quedarse moviéndose.
- **Cómo lo encontré:** Lo pensé al revisar el código y lo comprobé apagando el control.
- **Solución:** Agregué un failsafe con `controller.isConnected()`: si no hay control, los motores se detienen.

## Qué aprendí

Aprendí que el ESP32 puede conectarse directamente a un control de videojuegos por Bluetooth de baja energía (BLE), sin necesidad de un celular. Entendí que el joystick no da valores de solo encendido o apagado, sino un rango de -1 a 1, y que hay que definir una zona muerta para evitar movimientos no deseados. También aprendí que un sistema a control remoto siempre debe tener un modo seguro cuando se pierde la señal.

## Siguiente paso

Usar el gatillo derecho para acelerar de forma proporcional y el stick para girar mientras avanza.