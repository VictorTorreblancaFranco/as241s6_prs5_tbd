# EDUNOVA - Propuesta de Versionamiento con Trunk Based Development

## Proyecto

**EDUNOVA** es una plataforma educativa basada en arquitectura de microservicios, orientada a la gestión académica, administrativa y de seguimiento estudiantil.

El ecosistema actual del PRS está compuesto por un frontend unificado en Angular, un API Gateway y microservicios backend desarrollados principalmente con Java 21, Spring Boot WebFlux, Spring Data R2DBC y PostgreSQL Neon.

## Objetivo

Definir una propuesta estándar de trabajo para el versionamiento del código fuente de los microservicios del PRS EDUNOVA, aplicando **Trunk Based Development**, buenas prácticas de denominación, ramas, commits, Pull Requests e integración continua.

## Alcance

Este repositorio **no contiene código fuente de los microservicios**. Su objetivo es documentar una propuesta única de trabajo para organizar el desarrollo, versionamiento y colaboración del equipo EDUNOVA.

La propuesta aplica para:

- Microservicios backend desarrollados con Java 21, Spring Boot WebFlux y PostgreSQL.
- API Gateway desarrollado con Spring Cloud Gateway.
- Frontend unificado desarrollado con Angular.
- Repositorios de implementación alojados en GitLab.
- Repositorio de propuesta y estándar de trabajo alojado en GitHub.

> Este repositorio contiene únicamente documentación de trabajo. No se deben subir microservicios, archivos `.env`, credenciales, imágenes Docker ni código fuente de implementación.

---

## Arquitectura Actual Del Proyecto

EDUNOVA está organizado bajo una arquitectura de microservicios. El frontend consume únicamente el API Gateway, y el Gateway enruta las solicitudes hacia los microservicios internos.

```text
Frontend Angular
      |
      v
API Gateway - Spring Cloud Gateway
      |
      v
Microservicios Backend
      |
      v
Base de datos por microservicio
```

### Microservicios Identificados

| Microservicio | Puerto | Responsabilidad principal |
|---|---:|---|
| `vg-ms-gateway` | 5080 | Punto único de entrada. Enruta solicitudes, centraliza CORS, validación JWT, circuit breaker y balanceo lógico hacia los servicios. |
| `vg-ms-auth` | 5081 | Gestiona autenticación, credenciales, login, JWT, refresh tokens, roles, permisos y sesiones. |
| `vg-ms-user` | 5082 | Administra usuarios, perfiles, docentes, estudiantes, padres, roles y relaciones padre-estudiante. |
| `vg-ms-schedule` | 5083 | Gestiona horarios, aulas, sesiones de clase y programación semanal. |
| `vg-ms-grading` | 5084 | Gestiona evaluaciones, periodos de nota, actividades calificadas, cierres y reportes académicos. |
| `vg-ms-attendance` | 5085 | Gestiona registro, corrección, auditoría, evidencias y reportes de asistencia. |
| `vg-ms-welfare` | 5086 | Gestiona bienestar estudiantil, tutorías, incidencias, eventos, seguimiento y documentos. |
| `vg-ms-enrollment` | 5087 | Gestiona matrículas, estados, historial académico, traslados, retiros y promoción de estudiantes. |
| `vg-ms-comms` | 5088 | Gestiona comunicaciones, notificaciones, plantillas, bandeja y mensajes internos. |
| `vg-ms-academic` | 5089 | Gestiona el catálogo académico: años académicos, grados, secciones, cursos y relación curso-grado. |

### Tecnologías Del Proyecto

| Capa | Tecnología |
|---|---|
| Frontend | Angular 20.x |
| Backend | Java 21 |
| Framework backend | Spring Boot WebFlux |
| Gateway | Spring Cloud Gateway |
| Base de datos | PostgreSQL Neon |
| Acceso a datos | Spring Data R2DBC |
| Contenedores | Docker |
| Despliegue propuesto | Kubernetes / VPS |
| Repositorios de implementación | GitLab |
| Repositorio de propuesta TBD | GitHub |

La mayoría de microservicios backend utilizan **Spring Boot 3.4.5**. El microservicio `vg-ms-grading` utiliza **Spring Boot 3.2.6**. Para documentación general se recomienda indicar **Spring Boot 3.x + WebFlux**.

### Por Qué Aplicar Trunk Based Development

EDUNOVA tiene varios microservicios que se integran constantemente mediante el Gateway y contratos HTTP. Si cada desarrollador mantiene ramas largas o trabaja durante mucho tiempo separado de la rama principal, aumentan los conflictos y se vuelve más difícil integrar servicios como `attendance`, `schedule`, `enrollment`, `user` y `academic`.

Trunk Based Development permite que el equipo integre cambios pequeños y frecuentes sobre una única rama principal estable, reduciendo conflictos y mejorando la trazabilidad del trabajo.

