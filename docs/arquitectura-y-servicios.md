# Arquitectura de EDUNOVA y Mapa de Microservicios

## 1. ¿Cómo funciona EDUNOVA?

EDUNOVA no es un solo programa gigante. Está dividido en pequeños programas independientes llamados **microservicios**. Cada microservicio se encarga de un solo tema (por ejemplo: uno lleva las notas, otro la asistencia, otro las matrículas).

### Reglas de Oro del Sistema

1. **Aislamiento de Datos:** 
   *Un microservicio no se mete en la base de datos de otro.* Si el microservicio de *Matrículas* necesita el nombre de un alumno, no va a la base de datos de *Usuarios*. Se lo pide mediante una petición interna (HTTP/WebClient) al microservicio de *Usuarios*.

2. **Acceso Único por el Gateway (Puerta de Entrada):**
   El Frontend (página web Angular) **nunca habla directamente** con los microservicios internos. Todas las peticiones deben pasar obligatoriamente por el **API Gateway (Puerto 5080)**, que actúa como guardia de seguridad validando el token de acceso.

3. **Independencia en Caída (Tolerancia a Fallos):**
   Si un microservicio falla o se apaga (por ejemplo, *Bienestar*), **los demás deben seguir funcionando con normalidad**. Ningún servicio debe bloquear el funcionamiento general del colegio.

4. **Formato Estándar de Comunicación:**
   Todos los microservicios deben responder en formato **JSON** y usar los mismos códigos de estado HTTP (ejemplo: `200` para éxito, `400` para datos incorrectos, `401` para sin permiso, `404` para no encontrado, `500` para error interno).

5. **Aislamiento de Tecnologías y Versiones:**
   Cada microservicio debe ser capaz de compilarse, ejecutarse y desplegarse de manera totalmente independiente en su propio contenedor (Docker), sin depender de los archivos de código de los demás.
---

## 2. Cómo se comunican las partes

El sistema tiene tres capas principales:

1. **Pantalla (Frontend - Angular):** Es la página web que ven los profesores, alumnos y apoderados.
2. **Puerta de Entrada (API Gateway):** Es la única entrada a las tripas del sistema. Recibe las peticiones de la pantalla, verifica que el usuario tenga permiso y redirige la petición al microservicio correcto.
3. **Microservicios (Backend - Java / WebFlux):** Procesan la información y la guardan en su propia base de datos (PostgreSQL Neon).

```text
[ Pantalla Angular (vg-web-edunova) ]
        |
        v
[ Puerta de Entrada (Gateway) - Puerto 5080 (vg-ms-gateway) ]
        |
        +---> [ Autenticacion ] ---------> Base de Datos de Auth (db_auth)
        +---> [ Usuarios ] --------------> Base de Datos de Usuarios (db_user)
        +---> [ Catálogo Académico ] ----> Base de Datos Académica (db_academic)
        +---> [ Horarios ] --------------> Base de Datos de Horarios (db_schedule)
        +---> [ Matriculas ] ------------> Base de Datos de Matriculas (db_enrollment)
        +---> [ Asistencia ] ------------> Base de Datos de Asistencia (db_attendance)
        +---> [ Notas ] -----------------> Base de Datos de Notas (db_grading)
        +---> [ Bienestar ] -------------> Base de Datos de Bienestar (db_welfare)
        +---> [ Comunicaciones ] --------> Base de Datos de Comunicaciones (db_comms)

| Microservicio | Puerto | Descripción de su Función |
|---|---|---|
| **`vg-ms-auth`** | `5081` | Maneja los inicios de sesión, contraseñas, permisos y valida las credenciales del usuario mediante tokens seguros (JWT). |
| **`vg-ms-user`** | `5082` | Almacena y gestiona la información personal de todos los perfiles de la institución: alumnos, profesores, apoderados y personal administrativo. |
| **`vg-ms-academic`** | `5089` | Define la estructura del colegio: años lectivos, niveles (primaria/secundaria), grados, secciones y las asignaturas impartidas. |
| **`vg-ms-schedule`** | `5083` | Administra la asignación de horarios de clase, la disponibilidad de las aulas físicas y la carga horaria de los docentes. |
| **`vg-ms-enrollment`** | `5087` | Controla el proceso de inscripción y matrícula de alumnos, la reserva de vacantes, traslados y la promoción de grado año a año. |
| **`vg-ms-attendance`** | `5085` | Registra el control diario de asistencia a clases, marcando asistencias, tardanzas, faltas y la gestión de justificaciones. |
| **`vg-ms-grading`** | `5084` | Gestiona el registro de evaluaciones, ingreso de calificaciones por periodo escolar y la generación de libretas de notas. |
| **`vg-ms-welfare`** | `5086` | Realiza el seguimiento al bienestar estudiantil, incluyendo citas psicopedagógicas, reportes de conducta e incidencias escolares. |
| **`vg-ms-comms`** | `5088` | Encargado del envío de avisos institucionales, comunicados para apoderados, correos electrónicos y notificaciones internas. |