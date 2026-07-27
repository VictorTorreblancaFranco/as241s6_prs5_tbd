# Gestión de Horarios y Aulas (schedule) — Microservicio Central de Agenda Escolar

## Aclaración Importante
`vg-ms-schedule` es un microservicio autónomo y desacoplado, diseñado bajo una arquitectura de microservicios reactivos en EDUNOVA. Cuenta con su propia base de datos (PostgreSQL), su propio ciclo de vida de despliegue y puede funcionar de manera independiente, interactuando con otros servicios únicamente mediante APIs HTTP. Su responsabilidad principal es garantizar la consistencia física y temporal de la agenda de clases de la institución.

---

## Información Básica

| Atributo | Detalle |
| :--- | :--- |
| **Puerto** | `5083` |
| **Tecnología** | Java 21 + Spring Boot 3+ (WebFlux Reactivo + R2DBC) |
| **Base de Datos** | PostgreSQL (Neon), propia y aislada |
| **Código/Repo** | `vg-ms-schedule` |

---

## ¿Qué hace el microservicio?
* **Gestión de Aulas (`classrooms`)**: Administra el catálogo físico de espacios de la institución (salones, laboratorios, talleres), incluyendo capacidad y tipo de aula.
* **Programación Semanal (`schedule_entries`)**: Crea y estructura la agenda semanal de clases asociando días de la semana, horas de inicio/fin, docentes, aulas, cursos, grados y secciones.

---

## ¿Con quién se relaciona?
Sigue el principio de desacoplamiento: nunca accede a la base de datos de otros servicios, interactúa a través de WebClient reactivos y aplica tolerancia a fallos con Resilience4j.

* **Con [vg-ms-auth](file:///c:/Users/USUARIO/Proyectos-5to/EDUNOVA-3.0/vg-ms-schedule/src/main/java/com/vg/schedule/infrastructure/adapter/out/client/AuthServiceClient.java)**: Solicita y renueva automáticamente un token JWT del sistema utilizando credenciales del servicio para autenticar llamadas inter-servicio.
* **Con [vg-ms-user](file:///c:/Users/USUARIO/Proyectos-5to/EDUNOVA-3.0/vg-ms-schedule/src/main/java/com/vg/schedule/infrastructure/adapter/out/client/UserClient.java)**: Valida la existencia y estado activo del docente (`teacher_id`) mediante peticiones seguras que incluyen el token de servicio en las cabeceras.
* **Con [vg-ms-academic](file:///c:/Users/USUARIO/Proyectos-5to/EDUNOVA-3.0/vg-ms-schedule/src/main/java/com/vg/schedule/infrastructure/adapter/out/client/AcademicClient.java)**: Verifica de forma pública que existan el año académico, grado, sección y curso asignados a la entrada de horario (sin requerir token).

---

## Reglas de Negocio

1. **Evitar traslapes de Horario (Consistencia Física y Temporal)**:
   * **Para Docentes**: Un profesor no puede tener dos clases asignadas el mismo día en el mismo rango de horas.
   * **Para Aulas**: Un salón o laboratorio no puede albergar más de una clase en el mismo rango horario de un mismo día.
2. **Validación de Rango Temporal**:
   * La hora de inicio (`start_time`) debe ser estrictamente anterior a la hora de fin (`end_time`).
3. **Validación de Integridad Inter-Servicios**:
   * Para registrar cualquier horario, se verifica concurrentemente en otros microservicios que existan el docente, el curso, el grado, la sección y el año académico.
4. **Filtro de Estado Activo y Soft Deletes**:
   * Ningún registro de horario o aula se elimina físicamente de la base de datos. Se utiliza un borrado lógico marcando `active = false` para conservar el historial.
5. **Seguridad y Permisos de Escritura**:
   * Las operaciones de creación, edición y eliminación de aulas u horarios están restringidas por JWT únicamente para usuarios con el rol **DIRECTOR**.
