# ADR-006: API Gateway como Único Punto de Entrada

**Estado:** Aceptado

**Contexto:**
El frontend Angular necesita comunicarse con múltiples microservicios backend. Si cada microservicio expone su propia URL, el frontend debe conocer la ubicación de cada uno, aumentando la complejidad y la superficie de ataque. Además, la validación JWT y CORS se duplicaría en cada servicio.

**Decisión:**
Usar Spring Cloud Gateway como API Gateway (puerto 5080), que actúa como único punto de entrada. El frontend solo conoce la URL del Gateway. El Gateway valida JWT, maneja CORS, aplica circuit breaker y enruta las solicitudes al microservicio correspondiente.

**Consecuencias:**
- Positivas: seguridad centralizada (JWT validado en un solo lugar), CORS manejado una vez, balanceo de carga, circuit breaker, el frontend no necesita conocer la topología interna.
- Negativas: el Gateway es un punto único de fallo (single point of failure), posible cuello de botella si no se escala, latencia adicional en cada request.
- Mitigación: replicar el Gateway horizontalmente, configurar health checks y circuit breaker.
