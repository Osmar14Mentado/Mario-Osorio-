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
- Driver de motores TB6612 (puente H) con dos motorreductores TT
- Portapilas 4AA con interruptor para los motores y power bank de 5 V para el ESP32
- Programa del carrito (código abajo)

## Qué hice y qué pasó (evidencia)

El ESP32 funciona como cliente BLE que se conecta al control de Xbox. Puse el control en modo de emparejamiento (manteniendo presionado el botón de sincronización) y el ESP32 se conecta automáticamente con `controller.begin()`. En cada vuelta del `loop` se leen el joystick y los botones. Los ejes del stick se comparan con una zona muerta de 0.4 para ignorar movimientos pequeños. Si el stick no pasa de esa zona, el carrito se queda detenido.

| Entrada del control | Acción del carrito |
| ------------------- | ------------------ |
| Stick izquierdo ⬆️ | Avanza |
| Stick izquierdo ⬇️ | Retrocede |
| Stick izquierdo ➡️ | Gira a la derecha sobre su eje |
| Stick izquierdo ⬅️ | Gira a la izquierda sobre su eje |
| Stick suelto | Se detiene |
| Botón A | Velocidad lenta (170) |
| Botón B | Velocidad media (210, valor inicial al encender) |
| Botón X | Velocidad rápida (255) |
| Control desconectado | Se detiene (failsafe) |

Adelante y atrás tienen prioridad sobre los giros: si el stick está inclinado en diagonal, el carrito avanza o retrocede en línea recta. Si el stick queda invertido en el control, se cambia `ARRIBA_ES_POSITIVO` a `false`.

### Código

```cpp
#include <Arduino.h>
#include <BLEGamepadClient.h>

// Motor izquierdo (canal A del TB6612)
#define nAlto 17   // AIN1
#define nBajo 16   // AIN2
#define pwma  23   // PWMA
// Motor derecho (canal B del TB6612)
#define rAlto 19   // BIN1
#define rBajo 18   // BIN2
#define pwmb  22   // PWMB
// =====================================================

const bool ARRIBA_ES_POSITIVO = true;

// ----- Velocidades (botones A, B y X) -----
const int velA = 170;   // botón A: lenta
const int velB = 210;   // botón B: media
const int velX = 255;   // botón X: rápida
int vel = velB;

const float ZONA = 0.4;

XboxController controller;

// dir: 1 = adelante, -1 = atrás, 0 = alto
void motorIzq(int dir) {
  digitalWrite(nAlto, dir == 1  ? HIGH : LOW);
  digitalWrite(nBajo, dir == -1 ? HIGH : LOW);
  ledcWrite(pwma, dir == 0 ? 0 : vel);
}

void motorDer(int dir) {
  digitalWrite(rAlto, dir == 1  ? HIGH : LOW);
  digitalWrite(rBajo, dir == -1 ? HIGH : LOW);
  ledcWrite(pwmb, dir == 0 ? 0 : vel);
}

void alto() {
  motorIzq(0);
  motorDer(0);
}

void setup() {
  pinMode(rAlto, OUTPUT);
  pinMode(rBajo, OUTPUT);
  pinMode(nAlto, OUTPUT);
  pinMode(nBajo, OUTPUT);

  // PWM a 1 kHz, 8 bits (core ESP32 3.x)
  ledcAttachChannel(pwma, 1000, 8, 0);
  ledcAttachChannel(pwmb, 1000, 8, 1);

  alto();               // arranca detenido
  controller.begin();
}

void loop() {
  int adelante = 0, atras = 0, dere = 0, izq = 0;

  // FAILSAFE: sin control conectado -> detener
  if (!controller.isConnected()) {
    alto();
    delay(20);
    return;
  }

  XboxControlsState s;
  controller.read(&s);

  float ejeY = s.leftStickY;
  float ejeX = s.leftStickX;

  // Stick arriba = adelante, stick abajo = atrás
  if (ARRIBA_ES_POSITIVO) {
    if (ejeY >  ZONA) adelante = 1;
    if (ejeY < -ZONA) atras = 1;
  } else {
    if (ejeY < -ZONA) adelante = 1;
    if (ejeY >  ZONA) atras = 1;
  }

  // Stick a la derecha / izquierda
  if (ejeX >  ZONA) dere = 1;
  if (ejeX < -ZONA) izq = 1;

  // Botones A, B y X: velocidad
  if (s.buttonA) vel = velA;
  if (s.buttonB) vel = velB;
  if (s.buttonX) vel = velX;

  // Movimiento
  if (adelante) {
    motorIzq(1);  motorDer(1);
  } else if (atras) {
    motorIzq(-1); motorDer(-1);
  } else if (dere) {          // izquierda avanza, derecha retrocede
    motorIzq(1);  motorDer(-1);
  } else if (izq) {           // derecha avanza, izquierda retrocede
    motorIzq(-1); motorDer(1);
  } else {
    alto();
  }

  delay(20);
}
```

## Qué falló y cómo lo resolví

- **Síntoma:** El control no se conectaba al ESP32.
- **Cómo lo encontré:** El botón Xbox del control parpadeaba sin quedarse fijo.
- **Solución:** Puse el control en modo de emparejamiento con el botón de sincronización justo después de encender el ESP32.

- **Síntoma:** Al perder la conexión, el carrito podía quedarse moviéndose.
- **Cómo lo encontré:** Lo pensé al revisar el código y lo comprobé apagando el control.
- **Solución:** Agregué un failsafe con `controller.isConnected()`: si no hay control, los motores se detienen.

## Qué aprendí

Aprendí que el ESP32 puede conectarse directamente a un control de videojuegos por Bluetooth de baja energía (BLE), sin necesidad de un celular. Entendí que el joystick no da valores de solo encendido o apagado, sino un rango de -1 a 1, y que hay que definir una zona muerta (0.4 en mi código) para evitar movimientos no deseados. También aprendí que un sistema a control remoto siempre debe tener un modo seguro cuando se pierde la señal.

## Siguiente paso

Hacer la electrónica para empezar las pruebas