---

## Organización De Repositorios

La implementación real de EDUNOVA se mantiene en repositorios GitLab por microservicio y por frontend.

### Repositorios De Implementación

| Repositorio | Contenido |
|---|---|
| `vg-ms-gateway` | API Gateway |
| `vg-ms-auth` | Autenticación y credenciales |
| `vg-ms-user` | Gestión de usuarios |
| `vg-ms-attendance` | Asistencia |
| `vg-ms-academic` | Catálogo académico |
| `vg-ms-grading` | Evaluación y notas |
| `vg-ms-enrollment` | Matrícula |
| `vg-ms-schedule` | Horarios |
| `vg-ms-comms` | Comunicaciones |
| `vg-ms-welfare` | Bienestar estudiantil |
| `vg-web-edunova` | Frontend unificado Angular |

### Repositorio De Propuesta

Este repositorio GitHub se denomina:

```text
as241s6_prs5_tbd
```

Su contenido debe limitarse al archivo `README.md`, donde se define la propuesta de denominación, ramas y buenas prácticas con Trunk Based Development.

No contiene:

- Código fuente.
- Microservicios.
- Ramas por microservicio.
- Archivos `.env`.
- Credenciales.
- Configuración productiva.
- Imágenes Docker.

---

## Estrategia De Ramas

EDUNOVA utilizará **Trunk Based Development**, por lo tanto la rama principal será:

```text
main
```

### Por Qué `main` Será El Trunk

La rama `main` representa siempre la versión más estable e integrable del proyecto. Todos los cambios deben integrarse frecuentemente mediante Pull Requests pequeños y revisados.

Esto evita que el equipo mantenga ramas largas, ramas desactualizadas o integraciones grandes al final del sprint.

### Por Qué No Se Usará `develop`

No se utilizará `develop` porque en Trunk Based Development se busca integrar continuamente sobre una única rama principal.

Usar `develop` agregaría una segunda línea de integración, generando:

- Más conflictos.
- Más retrasos para integrar.
- Mayor diferencia entre ramas.
- Doble validación innecesaria.
- Riesgo de que `main` quede desactualizada.

El flujo recomendado será:

```text
feature/fix/refactor/docs
        |
        v
Pull Request
        |
        v
main
```

---

## Nomenclatura De Ramas

Las ramas deben ser cortas, descriptivas y relacionadas con una tarea específica.

### Formato General

```text
<tipo>/<microservicio>-<descripcion-corta>
```

### Tipos Permitidos

| Tipo | Uso |
|---|---|
| `feature` | Nueva funcionalidad |
| `fix` | Corrección de error |
| `refactor` | Mejora interna sin cambiar comportamiento |
| `docs` | Cambios de documentación |
| `test` | Agregar o corregir pruebas |
| `chore` | Tareas técnicas menores |
| `ci` | Cambios en integración continua |
| `build` | Cambios en build, Docker o dependencias |

### Ejemplos Adaptados A EDUNOVA

```text
feature/attendance-bulk-register
feature/enrollment-bulk-promotion
feature/comms-notification-template
feature/schedule-classroom-validation

fix/attendance-late-validation
fix/gateway-cors-config
fix/user-parent-student-link
fix/grading-period-close

refactor/attendance-domain-service
refactor/auth-token-validation
refactor/schedule-webclient-config

docs/architecture-tbd-standard
docs/attendance-postman-guide
docs/deployment-vps-notes

test/attendance-business-rules
test/enrollment-transfer-flow

ci/gateway-github-actions
build/attendance-dockerfile
```

### Reglas Para Ramas

- Las ramas deben vivir poco tiempo.
- Una rama debe representar una sola tarea.
- No se deben crear ramas por persona.
- No se deben crear ramas largas por sprint.
- No se debe usar `develop`.
- Toda rama debe terminar en Pull Request hacia `main`.
- La rama se elimina después del merge.

---

## Flujo De Trabajo

### Desde Una Tarea Hasta `main`

```text
1. Se recibe una tarea
        |
        v
2. Se actualiza main local
        |
        v
3. Se crea una rama corta
        |
        v
4. Se implementa el cambio
        |
        v
5. Se ejecutan pruebas locales
        |
        v
6. Se crea Pull Request
        |
        v
7. Revisión de código
        |
        v
8. Pipeline CI correcto
        |
        v
9. Merge a main
        |
        v
10. Eliminación de rama
```

### Pull Requests

Todo cambio debe ingresar a `main` mediante Pull Request.

Un Pull Request debe incluir:

- Descripción clara del cambio.
- Microservicio afectado.
- Evidencia de pruebas.
- Riesgos identificados.
- Capturas o ejemplos si aplica al frontend.
- Referencia a la tarea o requerimiento.

