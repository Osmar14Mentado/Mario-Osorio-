---
titulo: "Sesión 1 — GitHub básico"
fecha: 2026-08-21
autor: "Osorio Mario"
equipo: "Osorio Mario y Muñoz Maria"
estado: borrador   # completa | completa
---
## Qué debía lograr hoy
- [✅ ] Crear un repositorio en Git-hub
- [✅ ] Crear un presentación
- [✅ ] Entender visual y comandos básicos de bash

## Qué usé
- Git-hub
- Visual Studio Code
- Git Bash


# Mi portafolio web
Hola, soy Mario Osorio y me encuentro cursando mi primer semestre de ingenieria mecatrónica. Estudio mecatrónica porque mi meta es diseñar perifericos para computadora en alguna empresa como razer, logitech o HyperX.
![Diagrama del sistema](../recursos/imgs/Image%20(3).jpeg)

Estoy cursando mi licenciatura en la Iberoamericana Puebla

![OXXO](../recursos/imgs/ibero.jpeg)

Me gusta mucho pokemon, conocer nuevas personas, hacer galletas y jugar videojuegos.

![Pokemon](../recursos/imgs/Image%20(1).jpeg)

```bash
git clone https://github.com/usuario/repositorio.git
```
Copia un proyecto de GitHub a tu computadora.
```bash
git status
```
Muestra qué archivos has modificado o agregado.
```bash
git add archivo.txt
git add .   
```
Agrega archivos para guardarlos en el próximo commit.
```bash
git commit -m "Descripción breve de los cambios"
```
Guarda tus cambios con un mensaje descriptivo.
```bash
git push origin main
```
Envía tus commits locales al repositorio en GitHub.
```bash
git add . 
git commit -m "cambio_que_hiciste"
git push 
```
Comandos básico para subir cambios directo a Git-hub

## Qué falló y cómo lo resolví
Síntoma: El repositorio de visual code no pasaba mis cambios a github.
Cómo lo encontré: Revisé el estado del repositorio con git status y con git remote -v para ver si estaba conectado al remoto correcto.
Solución: Agregué los cambios con git add ., hice el commit con git commit -m "mensaje" y los subí con git push origin. Después confirmé en GitHub que ya aparecían.

## Qué aprendí
Antes pensaba que guardar un archivo en Visual Studio Code era suficiente para que apareciera en GitHub. Ahora entiendo que guardar, hacer commit y hacer push son tres pasos distintos, y que si me salto alguno los cambios se quedan solo en mi computadora. También aprendí a usar git status para ver en qué punto estoy y git remote -v para confirmar que mi repositorio está conectado al lugar correcto. Cuando algo no se sube, lo primero es revisar el estado antes de intentar cosas al azar.
## Siguiente paso

Practicar el flujo completo (git add, git commit, git push) en un cambio nuevo y verificar en GitHub que aparezca.