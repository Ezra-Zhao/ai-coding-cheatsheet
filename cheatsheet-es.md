# Guía rápida de asistentes de IA para programar (Español)

> Verificado el 2026-10-06: cada comando, atajo y opción se cotejó con la documentación oficial. Lo que no se pudo verificar se eliminó.
> Documentación oficial: Claude Code <https://docs.anthropic.com/en/docs/claude-code> · Codex <https://developers.openai.com/codex> · Cursor <https://docs.cursor.com>
> Todo cambia rápido; ante la duda, manda la documentación oficial.

---

## 1. Claude Code (asistente de Anthropic en la terminal)

### 1.1 Iniciar y manejar sesiones

| Comando | Descripción | Ejemplo |
|---|---|---|
| `claude` | Inicia una sesión interactiva | `claude` |
| `claude "pregunta"` | Inicia con un prompt inicial | `claude "explica la estructura del proyecto"` |
| `claude -p "pregunta"` | Una sola respuesta y sale (`--print` es lo mismo) | `claude -p "¿qué hace esta función?"` |
| `cat f.log \| claude -p "analiza"` | Analiza lo que le pases por pipe | `cat err.log \| claude -p "encuentra el error"` |
| `claude -c` | Continúa la última sesión de este directorio | `claude -c` |
| `claude -r "nombre" "sigue"` | Retoma una sesión por nombre o ID | `claude -r "auth-refactor" "sigue"` |
| `claude update` | Actualiza a la última versión | `claude update` |
| `claude doctor` | Diagnostica la instalación y la configuración | `claude doctor` |
| `claude auth login` | Inicia sesión en tu cuenta | `claude auth login` |
| `claude --version` | Muestra la versión | `claude --version` |

### 1.2 Parámetros comunes al iniciar

| Parámetro | Descripción | Ejemplo |
|---|---|---|
| `--model <nombre>` | Elige el modelo | `claude --model sonnet` |
| `--permission-mode <modo>` | Modo de permisos: `plan` / `acceptEdits` / `auto` / `manual` / `dontAsk` / `bypassPermissions` | `claude --permission-mode plan` |
| `--dangerously-skip-permissions` | Se salta todas las confirmaciones (solo en entornos de confianza) | — |
| `--allowedTools "Bash(git *)" "Read"` | Estas herramientas corren sin pedir permiso | `claude --allowedTools "Bash(git *)" "Read"` |
| `--add-dir <ruta>` | Directorios extra de trabajo | `claude --add-dir ../shared-lib` |
| `--agent <nombre>` | Inicia con un subagente específico | `claude --agent reviewer` |

### 1.3 Atajos y comandos slash en la sesión

| Entrada | Descripción |
|---|---|
| `Shift+Tab` | Cambia el modo de permisos (incluye el modo plan) |
| `Esc` | Interrumpe lo que está haciendo |
| `/help` | Muestra todos los comandos (la lista más confiable) |
| `/init` | Genera un borrador de CLAUDE.md para el proyecto |
| `/model` | Cambia de modelo |
| `/context` | Muestra el uso del contexto y los archivos de memoria cargados |
| `/doctor` | Diagnostica en la sesión y puede arreglar la configuración |
| `/cd <directorio>` | Cambia el directorio de trabajo de la sesión |
| `/usage` (alias `/cost`) | Muestra tu consumo |
| `/review` | Revisa código (skill incluida) |
| `/debug` `/batch` `/loop` `/verify` `/run` | Skills incluidas: depurar, tareas en lote, bucles, verificar, ejecutar |

### 1.4 CLAUDE.md: la memoria del proyecto

| Ubicación | Alcance |
|---|---|
| `./CLAUDE.md` o `./.claude/CLAUDE.md` | Del proyecto, se comparte en el repo con el equipo |
| `~/.claude/CLAUDE.md` | Personal, vale para todos tus proyectos |
| `./CLAUDE.local.md` | Preferencias locales, mételo en el `.gitignore` |
| `.claude/rules/*.md` | Reglas por rutas de archivo (frontmatter `paths`) |
| `AGENTS.md` | También lo lee, puede convivir con CLAUDE.md |

