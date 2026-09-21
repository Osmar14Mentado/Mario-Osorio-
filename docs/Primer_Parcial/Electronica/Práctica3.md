---
titulo: "Sesión 4 — conceptos básicos de electrónica"
fecha: 2026-09-11
autor: "Osorio Mario"
equipo: "Osorio Mario y Muñoz Mario"
estado: borrador   # completa | completa
---

# Sesión 4 — Puente H: control de motores DC y servomotor
## Qué debía lograr hoy
- [✅] Simular el control de dos motores DC con un puente H (L293D)
- [✅] Controlar el sentido de giro de un motor DC (adelante, atrás, izquierda, derecha)
- [✅] Encender y controlar un servomotor con Arduino
## Qué usé
- Arduino UNO (simulado en Tinkercad)
- Módulo puente H L293D (simulado en Tinkercad)
- 2 motores DC (simulados en Tinkercad)
- 1 servomotor (simulado en Tinkercad)
- Batería de 9V (simulada en Tinkercad)
- Cables de conexión (simulados en Tinkercad)

## Qué hice y qué pasó (evidencia)

![Diagrama de puente H](../../recursos/imgs/puente-h.jpg)

*Diagrama de un puente H con cuatro transistores (Q1-Q4), usado para controlar el sentido de giro de un motor DC.*

![Circuito base para puente H](../../recursos/imgs/Captura%20de%20pantalla%202026-09-18%20020047.png)

*Montaje inicial con un transistor, resistencia y LED como punto de partida antes de armar el puente H completo.*

![Simulación de control de dos motores con puente H](../../recursos/imgs/Captura%20de%20pantalla%202026-09-18%20000816.png)

*Simulación en Tinkercad de un Arduino UNO controlando dos motores DC a través de un módulo puente H (L293D).*

![Simulación de control servomotor](../../recursos/imgs/ServoMotor.png)

*Esquema de conexión entre el Arduino UNO y el servomotor en Tinkercad, mostrando las líneas de alimentación y señal PWM.*

```CPP
#include <Servo.h>
Servo oliver_dame_de_baja;
void adelante(){
  digitalWrite(6, HIGH);
  digitalWrite(7, LOW);
  digitalWrite(2, HIGH);
  digitalWrite(3, LOW);
}
void atras (){
  digitalWrite(7, HIGH);
  digitalWrite(6, LOW);
  digitalWrite(3, HIGH);
  digitalWrite(2, LOW);
}
void der (){
  digitalWrite(6, HIGH);
  digitalWrite(7, LOW);
  digitalWrite(3, HIGH);
  digitalWrite(2, LOW);
}
void izq (){
  digitalWrite(7, HIGH);
  digitalWrite(6, LOW);
  digitalWrite(2, HIGH);
  digitalWrite(3, LOW);
}
void setup()
{
  //SERVO
  oliver_dame_de_baja.attach(9);

  //MOTOR
  pinMode(6, OUTPUT); //OUT1
  pinMode(7,OUTPUT); //OUT2
  pinMode(3, OUTPUT); //OUT1
  pinMode(2,OUTPUT); //OUT2

  digitalWrite(5, HIGH);
  digitalWrite(1, HIGH);
}

void loop(){
  oliver_dame_de_baja.write(0);
  delay(1000);
  oliver_dame_de_baja.write(90);
  delay(1000);
  oliver_dame_de_baja.write(180);
  delay(1000);
  adelante();
  delay(1000);
  atras();
  delay(1000);
  der();
  delay(1000);
  izq();
  delay(1000);
}
```
*Código en Arduino que controla dos motores DC mediante el puente H (adelante, atrás, derecha, izquierda) junto con el movimiento de un servomotor.*

## Qué falló y cómo lo resolví
- **Síntoma:** En esta práctica no falló nada, pero sorprendió lo sencillo que resulta controlar la dirección de un motor DC combinando señales HIGH/LOW en el puente H.
- **Cómo lo encontré:** También sorprendió lo versátiles que son este tipo de motores, ya que con muy pocas líneas de código se logran distintos movimientos (adelante, atrás, giros) sin necesidad de un control complejo.
- **Solución:** No fue necesaria una solución, porque en esta práctica no falló nada.

## Qué aprendí
Antes no sabía qué era un motor DC ni cómo funcionaba. Aprendí que su sentido de giro depende de la polaridad con la que se alimenta, y que por eso el puente H invierte las terminales para hacerlo girar hacia adelante o hacia atrás. También entendí que la velocidad del motor está relacionada con el voltaje aplicado: a mayor voltaje, mayor velocidad de giro. En general, comprendí que son motores simples de controlar pero muy versátiles para distintos tipos de movimiento.
## Siguiente paso
Combinar el control de los motores DC con el servomotor para lograr un movimiento coordinado.