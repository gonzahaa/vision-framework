# VISION Framework

[English version](README.en.md) | Español

VISION es un framework local-first y guiado por agentes para convertir el contexto de un proyecto en especificaciones verificables, un plan de sprints y trabajo implementable. Sus comandos, precondiciones, scripts y estado del workflow viven en el repositorio.

Un agente de IA en tu IDE lee el contexto, produce o actualiza los artefactos de planificación e implementa las tareas del sprint. Los scripts de Node.js automatizan operaciones repetibles y mantienen coherente el estado. El flujo básico no requiere un servicio de IA alojado; las integraciones externas son opcionales.

## Qué aporta

- **Flujo local-first:** el contexto y los artefactos generados permanecen en el workspace; las integraciones externas son opcionales.
- **Comandos independientes del IDE:** `commands/` es la fuente de verdad y `scripts/` automatiza pasos concretos.
- **Etapas explícitas:** cada fase tiene precondiciones y resultados verificables.
- **Proyectos flexibles:** sirve para proyectos nuevos y para ingeniería inversa a partir de código existente.
- **Roles especializados:** integra Agency Agents y configuraciones para distintos IDEs.

## Inicio rápido

Requisitos: Node.js 18 o posterior y un IDE con agente de IA.

```bash
git clone https://github.com/gonzahaa/vision-framework.git
cd vision-framework
npm install
npm run setup:ide
npm run status
```

Agrega los documentos del proyecto a `refdocs/`. Si harás ingeniería inversa, agrega el código existente a `source-code/`. Después, escribe `init` en el agente de tu IDE y sigue la acción que recomiende. Los comandos del flujo VISION se escriben en el agente, no en la terminal.

Consulta el avance con `npm run status`. Para iniciar el tablero local, usa `npm run status:web`.

## Flujo de trabajo

```text
init
  → clarify_brief (opcional) → generate_brief
  → clarify_spec_kit (opcional) → generate_spec_kit
  → clarify_sprints (opcional) → generate_sprints
  → start_sprint --sprint N / continue_sprint
```

Las clarificaciones son opcionales y se usan cuando el agente encuentra ambigüedades. Cada etapa depende de la anterior: el brief alimenta la especificación, la especificación alimenta la planificación y el siguiente sprint comienza cuando el actual está completo. Usa `next_step` para detectar la siguiente acción disponible. Durante `start_sprint` y `continue_sprint`, el agente debe implementar las tareas, no limitarse a resumirlas.

## Artefactos

- `spec-kit/input/brief.md`: objetivos, alcance, usuarios, restricciones y criterios de éxito.
- `spec-kit/input/`: especificaciones de producto y técnicas, modelo de datos, contrato API, épicas, historias, plan de sprints y plan de pruebas.
- `planning/sprints/`: objetivos, historias, tareas y planes de QA por sprint.
- `planning/workflow-state.json`: estado actual y etapas completadas.

## Configuración de IDE

`npm run setup:ide` genera los puntos de entrada del IDE a partir de las definiciones canónicas en `commands/`. Incluye configuraciones para Cursor, Claude Code, OpenCode, GitHub Copilot y Antigravity. Consulta [commands/README.md](commands/README.md) y [AGENTS.md](AGENTS.md) para más información.

## Comandos útiles

| Comando | Propósito |
|---|---|
| `npm run status` | Mostrar la etapa actual del workflow |
| `npm run status:web` | Iniciar el tablero local del workflow |
| `npm run next_step` | Detectar la siguiente acción disponible |
| `npm run test:local` | Ejecutar las pruebas locales de Node.js |
| `npm run lint` | Analizar los scripts del framework |
| `npm run format:check` | Comprobar el formato |
| `npm run check:scripts` | Validar convenciones de scripts |

Guías completas: [español](GUIA_COMPLETA_VISION.md) · [English](GUIA_COMPLETA_VISION.en.md). La documentación del framework está en [docs/README.md](docs/README.md); cada comando tiene su propia guía en `commands/`.

## Estructura del repositorio

```text
commands/       Definiciones de comandos IDE-agnósticos
scripts/        Implementación del workflow en Node.js
bin/            Entrada del CLI de VISION
planning/       Estado del proyecto actual y planes de sprint
spec-kit/       Entradas de especificación y plantillas
refdocs/        Documentos fuente del proyecto (ignorados por defecto)
source-code/    Código existente para proyectos de ingeniería inversa
agency-agents/  Roles especializados de agentes
mcp/            Integración MCP local opcional
```

## Integraciones opcionales

El flujo central puede ejecutarse localmente. El reporte a Matrix y la integración con un CLI de IA local son opcionales. Consulta [docs/matrix-reporting.md](docs/matrix-reporting.md) y [docs/mcp-local-setup.md](docs/mcp-local-setup.md) para configurarlos.

## Licencia

MIT. Consulta [LICENSE](LICENSE).
