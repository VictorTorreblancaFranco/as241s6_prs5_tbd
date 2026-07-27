# ADR-001: Trunk Based Development sobre GitFlow

**Estado:** Aceptado

**Contexto:**
EDUNOVA tiene múltiples microservicios que se integran constantemente mediante un API Gateway y contratos HTTP. Con GitFlow, los desarrolladores mantenían ramas largas (`develop`, `feature`) que se desactualizaban rápido, generando conflictos frecuentes al integrar servicios como `attendance`, `schedule`, `enrollment` y `user`. Las integraciones tardaban días y los PRs eran difíciles de revisar.

**Decisión:**
Adoptar Trunk Based Development con `main` como única rama principal. No se usará `develop`. Todas las ramas deben ser cortas (< 1 día idealmente), fusionarse mediante PR revisado, y eliminarse después del merge.

**Consecuencias:**
- Positivas: integración continua real, menos conflictos, `main` siempre estable, PRs pequeños y revisables, despliegue más ágil.
- Negativas: requiere disciplina del equipo para no acumular cambios, obliga a usar feature flags para funcionalidades incompletas, curva de aprendizaje inicial para quienes vienen de GitFlow.
- Riesgo: si un cambio es grande, puede presionar al equipo a mergear código sin suficiente validación. Mitigación: feature flags y CI obligatorio.
