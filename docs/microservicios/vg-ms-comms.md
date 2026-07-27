# Microservicio de Comunicaciones (`vg-ms-comms`)

## Información Básica
* **Puerto donde trabaja:** 5088
* **Tecnología:** Java 21 con Spring Boot WebFlux (Programación Reactiva)
* **Base de datos:** PostgreSQL en la nube (Neon)
* **Ubicación del código:** GitLab (`vg-ms-comms`)

---

## ¿Qué hace este microservicio?

Es el responsable de toda la gestión de comunicaciones internas y externas de la plataforma. Centraliza el envío de notificaciones, la gestión de plantillas y la mensajería entre los actores del sistema.

### Tareas Principales:
1. Enviar notificaciones push y correos electrónicos a los destinatarios correspondientes (apoderados, docentes, administrativos).
2. Administrar plantillas de comunicación reutilizables para distintos tipos de evento (bienvenida, incidencia, tutoría, comunicado).
3. Gestionar la bandeja de mensajes internos entre usuarios de la plataforma.
4. Mantener un historial de todas las comunicaciones enviadas para auditoría.

---

## ¿Con quién se relaciona?

Dado que es el servicio central de notificaciones, varios microservicios se comunican con él para enviar avisos:

* **Recibe solicitudes de `vg-ms-welfare` (`tutoring`, `events`, `incidents`):** Para notificar a apoderados sobre sesiones de tutoría, eventos o incidencias registradas.
* **Recibe solicitudes de `vg-ms-enrollment` (Matrículas):** Para notificar confirmación de matrícula o cambios de estado del estudiante.
* **Recibe solicitudes de `vg-ms-attendance` (Asistencia):** Para alertar sobre inasistencias o tardanzas justificadas.
* **Recibe solicitudes de `vg-ms-grading` (Notas):** Para informar sobre la publicación de calificaciones o libretas de notas.
* **Consulta a `vg-ms-user` (Usuarios):** Para obtener los datos de contacto (correo, teléfono) del destinatario de la notificación.

---

## Reglas de Negocio que Siempre se Deben Cumplir

1. **Historial imborrable:** Ninguna comunicación enviada se elimina de la base de datos. Si es necesario anularla, se marca como `ANULADA` con registro de quién y cuándo.
2. **Consentimiento de contacto:** No se debe enviar una notificación a un destinatario que haya solicitado no ser contactado por el canal específico (correo, SMS, push).
3. **Plantilla obligatoria:** Toda comunicación debe usar una plantilla definida; no se permiten envíos con cuerpo de mensaje libre sin aprobación.
4. **Registro de entrega:** Cada comunicación debe registrar al menos el intento de envío, el resultado (enviado, fallido, leído) y la fecha/hora.
5. **Copia mínima del destinatario:** Al crear una notificación, se debe guardar una copia mínima del destinatario (ID, nombre, correo) para mantener la trazabilidad aunque el microservicio de usuarios cambie.
