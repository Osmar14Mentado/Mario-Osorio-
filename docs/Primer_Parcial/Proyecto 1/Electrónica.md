# Proyecto Parte 1 — Electrónica del carrito

## Qué debía lograr hoy

- [✅] Conectar el ESP32 con el puente H TB6612FNG
- [✅] Conectar los dos motores DC al puente H
- [✅] Alimentar el circuito con baterías y un interruptor de encendido

## Qué usé

- ESP32 DevKit (ESP32-WROOM-32)
- Módulo puente H TB6612FNG (rojo)
- 2 motorreductores DC tipo TT
- Portapilas de 4 pilas AA (6 V)
- Interruptor basculante (switch)
- Protoboard mini
- Cables de puente (jumpers)
- Multímetro

## Qué hice y qué pasó (evidencia)

El ESP32 manda las señales de control al puente H, y el puente H es el que entrega la corriente a los motores, ya que el ESP32 no puede alimentarlos directamente. La batería pasa primero por el interruptor para poder encender y apagar todo el carrito. El pin STBY del driver lo conecté directo a 3.3 V para que siempre esté habilitado.


| Desde | Hacia | Descripción |
| ----- | ----- | ----------- |
| Batería (+) | Interruptor | Encendido / apagado |
| Interruptor | VM del puente H y VIN del ESP32 | Alimentación de 6 V |
| Batería (−) | GND común | Tierra compartida por todo el circuito |
| ESP32 GPIO 17 | AIN1 | Dirección motor izquierdo |
| ESP32 GPIO 16 | AIN2 | Dirección motor izquierdo |
| ESP32 GPIO 23 | PWMA | Velocidad motor izquierdo |
| ESP32 GPIO 19 | BIN1 | Dirección motor derecho |
| ESP32 GPIO 18 | BIN2 | Dirección motor derecho |
| ESP32 GPIO 22 | PWMB | Velocidad motor derecho |
| ESP32 3.3 V | STBY | Driver siempre habilitado |
| ESP32 3.3 V | VCC del puente H | Alimentación lógica |
| AO1 / AO2 | Motor izquierdo | Salida de potencia |
| BO1 / BO2 | Motor derecho | Salida de potencia |

| Medición | Valor |
| -------- | ----- |
| Voltaje de la batería | ___ V |
| Voltaje en el motor (PWM 255) | ___ V |

## Qué falló y cómo lo resolví

- **Síntoma:** Los motores no giraban aunque el programa sí corría.
- **Cómo lo encontré:** Con el multímetro revisé que no llegaba voltaje a la salida del puente H.
- **Solución:** El pin STBY estaba al aire, y si no está en HIGH el driver queda apagado. Lo conecté directo a 3.3 V del ESP32.

- **Síntoma:** El ESP32 se reiniciaba al arrancar los motores.
- **Cómo lo encontré:** El LED del ESP32 se apagaba cada vez que los motores arrancaban.
- **Solución:** Revisé que todas las tierras (GND) estuvieran unidas y usé pilas cargadas.

## Qué aprendí

Aprendí por qué se necesita un puente H: el ESP32 solo da señales de baja corriente y un motor necesita mucha más, además de poder invertir el sentido de giro. Entendí que el TB6612FNG tiene un pin STBY que lo apaga si no recibe un nivel alto, y que conectarlo a 3.3 V ahorra un pin del ESP32. También aprendí la importancia de tener una tierra común entre la batería, el driver y el microcontrolador.

## Siguiente paso

Soldar las conexiones en una placa perforada para que no se suelten los cables con la vibración del carrito.