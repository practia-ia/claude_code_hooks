# Claude Code Plugins - Conversation Logger

Plugin oficial para Claude Code que registra automáticamente las conversaciones en archivos de log locales con rotación automática.

## Descripción

Este plugin captura y guarda:
- **Mensajes del usuario**: Cada prompt enviado a Claude
- **Uso de herramientas**: Registro de las herramientas utilizadas (Read, Write, Edit, Bash, Grep, etc.)
- **Respuestas del asistente**: El texto de respuesta generado por Claude

Los logs se guardan en `.claude/logs/` dentro del proyecto con rotación automática cuando alcanzan 512 KB.

## Requisitos

- **Claude Code** versión 1.0.40 o superior (soporte de plugins)
- **Windows** con PowerShell 5.1 o superior
- **Git** (para clonar el repositorio)

## Instalación

### Opción 1: Instalación desde GitHub (Recomendado)

Una vez publicado en GitHub, instala directamente desde Claude Code:

```bash
# Registrar el marketplace
/plugin marketplace add pratia-ia/claude_code_hooks

# Instalar el plugin
/plugin install conversation-logger@pratia-ia

# Verificar instalación
/plugin list
```

> Reemplaza `owner` con el usuario/organización de GitHub donde está publicado el repositorio.

### Opción 2: Instalación Local (Desarrollo)

#### 1. Clonar el repositorio

```bash
git clone <URL_DEL_REPOSITORIO> claude_code_hooks
cd claude_code_hooks
```

#### 2. Registrar el marketplace local

Dentro de Claude Code, ejecuta:

```bash
# Registrar el marketplace local (usar ruta absoluta)
/plugin marketplace add C:/ruta/a/claude_code_hooks

# Instalar el plugin
/plugin install conversation-logger@claude_code_hooks

# Verificar instalación
/plugin list
```

### Opción 3: Configuración en Settings del Proyecto

Para habilitar automáticamente en un proyecto, agrega a `.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": [
    "owner/claude_code_hooks"
  ],
  "enabledPlugins": {
    "owner": ["conversation-logger"]
  }
}
```

## Gestión del Plugin

```bash
# Ver menú interactivo de plugins
/plugin

# Listar plugins instalados
/plugin list

# Listar marketplaces registrados
/plugin marketplace list

# Habilitar/deshabilitar plugin
/plugin enable conversation-logger@marketplace-name
/plugin disable conversation-logger@marketplace-name

# Desinstalar plugin
/plugin uninstall conversation-logger@marketplace-name

# Actualizar marketplace
/plugin marketplace update marketplace-name
```

## Estructura del Plugin

```
claude_code_hooks/
├── README.md
├── .claude-plugin/
│   └── marketplace.json        # Definición del marketplace
└── conversation-logger/
    ├── .claude-plugin/
    │   └── plugin.json         # Manifiesto del plugin
    └── hooks/
        ├── hooks.json          # Configuración de hooks
        └── log-conversation.ps1 # Script principal
```

### Archivo plugin.json

```json
{
  "name": "conversation-logger",
  "version": "1.0.0",
  "description": "Logs Claude Code conversations to local files with automatic rotation",
  "author": "practiauy",
  "hooksPath": "./hooks/hooks.json"
}
```

## Hooks Configurados

| Evento | Descripción | Timeout |
|--------|-------------|---------|
| `UserPromptSubmit` | Se ejecuta antes de procesar cada mensaje del usuario | 10s |
| `PostToolUse` | Se ejecuta después de cada uso de herramienta | 10s |
| `Stop` | Se ejecuta cuando el asistente termina de responder | 15s |

## Variables de Entorno

El script utiliza estas variables de entorno proporcionadas por Claude Code:

| Variable | Descripción |
|----------|-------------|
| `$CLAUDE_PLUGIN_DIR` | Directorio donde está instalado el plugin |
| `$CLAUDE_PROJECT_DIR` | Directorio raíz del proyecto actual |

## Entrada JSON de los Hooks

