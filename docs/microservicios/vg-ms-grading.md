# Microservicio de Calificaciones (`vg-ms-grading`)

## Información Básica
* **Puerto donde trabaja:** 5092
* **Tecnología:** Java 21 con Spring Boot WebFlux (Programación Reactiva)
* **Base de datos:** PostgreSQL en la nube (Neon)
* **Ubicación del código:** GitLab (`vg-ms-grading`)

---

## ¿Qué hace este microservicio?

Es el responsable del sistema de calificaciones y evaluación académica de los estudiantes. Gestiona todo el proceso de registro de notas, cálculo de promedios y seguimiento del rendimiento académico durante el año escolar.

### Tareas Principales:
1. Registrar las calificaciones individuales de cada estudiante por actividad evaluativa.
2. Calcular promedios ponderados por periodo académico según el tipo de evaluación.
3. Mantener el registro histórico de todas las calificaciones ingresadas.
4. Generar estadísticas de rendimiento académico por estudiante, materia o sección.
5. Validar que las calificaciones cumplan con las escalas y rangos permitidos.

---

## ¿Con quién se relaciona?

Para cumplir con su trabajo, este microservicio necesita conversar con otros:

* **Con `vg-ms-eval-activities` (Actividades Evaluativas):** Le pregunta qué actividades de evaluación existen, sus configuraciones, puntajes máximos y fechas límite.
* **Con `vg-ms-eval-catalog` (Catálogo de Evaluaciones):** Consulta los periodos académicos activos, tipos de evaluación y las escalas de calificación que se deben aplicar.
* **Con `vg-ms-academic` (Catálogo Académico):** Valida que las materias y secciones existan antes de registrar calificaciones.
* **Con `vg-ms-enrollment` (Matrículas):** Verifica que el estudiante esté oficialmente matriculado en la materia antes de permitir el registro de una nota.
* **Con `vg-ms-user` (Usuarios):** Obtiene información de estudiantes y profesores al momento de registrar o consultar calificaciones.
* **Le da información a `vg-ms-eval-reportcards` (Boletas):** Proporciona todas las calificaciones y promedios necesarios para generar las boletas de notas oficiales en PDF.

---

## Reglas de Negocio que Siempre se Deben Cumplir

1. **Rango válido de calificaciones:** Toda calificación debe estar dentro del rango mínimo y máximo definido por la escala de calificación activa (ejemplo: 0-100 o 0-20 según el sistema).

2. **Estudiante matriculado:** Solo se puede registrar una calificación para un estudiante que esté oficialmente matriculado en la materia correspondiente.

3. **Actividad válida:** No se puede calificar una actividad que no exista o que haya sido eliminada del sistema.

4. **Historial auditable:** Ninguna calificación se elimina físicamente de la base de datos. Si se corrige una nota, se registra quién hizo el cambio, cuándo y el motivo.

5. **Ponderación correcta:** El cálculo de promedios debe respetar los pesos (porcentajes) asignados a cada tipo de evaluación según la configuración del catálogo.

6. **Periodo vigente:** Solo se pueden registrar calificaciones para periodos académicos que estén activos o dentro del rango de fechas permitido.

7. **Una calificación por estudiante por actividad:** Un estudiante no puede tener dos calificaciones registradas para la misma actividad evaluativa (aunque sí puede modificarse la existente).

8. **Validación de valores nulos:** No se permite guardar calificaciones con valores vacíos o nulos; el sistema debe rechazar registros incompletos.