Consejos:

- Escribe instrucciones concretas y verificables ("antes de commitear corre `npm test`"), nada de frases vacías
- Máximo 200 líneas por archivo; los procedimientos largos van en un skill
- Usa `@ruta` para importar otros archivos dentro de CLAUDE.md
- `/init` genera el borrador y luego lo pules a mano

### 1.5 Skills y MCP

- Un skill es un archivo `SKILL.md`: personal en `~/.claude/skills/<nombre>/SKILL.md`, del proyecto en `.claude/skills/<nombre>/SKILL.md`
- Se invoca con `/<nombre>` en la sesión; Claude también lo activa solo cuando aplica
- Skills incluidas: `/doctor` `/code-review` `/batch` `/debug` `/loop` `/claude-api` `/verify` `/run`
- `claude mcp`: gestiona servidores MCP (ver, agregar, autenticar) para conectar herramientas externas
- `claude plugin`: gestiona plugins

### 1.6 Flujo de trabajo

1. Cambios grandes, primero en plan: inicia con `--permission-mode plan` o cambia con `Shift+Tab`; que la IA proponga el plan y tú lo apruebes antes de tocar código
2. Avanza por pasos y revisa el `git diff` en cada uno
3. Antes de que toque código, pídele que repita lo que entendió: "dime qué archivos vas a cambiar"
4. Convierte lo repetitivo en un skill y deja de explicarlo cada vez
5. Una tarea, una sesión nueva; no acumules todo en la misma

---

## 2. Codex CLI (agente de OpenAI en la terminal)

Instalación: `npm install -g @openai/codex`

### 2.1 Comandos

| Comando | Descripción | Ejemplo |
|---|---|---|
| `codex` | Inicia una sesión interactiva | `codex` |
| `codex exec "tarea"` | Ejecuta sin interfaz, ideal para scripts y CI | `codex exec "arregla los tests que fallan"` |
| `codex resume` | Retoma una sesión anterior | `codex resume` |
| `codex mcp` | Gestiona servidores MCP | `codex mcp` |
| `codex cloud` | Manda la tarea a ejecutarse en la nube | `codex cloud` |
| `codex --image img.png` | Adjunta una imagen al primer prompt (ej. captura de un error) | `codex --image err.png "analiza este error"` |
| `codex --search` | Activa la búsqueda web en esta ejecución | `codex --search "busca el uso actual de la API"` |
| `codex completion` | Genera autocompletado para tu shell | `codex completion` |

### 2.2 Aprobación y sandbox (lo distintivo de Codex: "qué puede tocar" y "cuándo te pregunta" van por separado)

| Parámetro | Descripción | Ejemplo |
|---|---|---|
| `-a, --ask-for-approval <política>` | Cuándo se detiene a preguntarte: `on-request` (por defecto) / `never` | `codex -a never exec "corre los tests y arregla"` |
| `-s, --sandbox <modo>` | Qué puede tocar: `read-only` / `workspace-write` / `danger-full-access` | `codex -s read-only "explica este código"` |
| `--dangerously-bypass-approvals-and-sandbox` | Abre todo, sin aprobación ni sandbox (solo en entornos aislados de confianza) | — |
| `--profile <nombre>` | Usa un perfil de configuración | `codex --profile work` |

Dentro de la sesión, `/permissions` muestra y ajusta los límites de esa ejecución.

### 2.3 AGENTS.md y configuración

- Pon un `AGENTS.md` en la raíz del proyecto: comandos de build, convenciones, prohibiciones; Codex lo lee solo cada vez
- Configuración de usuario en `~/.codex/config.toml`; la del proyecto en `.codex/config.toml` (requiere confiar en el proyecto)
- Claves comunes: `model`, `approval_policy = "on-request"`, `sandbox_mode`

### 2.4 Plugin para el IDE

- Hay plugin para VS Code: conversas en el editor, ves diffs y compartes tu selección
- El plugin y el CLI comparten el mismo `AGENTS.md`, la configuración y los servidores MCP