Los hooks reciben información a través de JSON en `stdin`:

```json
{
  "session_id": "sess_123abc",
  "hook_event_name": "UserPromptSubmit",
  "prompt": "El texto del prompt del usuario",
  "transcript_path": "/path/to/transcript.ndjson",
  "cwd": "/current/working/dir",
  "tool_name": "Read",
  "tool_input": {
    "file_path": "/path/to/file"
  }
}
```

## Formato de Logs

Los logs se guardan en `.claude/logs/conversation-YYYYMMDD.log`:

```
==============================================
CLAUDE CODE - CONVERSATION LOG
Started: 2024-12-04 10:30:00
==============================================

[2024-12-04 10:30:00] ========== USER MESSAGE ==========
Session: abc123
Message:
Hola, necesito ayuda con mi código

[2024-12-04 10:30:05] ----- TOOL USED: Read -----
File: /path/to/file.js

[2024-12-04 10:30:15] ========== ASSISTANT RESPONSE ==========
Response:
Aquí está mi análisis del código...
```

## Rotación de Logs

- Archivos por día: `conversation-YYYYMMDD.log`
- Rotación automática al alcanzar 512 KB → `conversation-YYYYMMDD-HHmmss.log`
- Configurable en `$maxLogSizeKB` dentro de `log-conversation.ps1`

## Personalización

### Cambiar tamaño máximo de archivo

```powershell
# En log-conversation.ps1
$maxLogSizeKB = 1024  # Cambiar a 1 MB
```

### Cambiar directorio de logs

```powershell
# En log-conversation.ps1
$logsDir = Join-Path $projectDir "custom-logs-folder"
```

### Agregar nuevos eventos

Edita `hooks/hooks.json`:

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \"$CLAUDE_PLUGIN_DIR/hooks/log-conversation.ps1\"",
            "timeout": 10
          }
        ]
      }
    ]
  }
}
```

**Eventos disponibles:**
- `UserPromptSubmit` - Antes de procesar prompt
- `PreToolUse` - Antes de usar herramienta
- `PostToolUse` - Después de usar herramienta
- `Stop` - Cuando termina la respuesta
- `SessionStart` - Al iniciar sesión
- `Notification` - Para diálogos de permisos

## Solución de Problemas

### El plugin no aparece después de instalar

```bash
# Verificar marketplaces registrados
/plugin marketplace list

# Actualizar el marketplace
/plugin marketplace update nombre-marketplace
```

### Los logs no se crean

1. Verifica que PowerShell esté disponible:
   ```bash
   powershell -Command "echo test"
   ```

2. Comprueba permisos de ejecución:
   ```powershell
   Get-ExecutionPolicy
   # Si es restrictivo:
   Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
   ```

3. Ejecuta Claude Code en modo debug:
   ```bash
   claude --debug
   ```

### Error de timeout en hooks

Aumenta el timeout en `hooks.json`:

```json
{
  "type": "command",
  "command": "...",
  "timeout": 30
}
```

## Seguridad

> **Advertencia**: Los hooks de Claude Code ejecutan comandos arbitrarios en tu sistema. Revisa siempre el código de los plugins antes de instalarlos. Los hooks pueden acceder a cualquier archivo que tu cuenta de usuario pueda alcanzar.

## Crear tu Propio Plugin

Usa este repositorio como plantilla:

1. Crea la estructura:
   ```
   mi-plugin/
   ├── .claude-plugin/
   │   └── plugin.json
   └── hooks/
       ├── hooks.json
       └── mi-script.ps1
   ```

2. Define `plugin.json`:
   ```json
   {
     "name": "mi-plugin",
     "version": "1.0.0",
     "description": "Descripción del plugin",
     "author": "tu-nombre",
     "hooksPath": "./hooks/hooks.json"
   }
   ```

3. Configura hooks en `hooks.json`
4. Crea tus scripts
5. Publica en GitHub como marketplace

## Licencia

MIT

## Autor

practiauy
