# Microservicio de Matrículas (`vg-ms-enrollment`)

## Información Básica
* **Puerto donde trabaja:** 5087
* **Tecnología:** Java 21 con Spring Boot WebFlux (Programación Reactiva)
* **Base de datos:** PostgreSQL en la nube (Neon)
* **Ubicación del código:** GitLab (`vg-ms-enrollment`)

---

## ¿Qué hace este microservicio?

Es el responsable de todo el ciclo de vida de la matrícula de un estudiante en el colegio. Administra desde el ingreso del alumno hasta su paso al siguiente año escolar.

### Tareas Principales:
1. Registrar la matrícula inicial de un estudiante.
2. Procesar la promoción masiva de alumnos (pasar a todo un salón al siguiente grado).
3. Registrar cambios de estado: retiro de alumnos, reservas de vacante o traslados.
4. Mantener el historial académico de matrículas del estudiante a lo largo de los años.

---

## ¿Con quién se relaciona?

Para cumplir con su trabajo, este microservicio necesita conversar con otros:

* **Con `vg-ms-user` (Usuarios):** Le pregunta si el estudiante y el apoderado existen y están registrados correctamente.
* **Con `vg-ms-academic` (Catálogo Académico):** Le pregunta qué grados, secciones y vacantes hay disponibles para el año lectivo.
* **Le da información a `vg-ms-attendance` (Asistencia):** Cuando el profesor va a tomar asistencia, Asistencia le pregunta a Matrículas cuáles son los alumnos oficialmente matriculados en ese salón.
* **Le da información a `vg-ms-grading` (Notas):** Para que un alumno tenga notas, debe estar registrado previamente en Matrículas.

---

## Reglas de Negocio que Siempre se Deben Cumplir

1. **Un solo registro activo:** Un alumno no puede tener dos matrículas activas en el mismo año escolar.
2. **Historial imborrable:** Ninguna matrícula se elimina de la base de datos. Si un alumno se retira, su estado cambia a `RETIRADO` y queda guardado quién hizo el cambio y cuándo.