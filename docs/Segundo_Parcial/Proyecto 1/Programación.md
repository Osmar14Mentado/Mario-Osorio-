# Proyecto Parte 1 — Programación del carrito

## Qué debía lograr hoy

- [✅] Programar el ESP32 para controlar dos motores DC a través del puente H TB6612FNG
- [✅] Hacer que el carrito avance, retroceda y gire a la izquierda y a la derecha
- [✅] Controlar la velocidad de los motores con PWM

## Qué usé

- ESP32 DevKit (ESP32-WROOM-32, USB-C)
- Arduino IDE / PlatformIO con el núcleo ESP32 3.x
- Módulo puente H TB6612FNG
- Cable USB-C para cargar el programa

## Qué hice y qué pasó (evidencia)

Primero definí los pines del ESP32 conectados al puente H. Cada motor usa dos pines de dirección y uno de velocidad (PWM). Después hice una función para cada motor que recibe la dirección (1 adelante, -1 atrás, 0 alto). En el `loop` el carrito ejecuta una secuencia de prueba: avanza, retrocede, gira a la derecha y a la izquierda, y al final repite el avance con tres velocidades distintas.

| Señal del TB6612 | Pin del ESP32 | Nombre en el código | Función |
| ---------------- | ------------- | ------------------- | ------- |
| AIN1 | GPIO 17 | `nAlto` | Dirección motor izquierdo |
| AIN2 | GPIO 16 | `nBajo` | Dirección motor izquierdo |
| PWMA | GPIO 23 | `pwma` | Velocidad motor izquierdo |
| BIN1 | GPIO 19 | `rAlto` | Dirección motor derecho |
| BIN2 | GPIO 18 | `rBajo` | Dirección motor derecho |
| PWMB | GPIO 22 | `pwmb` | Velocidad motor derecho |
| STBY | 3.3 V | — (no usa GPIO) | Driver siempre habilitado |

```cpp
#include <Arduino.h>

// Motor izquierdo (canal A del TB6612)
#define nAlto 17   // AIN1
#define nBajo 16   // AIN2
#define pwma  23   // PWMA
// Motor derecho (canal B del TB6612)
#define rAlto 19   // BIN1
#define rBajo 18   // BIN2
#define pwmb  22   // PWMB
// STBY va conectado directo a 3.3 V del ESP32 (no usa GPIO)
// =====================================================

// ----- Velocidades (PWM de 8 bits: 0 a 255) -----
const int velA = 170;   // lenta
const int velB = 210;   // media
const int velX = 255;   // rápida
int vel = velB;

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

void adelante()  { motorIzq(1);  motorDer(1);  }
void atras()     { motorIzq(-1); motorDer(-1); }
void derecha()   { motorIzq(1);  motorDer(-1); }  // izquierda avanza, derecha retrocede
void izquierda() { motorIzq(-1); motorDer(1);  }  // derecha avanza, izquierda retrocede

void setup() {
  pinMode(rAlto, OUTPUT);
  pinMode(rBajo, OUTPUT);
  pinMode(nAlto, OUTPUT);
  pinMode(nBajo, OUTPUT);

  // PWM a 1 kHz, 8 bits (core ESP32 3.x)
  ledcAttachChannel(pwma, 1000, 8, 0);
  ledcAttachChannel(pwmb, 1000, 8, 1);

  alto();               // arranca detenido
  delay(2000);          // tiempo para soltar el carrito
}

void loop() {
  // ----- Movimientos con velocidad media -----
  vel = velB;
  adelante();   delay(2000);
  alto();       delay(1000);
  atras();      delay(2000);
  alto();       delay(1000);
  derecha();    delay(1000);
  alto();       delay(1000);
  izquierda();  delay(1000);
  alto();       delay(2000);

  // ----- Prueba de velocidades avanzando -----
  vel = velA;
  adelante();   delay(2000);
  alto();       delay(1000);

  vel = velB;
  adelante();   delay(2000);
  alto();       delay(1000);

  vel = velX;
  adelante();   delay(2000);
  alto();       delay(3000);
}
```

*Código cargado al ESP32. El carrito repite la secuencia de movimientos y la prueba de velocidades.*

| Parte del código | Para qué sirve |
| ---------------- | -------------- |
| `motorIzq()` / `motorDer()` | Ponen la dirección y la velocidad de cada motor |
| `adelante()`, `atras()`, `derecha()`, `izquierda()` | Combinan los dos motores para cada movimiento |
| `ledcAttachChannel()` | Configura el PWM a 1 kHz y 8 bits (0 a 255) |
| `vel` | Valor de PWM que se aplica a ambos motores |
| `alto()` | Apaga los dos motores |


*Carga del programa al ESP32 por el puerto USB-C.*

## Qué falló y cómo lo resolví

- **Síntoma:** Al compilar aparecían errores con las funciones de PWM `ledcSetup` y `ledcAttachPin`.
- **Cómo lo encontré:** El mensaje de error indicaba que esas funciones ya no existen en el núcleo ESP32 3.x.
- **Solución:** Usé `ledcAttachChannel(pin, frecuencia, resolución, canal)` y `ledcWrite(pin, valor)`.

## Qué aprendí

Aprendí que para controlar un motor con un microcontrolador se necesitan pines de dirección y una señal PWM para la velocidad, y que el valor de PWM (0 a 255) indica qué porcentaje del tiempo le llega energía al motor. También aprendí que para girar sobre su propio eje el carrito necesita que un motor avance mientras el otro retrocede.

## Siguiente paso

Controlar el carrito con un control de Xbox por Bluetooth