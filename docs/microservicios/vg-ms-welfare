# Bienestar Estudiantil (`welfare`) — Dominio Compuesto por 3 Microservicios Independientes
 
## Aclaración Importante
 
**`welfare` no es un solo microservicio**, es el nombre del **dominio/módulo de bienestar estudiantil** de EDUNOVA. Dentro de ese dominio existen **3 microservicios independientes y desacoplados entre sí**:
 
| Microservicio | Responsabilidad |
|---|---|
| `vg-ms-tutoring` | Tutorías |
| `vg-ms-events` | Eventos de bienestar |
| `vg-ms-incidents` | Incidencias |
 
No es una arquitectura monolítica: cada uno tiene su propia base de datos, su propio despliegue y puede funcionar aunque los otros dos estén caídos. Se agrupan bajo "welfare" solo como **concepto de dominio de negocio**, igual que "evaluación" agrupa a `eval-activities`, `eval-catalog` y `eval-reportcards`.
 
---
 
## Información Básica de cada microservicio
 
| | `vg-ms-tutoring` | `vg-ms-events` | `vg-ms-incidents` |
|---|---|---|---|
| **Puerto** | 5086 | 5090 | 5091 |
| **Tecnología** | Java 21 + Spring Boot WebFlux | Java 21 + Spring Boot WebFlux | Java 21 + Spring Boot WebFlux |
| **Base de datos** | PostgreSQL (Neon), propia | PostgreSQL (Neon), propia | PostgreSQL (Neon), propia |
| **Código** | GitLab `vg-ms-tutoring` | GitLab `vg-ms-events` | GitLab `vg-ms-incidents` |
 
---
 
## ¿Qué hace cada uno?
 
**`vg-ms-tutoring`**: gestiona la programación de sesiones de tutoría entre un docente/tutor y un estudiante, y su seguimiento hasta el cierre de cada sesión.
 
**`vg-ms-events`**: gestiona la programación de eventos de bienestar (charlas, talleres, actividades) dirigidos a estudiantes o secciones, y el registro de participación.
 
**`vg-ms-incidents`**: gestiona el registro, clasificación por severidad y seguimiento de incidencias estudiantiles hasta su cierre.
 
---
 
## ¿Con quién se relaciona cada uno?
 
Los tres siguen el mismo principio de desacople: **nunca acceden directamente a la base de datos de otro microservicio**, solo consumen HTTP interno y guardan una copia mínima de lo que necesitan.
 
### `vg-ms-tutoring`
- Con `vg-ms-user`: obtiene datos del estudiante y del tutor; guarda copia mínima (`id`, `nombre`, `rol`).
- Con `vg-ms-enrollment`: valida que el estudiante esté matriculado activamente.
- Le da información a `vg-ms-comms`: para notificar al padre cuando se agenda una sesión.
### `vg-ms-events`
- Con `vg-ms-user`: obtiene datos del responsable/organizador; guarda copia mínima.
- Con `vg-ms-academic`: consulta secciones/grados destinatarios del evento.
- Con `vg-ms-schedule`: valida que el aula/horario no choque con una clase.
- Le da información a `vg-ms-comms`: para notificar a los convocados.
### `vg-ms-incidents`
- Con `vg-ms-user`: obtiene datos del estudiante y del responsable que registra; guarda copia mínima.
- Con `vg-ms-enrollment`: valida matrícula activa del estudiante.
- Con `vg-ms-academic`: consulta sección/grado para reportes.
- Le da información a `vg-ms-comms`: para notificar apertura/cierre de incidencia.
> **Nota:** `vg-ms-tutoring`, `vg-ms-events` e `vg-ms-incidents` **no se comunican entre sí ni comparten base de datos**. Son totalmente independientes unos de otros, aunque conceptualmente pertenezcan al mismo dominio de bienestar.
 
---
 
## Reglas de Negocio
 
### `vg-ms-tutoring`
1. Solo se agenda tutoría a un estudiante matriculado activamente.
2. Cada sesión tiene un único tutor y un único estudiante.
3. Ninguna sesión se elimina físicamente; se marca cancelada con motivo.
4. Copia mínima del estudiante/tutor obligatoria al crear la sesión.
### `vg-ms-events`
1. No se programa un evento con fecha anterior a la actual.
2. El aula/horario del evento no puede solaparse con una clase.
3. Un evento cancelado cambia de estado, no se elimina.
4. Copia mínima del responsable obligatoria al crear el evento.
### `vg-ms-incidents`
1. Solo se registra incidencia a estudiante matriculado activamente.
2. Toda incidencia debe clasificarse por severidad (leve, moderada, grave).
3. Ninguna incidencia se elimina; solo cambia de estado.
4. No se puede cerrar una incidencia sin registrar la resolución.
5. Copia mínima del estudiante/responsable obligatoria al crear el registro.