---

## 3. Cursor (IDE nativo de IA)

### 3.1 Atajos principales (Mac: Cmd, Windows/Linux: Ctrl)

| Atajo | Descripción |
|---|---|
| `Cmd/Ctrl+K` | Edición en línea: selecciona código y dile cómo cambiarlo |
| `Cmd/Ctrl+L` | Abre el chat: tu selección entra sola al contexto |
| `Cmd/Ctrl+I` | Abre el Agent/Composer: cambios automáticos en varios archivos |
| `Cmd/Ctrl+Shift+L` | Añade la selección al chat actual |
| `Cmd/Ctrl+Enter` | Enviar; si el agente está ocupado puedes encadenar preguntas |
| `Tab` | Acepta la sugerencia de la IA (Cursor Tab, se activa en ajustes) |
| `Esc` | Cierra la sugerencia / interrumpe a la IA |

### 3.2 Menciones @ (para darle contexto a la IA)

| Sintaxis | Descripción |
|---|---|
| `@Files` / `@Folders` | Referencia archivos / carpetas |
| `@Codebase` | Búsqueda semántica en todo el código |
| `@Docs` | Referencia documentación |
| `@Web` | Búsqueda en la web |
| `@Git` | Referencia historial y diffs de git |
| `@<archivo>` | Mencionar el archivo por nombre también vale |

### 3.3 Project Rules (`.cursor/rules/`)

- Una regla por archivo `.mdc`: `description` + `globs` (a qué archivos aplica) + instrucciones
- Cuatro formas de activarse: Always (siempre) / Auto Attached (entra sola por globs) / Agent Requested (la IA decide) / Manual (solo con @)
- Tus reglas personales van en Ajustes → Rules
- Las del equipo se commitean al repo; las solo tuyas, no

### 3.4 Modos Agent / Plan

- Ask: solo pregunta, no toca nada; ideal para entender código primero
- Agent: edita archivos y corre comandos solo; confirmas cada diff antes de aceptar
- Plan: propone el plan de implementación y lo ejecutas cuando lo apruebas
- Se cambian encima del cuadro de texto; la interfaz cambia seguido, manda la documentación oficial

### 3.5 Autocompletado con Tab

- Búscalo como "Cursor Tab" en ajustes para activarlo; `Tab` acepta, `Esc` rechaza
- Si te estorba, apágalo o déjalo solo en ciertos lenguajes

---

## 4. General: .gitignore / prompts / seguridad

### 4.1 .gitignore: lo esencial

```gitignore
.env*             # claves y tokens
*.pem *.key       # llaves privadas y certificados
node_modules/     # dependencias
dist/ build/      # salidas de build
.DS_Store
CLAUDE.local.md   # memoria local de IA, no se commitea
```

Cursor además tiene `.cursorignore`: ahí van los archivos que no quieres que la IA indexe.

### 4.2 Cómo pedirle bien (prompts)

1. Primero el objetivo, luego el cómo: "quiero rate limiting en el login, toca estos 3 archivos; dime tu plan primero"
2. Pide algo verificable: "cuando termines corre los tests y pega el resultado"
3. De a poco: una tarea por vez
4. Dale la lista negra: "no toques el schema de la base, no instales dependencias nuevas"
5. En tareas complejas, que proponga el plan y tú lo apruebas antes de que toque código

### 4.3 Seguridad: líneas rojas

- Claves, tokens, llaves privadas y datos de clientes jamás van en el chat
- `--dangerously-skip-permissions` / `--dangerously-bypass-approvals-and-sandbox` solo en entornos aislados de confianza
- Las API keys van en variables de entorno (ej. `ANTHROPIC_API_KEY`), nunca en el código ni en el prompt
- Comandos destructivos o de deploy que proponga la IA: míralos antes de dar Enter
- En repos públicos no subas `.env`, direcciones internas ni datos reales de usuarios

---

MIT License · Copyright (c) 2026 Guangyi Zhao
