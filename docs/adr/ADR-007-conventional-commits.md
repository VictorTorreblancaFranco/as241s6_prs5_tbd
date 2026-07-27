# ADR-007: Conventional Commits como Estándar de Commits

**Estado:** Aceptado

**Contexto:**
El equipo necesita un formato de commits consistente para generar changelogs automáticos, facilitar la revisión de código y entender el historial del proyecto sin leer cada diff. Sin un estándar, los commits tienden a ser genéricos ("update", "fix", "changes").

**Decisión:**
Adoptar Conventional Commits con el formato `<tipo>(<alcance>): <descripción>`.

Tipos permitidos: `feat`, `fix`, `refactor`, `docs`, `test`, `chore`, `ci`, `build`, `perf`, `style`.

Alcances: `gateway`, `auth`, `user`, `attendance`, `academic`, `grading`, `enrollment`, `schedule`, `comms`, `welfare`, `frontend`, `docker`, `docs`, `ci`.

**Consecuencias:**
- Positivas: trazabilidad clara de qué cambió y en qué servicio, posibilidad de generar CHANGELOG.md automáticamente (semantic-release), facilita la revisión cruzada entre servicios.
- Negativas: requiere disciplina para escribir commits correctamente, los desarrolladores pueden sentirse limitados por el formato.
- Compensación: se permiten excepciones documentadas en commits de merge o fixups durante rebase.
