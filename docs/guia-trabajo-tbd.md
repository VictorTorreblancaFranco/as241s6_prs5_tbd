# Guía de Trabajo: Cómo Desarrollar en EDUNOVA

## 1. ¿Cómo trabajamos? (Trunk Based Development)

En este proyecto utilizamos una forma de trabajo llamada **Trunk Based Development (TBD)**.

* Existe una sola rama principal llamada **`main`**.
* La rama `main` siempre debe estar funcional y lista para usarse.
* No acumulamos trabajo guardado durante semanas. Hacemos cambios pequeños, los probamos y los sumamos rápidamente a `main`.
* **No usamos la rama `develop`**. Todo va directamente a `main` a través de revisiones.

---

## 2. Pasos para Realizar un Cambio

Cuando se tenga que desarrollar una tarea, se sigue este camino:

### Paso 1: Actualizar equipo
Antes de empezar a programar, asegúrate de tener la versión más reciente del proyecto:
1. Ve a la rama principal: `git checkout main`
2. Descarga lo último: `git pull origin main`

### Paso 2: Crear una rama corta
Crea una rama temporal para tu trabajo. Su nombre debe seguir este formato sencillo:
`<tipo>/<servicio>-<resumen-de-tu-tarea>`

**Tipos permitidos:**
* **`feature`**: Si vas a crear algo nuevo (Ejemplo: `feature/enrollment-bulk-promotion`).
* **`fix`**: Si vas a reparar un error (Ejemplo: `fix/attendance-late-validation`).
* **`refactor`**: Si vas a mejorar código sin cambiar lo que hace (Ejemplo: `refactor/auth-token-validation`).
* **`docs`**: Si vas a cambiar documentación (Ejemplo: `docs/readme-update`).

### Paso 3: Guardar tus avances (Commits)
Cada vez que guardes un avance en Git, escribe un mensaje claro indicando qué hiciste:
`<tipo>(<modulo>): <explicacion de lo que hiciste>`

**Ejemplos sencillos:**
* `feat(enrollment): permitir promocionar alumnos en lote`
* `fix(attendance): corregir validacion de hora de llegada`
* `docs(user): actualizar lista de campos obligatorios`

### Paso 4: Actualizar tu rama antes de enviar (Rebase)
Antes de pedir que revisen tu trabajo, debes traer los últimos cambios de `main` a tu rama para no sobrescribir el trabajo de los compañeros:

1. `git checkout main`
2. `git pull origin main`
3. `git checkout tu-rama`
4. `git rebase main` (Esto pone tus cambios encima de lo más reciente).
5. `git push origin tu-rama --force-with-lease`

### Paso 5: Pedir revisión (Pull Request)
Abre un **Pull Request (PR)** en la plataforma. Los compañeros revisarán que el código funcione bien. Una vez aprobado, tu cambio se sumará a `main` y la rama temporal utilizada se eliminará.

---

## 3. Interruptores de Funcionalidad (Feature Flags)

¿Qué pasa si tu tarea es muy grande y no la terminas en un solo día?
Para no bloquear al equipo, subes tu código a `main` pero **desactivado por un interruptor en la configuración**.

Ejemplo en el archivo de configuración del microservicio:
```yaml
features:
  enrollment:
    nueva-promocion-activa: false # Está en el sistema, pero nadie lo ve hasta cambiar a true