### Tamaño De Pull Requests

Un Pull Request ideal debe:

- Resolver una sola necesidad.
- Ser fácil de revisar.
- No mezclar refactor con funcionalidad.
- No cambiar varios microservicios sin justificación.
- Poder revertirse con facilidad.

### Revisión De Código

Antes de aprobar un Pull Request se debe revisar:

- Correcta separación de responsabilidades.
- Validaciones de negocio.
- Manejo de errores.
- Seguridad.
- Pruebas.
- Estilo de código.
- Impacto en otros microservicios.
- Compatibilidad con el Gateway.
- Cambios en contratos de API.

### Eliminación De Ramas

Luego de fusionar el Pull Request hacia `main`, la rama debe eliminarse para mantener limpio el repositorio.

---

## Convención De Commits

EDUNOVA utilizará **Conventional Commits**.

### Formato

```text
<tipo>(<alcance>): <descripcion>
```

### Tipos Permitidos

| Tipo | Uso |
|---|---|
| `feat` | Nueva funcionalidad |
| `fix` | Corrección de error |
| `refactor` | Refactor interno |
| `docs` | Documentación |
| `test` | Pruebas |
| `chore` | Tareas menores |
| `ci` | Integración continua |
| `build` | Build, Docker o dependencias |
| `perf` | Mejora de rendimiento |
| `style` | Formato sin cambio funcional |

### Alcances Recomendados

Los alcances deben coincidir con el microservicio o módulo afectado:

```text
gateway
auth
user
attendance
academic
grading
enrollment
schedule
comms
welfare
frontend
docker
docs
ci
```

### Ejemplos Reales Para EDUNOVA

```text
feat(attendance): add bulk attendance registration
fix(attendance): validate late arrival tolerance
docs(attendance): add postman production guide

feat(enrollment): add bulk student promotion
fix(enrollment): prevent duplicate active enrollment

feat(schedule): add classroom availability validation
fix(schedule): prevent overlapping teacher schedules

feat(user): add parent student relationship endpoint
fix(user): validate role permissions for student creation

feat(comms): add notification template management
fix(comms): correct inbox filtering by recipient

fix(gateway): update attendance route configuration
ci(gateway): add build validation workflow

refactor(grading): simplify assignment service rules
test(attendance): add business rule validation tests
build(attendance): update docker image configuration
```

---

## Buenas Prácticas Para El Equipo

1. Mantener `main` siempre estable.
2. No trabajar directamente sobre `main`.
3. Crear ramas pequeñas y de corta duración.
4. Integrar cambios frecuentemente.
5. Evitar ramas largas por sprint.
6. No usar `develop` en este modelo.
7. Crear Pull Requests pequeños y revisables.
8. No mezclar cambios de distintos objetivos en un solo PR.
9. Usar nombres de ramas descriptivos.
10. Usar Conventional Commits.
11. Ejecutar pruebas antes de crear un PR.
12. No subir credenciales, tokens ni archivos `.env` reales.
13. Mantener variables sensibles en Secrets.
14. Documentar cambios que afecten contratos de API.
15. Evitar que un microservicio acceda directamente a la base de datos de otro.
16. Consumir otros servicios mediante HTTP interno, Gateway o eventos según corresponda.
17. Mantener una base de datos por microservicio.
18. Respetar la responsabilidad de cada microservicio.
19. No duplicar lógica de negocio entre servicios.
20. Validar reglas críticas dentro del microservicio dueño del dominio.
21. Mantener endpoints claros y versionables.
22. Documentar endpoints importantes con Postman o Swagger.
23. No romper compatibilidad sin avisar al equipo.
24. Revisar impacto en frontend cuando cambia una respuesta de API.
25. Usar logs claros para errores de negocio.
26. Configurar health checks por microservicio.
27. Mantener Dockerfiles ligeros y reproducibles.
28. Usar límites de CPU y memoria en despliegues.
29. Revisar dependencias antes de actualizarlas.
30. Eliminar ramas ya fusionadas.
31. Evitar commits genéricos como `update`, `changes` o `fix bugs`.
32. Toda corrección crítica debe incluir prueba o evidencia.
33. El Gateway debe mantenerse como único punto de entrada.
34. El frontend debe consumir APIs a través del Gateway.
35. Cada microservicio debe conservar independencia de despliegue.

---

## Integración Continua

Trunk Based Development requiere que `main` se mantenga estable. Para lograrlo, se propone utilizar GitHub Actions o pipelines equivalentes.

### Validaciones Recomendadas Para Backend

```text
- Compilar proyecto Maven
- Ejecutar pruebas unitarias
- Validar dependencias
- Construir imagen Docker
- Revisar formato y calidad básica
```

### Validaciones Recomendadas Para Frontend

```text
- Instalar dependencias
- Compilar proyecto Angular
- Ejecutar pruebas
- Validar lint si aplica
```

