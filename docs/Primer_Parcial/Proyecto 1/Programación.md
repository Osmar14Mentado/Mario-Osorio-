# Proyecto Parte 1 — Programación del carrito

## Qué debía lograr hoy

- [✅] Programar el ESP32 para controlar dos motores DC a través del puente H TB6612FNG
- [✅] Hacer que el carrito avance, retroceda y gire a la izquierda y a la derecha
- [✅] Controlar la velocidad de los motores con PWM
- [✅] Manejar el carrito con un control de Xbox por Bluetooth

## Qué usé

- ESP32 DevKit (ESP32-WROOM-32, USB-C)
- Arduino IDE / PlatformIO con el núcleo ESP32 3.x
- Librería `BLEGamepadClient`
- Control de Xbox con Bluetooth
- Módulo puente H TB6612FNG
- Cable USB-C para cargar el programa

## Qué hice y qué pasó (evidencia)

Primero definí los pines del ESP32 conectados al puente H. Cada motor usa dos pines de dirección y uno de velocidad (PWM). Después hice una función para cada motor que recibe la dirección (1 adelante, -1 atrás, 0 alto) y, en el `loop`, leo el joystick izquierdo del control para decidir el movimiento. Los botones A, B y X cambian la velocidad.

| Señal del TB6612 | Pin del ESP32 | Nombre en el código | Función |
| ---------------- | ------------- | ------------------- | ------- |
| AIN1 | GPIO 17 | `nAlto` | Dirección motor izquierdo |
| AIN2 | GPIO 16 | `nBajo` | Dirección motor izquierdo |
| PWMA | GPIO 23 | `pwma` | Velocidad motor izquierdo |
| BIN1 | GPIO 19 | `rAlto` | Dirección motor derecho |
| BIN2 | GPIO 18 | `rBajo` | Dirección motor derecho |
| PWMB | GPIO 22 | `pwmb` | Velocidad motor derecho |
| STBY | 3.3 V | `STBY` | Driver siempre habilitado |

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
// Standby (debe estar en HIGH, o conectado a 3.3V)
#define STBY  21
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
  pinMode(STBY, OUTPUT);
  digitalWrite(STBY, HIGH);

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

*Código final cargado al ESP32. El joystick izquierdo controla la dirección y los botones A, B y X la velocidad.*

| Parte del código | Para qué sirve |
| ---------------- | -------------- |
| `motorIzq()` / `motorDer()` | Ponen la dirección y la velocidad de cada motor |
| `ledcAttachChannel()` | Configura el PWM a 1 kHz y 8 bits (0 a 255) |
| `ZONA = 0.4` | Zona muerta: el stick debe moverse más del 40 % para que el carrito reaccione |
| `ARRIBA_ES_POSITIVO` | Permite invertir el eje Y sin reescribir el código |
| `isConnected()` | Failsafe: si se pierde el control, el carrito se detiene |


*Carga del programa al ESP32 por el puerto USB-C.*

## Qué falló y cómo lo resolví

- **Síntoma:** Al compilar aparecían errores con las funciones de PWM `ledcSetup` y `ledcAttachPin`.
- **Cómo lo encontré:** El mensaje de error indicaba que esas funciones ya no existen en el núcleo ESP32 3.x.
- **Solución:** Usé `ledcAttachChannel(pin, frecuencia, resolución, canal)` y `ledcWrite(pin, valor)`.

- **Síntoma:** Al empujar el stick hacia arriba, el carrito iba hacia atrás.
- **Cómo lo encontré:** Probé el control y vi que el eje Y venía con el signo contrario al que esperaba.
- **Solución:** Agregué la constante `ARRIBA_ES_POSITIVO` para cambiar el sentido con solo poner `true` o `false`.

- **Síntoma:** El carrito se movía solo aunque no tocara el control.
- **Cómo lo encontré:** El joystick no regresa exactamente a 0 cuando se suelta.
- **Solución:** Agregué una zona muerta (`ZONA = 0.4`) para ignorar movimientos pequeños.

## Qué aprendí

Aprendí que para controlar un motor con un microcontrolador se necesitan pines de dirección y una señal PWM para la velocidad, y que el valor de PWM (0 a 255) indica qué porcentaje del tiempo le llega energía al motor. Aprendí a leer un control de Xbox desde el ESP32 y a convertir los valores del joystick en movimientos. También entendí la importancia de la zona muerta y del failsafe: un carrito que sigue avanzando cuando se pierde la conexión es peligroso.

## Siguiente paso

Usar el valor exacto del joystick para tener velocidad proporcional y poder girar mientras avanza.