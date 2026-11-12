# Práctica: Git-flow e Integración Continua

Ingeniería de Software, I-2026

---

## Objetivo

En esta actividad usted practicará un flujo básico de **Git-flow** usando un sistema pequeño.
La idea es simular el paso de una funcionalidad por las ramas:

```text
feature → develop → release → main
```

Además, al final deberá responder algunas preguntas directamente en este mismo archivo `README.md`.

**Duración estimada:** 20 a 30 minutos.

---

## Sistema base

El repositorio contiene un sistema pequeño para calcular descuentos.

Estructura esperada del proyecto:

```text
.
├── package.json
├── src
│   └── descuentos.js
├── test
│   └── descuentos.test.js
└── README.md
```

El archivo principal es:

```text
src/descuentos.js
```

La función principal recibe el precio de un producto y el tipo de cliente, y retorna el precio final con descuento.

Ejemplo:

```js
calcularPrecioFinal(10000, "normal")
```

---

## Pre-condición

Clonar el repositorio y entra a la carpeta del proyecto:

```bash
git clone URL_DEL_REPOSITORIO
cd NOMBRE_DEL_REPOSITORIO
```

Instala las dependencias:

```bash
npm install
```

Ejecuta las pruebas:

```bash
npm test
```

Antes de modificar el código, verifique que las pruebas iniciales pasen correctamente.

---

## Parte 1: preparar Git-flow

Primero, asegurar de estar en la rama principal:

```bash
git checkout main
```

Crear la rama `develop` a partir de `main`:

```bash
git checkout -b develop
git push -u origin develop
```

La rama `main` representa el código en producción.
La rama `develop` representa el código en desarrollo.

---

## Parte 2: crear una feature branch

Crea una rama para implementar una nueva funcionalidad:

```bash
git checkout -b feature/descuento-estudiante develop
```

Ahora implementa la siguiente funcionalidad:

> Si el tipo de cliente es `"estudiante"`, el sistema debe aplicar un descuento del 15%.

Ejemplo esperado:

```js
calcularPrecioFinal(10000, "estudiante")
```

Resultado esperado:

```js
8500
```

Debe modificar el archivo:

```text
src/descuentos.js
```

También debe agregar o completar una prueba en:

```text
test/descuentos.test.js
```

Ejecuta las pruebas:

```bash
npm test
```

Cuando todo funcione, realice un commit:

```bash
git add .
git commit -m "Agrega descuento para estudiantes"
```

---

## Parte 3: integrar la feature en develop

Vuelva a la rama `develop`:

```bash
git checkout develop
```

Integre la rama de funcionalidad usando `--no-ff`:

```bash
git merge --no-ff feature/descuento-estudiante -m "Integra feature descuento estudiante"
```

El parámetro `--no-ff` fuerza la creación de un commit de merge.
Esto permite dejar registrado que la funcionalidad fue desarrollada en una rama separada.

Elimine la rama local de la funcionalidad:

```bash
git branch -d feature/descuento-estudiante
```

Suba los cambios:

```bash
git push origin develop
```

---

## Parte 4: crear una release branch

Cree una rama de release desde `develop`:

```bash
git checkout -b release/1.0 develop
```

En esta rama, realiza una corrección menor en el archivo `README.md`.
Por ejemplo, agrega una breve descripción del sistema en la siguiente sección:

### Descripción del sistema

Escribe aquí una descripción breve del sistema:

> Respuesta del estudiante: Es un sistema simple que calcula el precio final de un producto según el tipo de cliente. Si el cliente es normal se mantiene el precio y si es estudiante se le aplica un 15% de descuento.

Luego realice un commit:

```bash
git add README.md
git commit -m "Actualiza documentación para release 1.0"
```

---

## Parte 5: publicar la release en main

Cambie a la rama `main`:

```bash
git checkout main
```

Integre la release:

```bash
git merge --no-ff release/1.0 -m "Publica release 1.0"
```

Cree una etiqueta para la versión:

```bash
git tag -a 1.0 -m "Versión 1.0"
```

Suba los cambios y la etiqueta:

```bash
git push origin main
git push origin 1.0
```

---

## Parte 6: devolver cambios a develop

Las correcciones hechas en la rama `release/1.0` también deben volver a `develop`.

```bash
git checkout develop
git merge --no-ff release/1.0 -m "Integra release 1.0 en develop"
```

Elimine la rama de release:

```bash
git branch -d release/1.0
```

Suba los cambios:

```bash
git push origin develop
```

---

## Parte 7: revisar el historial

Ejecute:

```bash
git log --oneline --graph --all
```

Observe el historial y verifique que aparezcan las ramas, los commits y los merges realizados durante la práctica.

---

## Preguntas

Responda las siguientes preguntas directamente en este archivo `README.md`.

### 1. ¿Cuál es la diferencia entre `main` y `develop` en Git-flow?

Respuesta: `main` tiene el código que está en producción, o sea las versiones que ya se publicaron y son estables. `develop` es donde se va juntando el trabajo que se está desarrollando, y desde ahí después se prepara la siguiente versión.

---

### 2. ¿Por qué la funcionalidad se desarrolló en una rama `feature` y no directamente en `main`?

Respuesta: Para no tocar el código de producción mientras se trabaja. Así la funcionalidad se puede hacer y probar aparte, y si algo sale mal `main` no se ve afectada. Además permite que varias personas trabajen en cosas distintas al mismo tiempo sin pisarse.

---

### 3. ¿Qué hace el comando `git merge --no-ff`?

Respuesta: Hace el merge pero siempre crea un commit de merge, aunque se pudiera hacer fast-forward. Así queda registrado en el historial que los cambios vinieron de otra rama.

---

### 4. ¿Qué diferencia habría si Git hiciera un merge con fast-forward?

Respuesta: Con fast-forward Git solo mueve el puntero de la rama al último commit de la feature, sin crear commit de merge. El historial queda lineal y no se nota que esos commits se hicieron en una rama aparte.

---

### 5. Después del merge, ¿los commits hechos en `feature/descuento-estudiante` quedan en el historial de `develop`?

Respuesta: Sí, los commits de la feature quedan en el historial de `develop` aunque se haya borrado la rama. Lo que se borra es solo el nombre de la rama, los commits siguen ahí y se ven conectados por el commit de merge.

---

### 6. ¿Qué rol cumple una rama `release`?

Respuesta: Sirve para preparar una versión antes de pasarla a producción. En esa rama se hacen ajustes finales, como corregir errores menores o actualizar la documentación, sin agregar funcionalidades nuevas. Después se integra en `main` y también vuelve a `develop`.

---

### 7. ¿Es posible usar Integración Continua con Git-flow? Justifica brevemente.

Respuesta: Sí. Se puede configurar para que las pruebas se ejecuten automáticamente cada vez que se hace push o un merge en ramas como `develop`, `release` o `main`. Así se detectan errores antes de integrar los cambios y se mantiene estable el código que llega a producción.

---

## Generar evidencia 

Ejecuta el siguiente comando en PowerShell (Windows) o Bash (Linux), dentro de la carpeta del repositorio:

Windows:
```powershell
powershell -ExecutionPolicy Bypass -File .\generar-evidencia.ps1
```
Linux:
```bash
chmod +x generar-evidencia.sh
./generar-evidencia.sh```
```

En campus virtual subir:

```text
1. Archivo evidencia_gitflow.txt.
2. README.md con las respuestas completas.
```
