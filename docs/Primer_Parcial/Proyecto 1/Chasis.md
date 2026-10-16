# Proyecto Parte 1 — Chasis del carrito

## Qué debía lograr hoy

- [✅] Diseñar e imprimir en 3D la base del carrito
- [✅] Imprimir soportes para los motorreductores
- [✅] Montar motores, llantas, protoboard, portapilas e interruptor

## Qué usé

- Impresora 3D y filamento PLA (blanco para la base, verde para los soportes)
- Software de diseño 3D (Fusion 360 / Tinkercad)
- Software laminador (Cura / PrusaSlicer)
- 2 motorreductores amarillos tipo TT (1:48)
- 2 llantas de goma de 65 mm
- Tornillos y tuercas
- Cinta doble cara

## Qué hice y qué pasó (evidencia)

Diseñé una base plana con un recorte en forma de media luna en la parte delantera y dos ranuras laterales para que las llantas queden dentro del ancho del carrito. Los motores se fijan con soportes verdes impresos aparte, atornillados a la base.


| Pieza | Material | Cantidad | Función |
| ----- | -------- | -------- | ------- |
| Base principal | PLA blanco | 1 | Soporta toda la electrónica |
| Soporte de motor | PLA verde | 2 | Fija cada motorreductor a la base |
| Motorreductor TT | — | 2 | Tracción |
| Llanta 65 mm | Goma / plástico | 2 | Contacto con el piso |

*Distribución: los motores quedan al centro para que el peso se reparta entre las llantas; la batería va atrás para equilibrar el carrito.*

## Qué falló y cómo lo resolví

- **Síntoma:** Los motores se movían dentro de su soporte al arrancar.
- **Cómo lo encontré:** Al probar el carrito, una llanta se desalineaba y el carrito no iba derecho.
- **Solución:** Ajusté la tolerancia del soporte en el diseño y volví a imprimirlo para que el motor quedara a presión.

- **Síntoma:** El carrito se inclinaba porque solo tiene dos llantas.
- **Cómo lo encontré:** Al poner la batería se iba hacia atrás.
- **Solución:** Se planea agregar una rueda loca (caster) o un punto de apoyo.

## Qué aprendí

Aprendí que al diseñar para impresión 3D hay que dejar tolerancias, porque la pieza impresa no sale exactamente de la medida del diseño. También entendí que la posición del peso (batería, protoboard) cambia el equilibrio y la tracción del carrito, y que es mejor pensar dónde va cada componente desde el diseño y no al final.

## Siguiente paso

Agregar una rueda loca y un soporte fijo para el portapilas y el interruptor.