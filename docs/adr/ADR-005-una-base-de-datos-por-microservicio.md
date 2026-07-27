# ADR-005: Una Base de Datos por Microservicio

**Estado:** Aceptado

**Contexto:**
El equipo necesita asegurar que los microservicios sean independientes entre sí. Si dos servicios comparten una misma base de datos, se genera acoplamiento oculto: cambios en el schema de uno pueden romper al otro, y no hay límite real de responsabilidad.

**Decisión:**
Cada microservicio backend tiene su propia base de datos PostgreSQL en Neon. Ningún servicio accede directamente a la base de datos de otro; si necesita datos ajenos, los solicita mediante HTTP interno (WebClient) al servicio propietario.

**Consecuencias:**
- Positivas: desacoplamiento real, cada equipo puede evolucionar su schema sin coordinar con otros, tolerancia a fallos (una DB caída no afecta a las demás).
- Negativas: complejidad en consultas que cruzan dominios (obligatorio llamada HTTP), latencia adicional por red, eventual inconsistencia (no hay transacciones distribuidas).
- Mitigación: cada servicio guarda una "copia mínima" de datos que necesita de otros servicios (ej: `id`, `nombre`, `rol`) para reducir llamadas repetitivas.
