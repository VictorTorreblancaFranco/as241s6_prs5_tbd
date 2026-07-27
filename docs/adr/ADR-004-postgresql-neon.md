# ADR-004: PostgreSQL Neon como Base de Datos

**Estado:** Aceptado

**Contexto:**
EDUNOVA necesita una base de datos relacional que soporte operaciones transaccionales, schema por microservicio, y esté disponible en la nube sin necesidad de administrar servidores. El equipo está familiarizado con PostgreSQL. Se evaluaron opciones como MySQL, Supabase y PlanetScale.

**Decisión:**
Usar PostgreSQL administrado por Neon (serverless PostgreSQL) como base de datos para todos los microservicios.

**Consecuencias:**
- Positivas: PostgreSQL compatible al 100%, soporte nativo para R2DBC, escalabilidad serverless (auto-pausa), branching para entornos de desarrollo, buena integración con el stack Java/Spring.
- Negativas: dependencia de un proveedor externo, latencia de "cold start" al reanudar después de pausa, límites de compute en el plan gratuito.
- Cada microservicio tiene su propia base de datos en Neon, lo que refuerza el aislamiento de datos.
