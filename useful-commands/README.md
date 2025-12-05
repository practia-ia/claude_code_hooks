# Useful Commands Plugin

Plugin de Claude Code con comandos útiles para gestión de PRs y documentación de sesiones.

## Instalación

```bash
# Registrar el marketplace (instala automáticamente el plugin)
/plugin marketplace add practia-ia/claude_code_plugins

# Habilitar el plugin
/plugin enable useful-commands@practia-ia
```

Requiere Claude Code versión 1.0.40+ (soporte de plugins).

## Comandos Disponibles

### Azure DevOps

#### `/create-pr`

Crea un Pull Request en Azure DevOps hacia la rama main.

**Funcionalidad:**

- Verifica el estado de git y commits pendientes
- Analiza los cambios desde main
- Extrae Work Items de los mensajes de commit
- Genera descripción detallada del PR
- Crea el PR con título y descripción automáticos

#### `/update-pr`

Actualiza un Pull Request existente en Azure DevOps.

**Funcionalidad:**

- Similar a `/create-pr` pero actualiza un PR existente
- Preserva imágenes existentes en la descripción
- Actualiza la descripción con los nuevos cambios

### GitHub

#### `/create-pr-gh`

Crea un Pull Request en GitHub hacia la rama main.

**Funcionalidad:**

- Detecta owner/repo automáticamente del remote
- Verifica el estado de git y commits pendientes
- Analiza los cambios desde main
- Extrae referencias a issues (#123, fixes #456)
- Genera descripción detallada del PR
- Crea el PR con título y descripción automáticos

#### `/update-pr-gh`

Actualiza un Pull Request existente en GitHub.

**Funcionalidad:**

- Similar a `/create-pr-gh` pero actualiza un PR existente
- Preserva imágenes existentes en la descripción
- Actualiza la descripción con los nuevos cambios

### Utilidades

#### `/document`

Documenta la sesión actual de Claude Code.

**Funcionalidad:**

- Crea un resumen detallado de la sesión
- Guarda en `/docs/logs/session_YYYY-MM-DD_HH-MM-SS.md`
- Incluye tareas completadas, problemas resueltos, archivos modificados
- Genera próximos pasos sugeridos

## Requisitos

### Variables de Entorno

#### Para Azure DevOps (`/create-pr`, `/update-pr`)

```bash
AZURE_DEVOPS_ORG=tu-organizacion      # Nombre de tu organización en Azure DevOps
AZURE_DEVOPS_PROJECT=tu-proyecto      # Nombre del proyecto en Azure DevOps
AZURE_DEVOPS_REPO=tu-repositorio      # Nombre del repositorio (opcional, se detecta del remote)
```

#### Para GitHub (`/create-pr-gh`, `/update-pr-gh`)

```bash
GITHUB_PERSONAL_ACCESS_TOKEN=ghp_xxx  # GitHub PAT con scope "repo"
```

Puedes configurarlas en tu archivo `.claude/settings.json` o en las variables de entorno del sistema.

### Autenticación

**Azure DevOps:** La autenticación se realiza mediante el navegador la primera vez que se ejecuta una herramienta. Se te pedirá iniciar sesión con tu cuenta de Microsoft.

**GitHub:** Requiere un Personal Access Token (PAT) con scope `repo`. Puedes crearlo en [GitHub Settings > Developer settings > Personal access tokens](https://github.com/settings/tokens).

### Dependencias

- **Node.js 20+** - Para el MCP de Azure DevOps
- **Docker** - Para el MCP de GitHub (Docker Desktop debe estar corriendo)

## Estructura

```
useful-commands/
├── .claude-plugin/
│   └── plugin.json          # Metadata del plugin
├── .mcp.json                 # Configuración de MCP servers
├── commands/
│   ├── create-pr.md         # Comando /create-pr (Azure DevOps)
│   ├── create-pr-gh.md      # Comando /create-pr-gh (GitHub)
│   ├── document.md          # Comando /document
│   ├── update-pr.md         # Comando /update-pr (Azure DevOps)
│   └── update-pr-gh.md      # Comando /update-pr-gh (GitHub)
└── README.md
```

## MCP Servers Incluidos

### Azure DevOps MCP

- **Repositorio**: [microsoft/azure-devops-mcp](https://github.com/microsoft/azure-devops-mcp)
- **Paquete**: `@azure-devops/mcp`

Por defecto se cargan todos los dominios. Si quieres limitar los dominios, edita `.mcp.json` y agrega el flag `-d`:

```json
"args": ["-y", "@azure-devops/mcp", "${AZURE_DEVOPS_ORG}", "-d", "core", "repositories", "pipelines"]
```

| Dominio | Descripción |
|---------|-------------|
| `core` | Operaciones básicas de Azure DevOps |
| `work` | Gestión de trabajo |
| `work-items` | Work items (tareas, bugs, etc.) |
| `repositories` | Repositorios Git y Pull Requests |
| `pipelines` | Pipelines de CI/CD |
| `wiki` | Wikis del proyecto |
| `test-plans` | Planes de prueba |
| `search` | Búsqueda en Azure DevOps |
| `advanced-security` | Seguridad avanzada |

### GitHub MCP

- **Repositorio**: [github/github-mcp-server](https://github.com/github/github-mcp-server)
- **Imagen**: `ghcr.io/github/github-mcp-server`

El servidor de GitHub incluye herramientas para:
- Gestión de repositorios
- Pull Requests (crear, actualizar, listar, merge)
- Issues (crear, actualizar, comentar)
- Branches y commits
- GitHub Actions

## Notas

- Los comandos son genéricos y funcionan con cualquier proyecto
- Las descripciones de PR se generan en el mismo idioma que los mensajes de commit
- El comando `/document` crea la estructura de directorios si no existe