### Validaciones Recomendadas Para Documentación

```text
- Validar existencia de README.md
- Validar estructura mínima
- Revisar enlaces importantes
```

### Flujo CI Propuesto

```text
Pull Request
      |
      v
GitHub Actions
      |
      |-- build
      |-- test
      |-- docker validation
      |-- documentation check
      v
PR aprobado
      |
      v
merge a main
```

Si el pipeline falla, el Pull Request no debe fusionarse.

---

## Beneficios Para EDUNOVA

El uso de Trunk Based Development beneficia a EDUNOVA porque el proyecto está compuesto por varios microservicios que deben integrarse de forma constante.

| Beneficio | Impacto En EDUNOVA |
|---|---|
| Integración frecuente | Evita que `attendance`, `schedule`, `enrollment` o `user` se desactualicen entre sí. |
| Menos conflictos | Reduce conflictos al trabajar con ramas pequeñas. |
| `main` estable | Permite tener una versión integrable del sistema en todo momento. |
| Mejor revisión | Los PR pequeños facilitan detectar errores en reglas de negocio. |
| Despliegue más ordenado | Facilita construir imágenes Docker desde una rama estable. |
| Mejor trazabilidad | Los commits indican qué microservicio fue afectado. |
| Menos ramas acumuladas | Evita desorden en repositorios con muchos integrantes. |
| Mayor calidad | Cada cambio pasa por revisión y pipeline antes de entrar a `main`. |
| Mejor coordinación frontend/backend | Ayuda a controlar cambios en contratos API consumidos por Angular. |
| Escalabilidad del equipo | Permite que varios responsables trabajen sin bloquearse. |

### Ejemplo En EDUNOVA

Si se modifica `vg-ms-attendance` para cambiar una regla de tardanza, el flujo TBD obliga a:

- Crear una rama corta.
- Hacer un PR.
- Ejecutar pruebas.
- Revisar impacto con frontend.
- Integrar rápido a `main`.

Esto evita que el cambio quede aislado durante varios días y luego genere conflictos con `schedule`, `enrollment` o `user`.

---

## Denominación Estándar

### Microservicios Backend

Los microservicios backend deben mantener el prefijo:

```text
vg-ms-
```

Ejemplos:

```text
vg-ms-attendance
vg-ms-enrollment
vg-ms-schedule
```

### Frontend

El frontend unificado debe mantener una denominación clara:

```text
vg-web-edunova
```

### Imágenes Docker

Las imágenes Docker deben seguir el nombre del servicio:

```text
vg-ms-attendance:latest
vg-ms-gateway:latest
vg-web-edunova:latest
```

En ambientes productivos se recomienda usar tags versionados:

```text
vg-ms-attendance:1.0.0
vg-ms-attendance:1.0.1
vg-ms-attendance:2026.07.23
```

### Variables De Entorno

Las variables deben ser descriptivas y por servicio:

```text
SERVER_PORT
SPRING_R2DBC_URL
AUTH_SERVICE_URL
USER_SERVICE_URL
ATTENDANCE_SERVICE_URL
SCHEDULE_SERVICE_URL
ENROLLMENT_SERVICE_URL
```

---

## Modelo De Responsabilidades

Cada microservicio debe ser dueño de su dominio.

| Servicio | Dueño de |
|---|---|
| `auth` | Credenciales, JWT y sesión |
| `user` | Usuarios, roles institucionales y perfiles |
| `academic` | Catálogo académico |
| `schedule` | Horarios y aulas |
| `enrollment` | Matrícula e historial |
| `attendance` | Asistencia, auditoría y evidencias |
| `grading` | Evaluaciones y notas |
| `welfare` | Bienestar e incidencias |
| `comms` | Comunicaciones y notificaciones |
| `gateway` | Enrutamiento y entrada única |

Regla principal:

```text
Un microservicio no debe modificar directamente datos que pertenecen a otro microservicio.
```

---

## Conclusiones

La propuesta de Trunk Based Development para EDUNOVA permite establecer una forma de trabajo ordenada, profesional y sostenible para un proyecto basado en microservicios.

Al utilizar `main` como única rama principal, ramas cortas, Pull Requests pequeños, Conventional Commits e integración continua, el equipo reduce conflictos, mejora la calidad del código y mantiene una versión estable del sistema.

Esta estrategia se adapta al contexto real de EDUNOVA porque el proyecto está compuesto por múltiples microservicios independientes, un API Gateway, un frontend unificado y bases de datos separadas por dominio. La propuesta permite que cada responsable trabaje sobre su microservicio sin perder integración con el resto de la plataforma.

El objetivo final es que EDUNOVA mantenga una estructura de desarrollo clara, trazable y alineada a buenas prácticas profesionales de versionamiento de código fuente.
