# ADR-002: Spring Boot WebFlux sobre Spring MVC

**Estado:** Aceptado

**Contexto:**
EDUNOVA manejará operaciones que involucran comunicación HTTP entre microservicios (el Gateway redirige a los servicios internos). Con Spring MVC (servlet síncrono), cada endpoint que consulta a otro servicio bloquea un hilo del pool de Tomcat mientras espera la respuesta, limitando la concurrencia bajo carga. Se espera que el sistema maneje múltiples solicitudes simultáneas en horarios pico (inicio de clases, cierre de periodos).

**Decisión:**
Usar Spring Boot WebFlux con programación reactiva en todos los microservicios backend. WebFlux utiliza un modelo no bloqueante (reactor Netty) que permite manejar más solicitudes concurrentes con menos hilos.

**Consecuencias:**
- Positivas: mejor escalabilidad vertical, menor uso de recursos por solicitud, manejo eficiente de I/O (llamadas a DB, HTTP requests a otros servicios).
- Negativas: curva de aprendizaje del paradigma reactivo (Mono/Flux), debugging más complejo, no todas las librerías tienen soporte reactivo.
- Nota: `vg-ms-grading` usa Spring Boot 3.2.6; el resto usa 3.4.5. Ambos con WebFlux. Para documentación general se referencia Spring Boot 3.x + WebFlux.
