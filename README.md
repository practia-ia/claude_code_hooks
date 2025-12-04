# Claude Code Hooks - Conversation Logger

Plugin para Claude Code que registra automáticamente las conversaciones en archivos de log locales con rotación automática.

## Descripción

Este plugin captura y guarda:
- **Mensajes del usuario**: Cada prompt enviado a Claude
- **Uso de herramientas**: Registro de las herramientas utilizadas (Read, Write, Edit, Bash, Grep, etc.)
- **Respuestas del asistente**: El texto de respuesta generado por Claude

Los logs se guardan en `.claude/logs/` dentro del proyecto con rotación automática cuando alcanzan 512 KB.

## Requisitos

- **Claude Code** instalado y configurado
- **Windows** con PowerShell 5.1 o superior
- **Git** (para clonar el repositorio)

## Instalación

### 1. Clonar el repositorio

```bash
git clone <URL_DEL_REPOSITORIO> claude_code_hooks
```

### 2. Configurar el plugin en Claude Code

Hay dos opciones para instalar el plugin:

#### Opción A: Instalación global (para todos los proyectos)

Edita el archivo de configuración global de Claude Code:

**Windows:**
```
%APPDATA%\Claude\settings.json
```

**macOS/Linux:**
```
~/.config/claude/settings.json
```

Agrega la configuración del plugin:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \"C:\\ruta\\completa\\claude_code_hooks\\conversation-logger\\hooks\\log-conversation.ps1\"",
            "timeout": 10
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \"C:\\ruta\\completa\\claude_code_hooks\\conversation-logger\\hooks\\log-conversation.ps1\"",
            "timeout": 10
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \"C:\\ruta\\completa\\claude_code_hooks\\conversation-logger\\hooks\\log-conversation.ps1\"",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

> **Importante:** Reemplaza `C:\\ruta\\completa\\claude_code_hooks` con la ruta real donde clonaste el repositorio.

#### Opción B: Instalación por proyecto

Copia la carpeta `conversation-logger` dentro del directorio `.claude/` de tu proyecto:

```bash
# Desde el directorio de tu proyecto
mkdir -p .claude
cp -r /ruta/a/claude_code_hooks/conversation-logger .claude/
```

Luego crea o edita `.claude/settings.local.json` en tu proyecto:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \".claude/conversation-logger/hooks/log-conversation.ps1\"",
            "timeout": 10
          }
        ]
      }
    ],
    "PostToolUse": [
      {
        "matcher": "*",
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \".claude/conversation-logger/hooks/log-conversation.ps1\"",
            "timeout": 10
          }
        ]
      }
    ],
    "Stop": [
      {
        "hooks": [
          {
            "type": "command",
            "command": "powershell -ExecutionPolicy Bypass -File \".claude/conversation-logger/hooks/log-conversation.ps1\"",
            "timeout": 15
          }
        ]
      }
    ]
  }
}
```

### 3. Verificar la instalación

1. Abre Claude Code en cualquier proyecto
2. Envía un mensaje cualquiera
3. Verifica que se haya creado el archivo de log en `.claude/logs/conversation-YYYYMMDD.log`

## Estructura del proyecto

```
claude_code_hooks/
├── README.md
├── conversation-logger/
│   ├── .claude-plugin/
│   │   └── plugin.json          # Metadata del plugin
│   └── hooks/
│       ├── hooks.json           # Configuración de hooks
│       └── log-conversation.ps1 # Script principal
└── .claude/
    └── settings.local.json      # Configuración local
```

## Formato de logs

Los logs se guardan con el siguiente formato:

```
==============================================
CLAUDE CODE - CONVERSATION LOG
Started: 2024-01-15 10:30:00
==============================================

[2024-01-15 10:30:00] ========== USER MESSAGE ==========
Session: abc123
Message:
Hola, necesito ayuda con mi código

[2024-01-15 10:30:05] ----- TOOL USED: Read -----
File: /path/to/file.js

[2024-01-15 10:30:15] ========== ASSISTANT RESPONSE ==========
Response:
Aquí está mi análisis del código...
```

## Rotación de logs

- Los archivos de log se crean por día: `conversation-YYYYMMDD.log`
- Cuando un archivo alcanza 512 KB, se rota automáticamente a `conversation-YYYYMMDD-HHmmss.log`
- Puedes modificar el tamaño máximo editando `$maxLogSizeKB` en `log-conversation.ps1`

## Personalización

### Cambiar directorio de logs

Por defecto los logs se guardan en `.claude/logs/` del proyecto. Puedes cambiar esto modificando la variable `$logsDir` en `log-conversation.ps1`.

### Cambiar tamaño máximo de archivo

Edita `$maxLogSizeKB` en `log-conversation.ps1` (valor por defecto: 512 KB).

### Modificar formato de log

El script `log-conversation.ps1` contiene toda la lógica de formateo. Puedes personalizarlo según tus necesidades.

## Solución de problemas

### Los logs no se crean

1. Verifica que PowerShell esté disponible: `powershell -Command "echo test"`
2. Comprueba los permisos de ejecución: `Get-ExecutionPolicy`
3. Si es restrictivo, ejecuta: `Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser`

### Error de permisos

Asegúrate de que Claude Code tenga permisos para ejecutar scripts de PowerShell. El flag `-ExecutionPolicy Bypass` debería manejar esto.

### Los hooks no se ejecutan

1. Reinicia Claude Code después de modificar la configuración
2. Verifica que la ruta al script sea correcta
3. Revisa los logs de Claude Code para errores

## Licencia

MIT

## Autor

practiauy
