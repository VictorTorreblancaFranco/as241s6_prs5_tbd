# ADR-003: Spring Data R2DBC sobre JPA/Hibernate

**Estado:** Aceptado

**Contexto:**
Al usar WebFlux (reactivo), el acceso a base de datos debe ser no bloqueante para evitar que el hilo reactivo se bloquee al consultar PostgreSQL. JPA/Hibernate es tradicionalmente síncrono y bloqueante; aunque existe Hibernate Reactive, la configuración y ecosistema son más complejos. El equipo ya tiene experiencia con SQL directo y prefiere mantener control sobre las consultas.

**Decisión:**
Usar Spring Data R2DBC para el acceso a datos. R2DBC es el estándar reactivo para bases de datos relacionales, no bloqueante, y se integra de forma nativa con WebFlux.

**Consecuencias:**
- Positivas: driver reactivo no bloqueante alineado con WebFlux, sin lazy loading ni N+1 (control explícito de consultas), mapeo simple y predecible.
- Negativas: no hay soporte para relaciones complejas (sin @OneToMany, sin lazy loading), requiere escribir más consultas manuales (@Query), no hay cache de segundo nivel.
- Compensación: la simplicidad del modelo de datos por microservicio (una DB por servicio) reduce la necesidad de relaciones complejas entre tablas de distintos servicios.
