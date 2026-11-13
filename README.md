# EGM en DeepSeek Harness

Guía para instalar el plugin **Elephant-Goldfish Model (EGM)** de Google en **DeepSeek Harness (DSH)**, aunque DSH no esté en la lista oficial de EGM.

> **Aviso:** esto es una guía de la comunidad. No es oficial de Google ni de DeepSeek. EGM dice que [no es un producto con soporte oficial de Google](https://github.com/google/elephant-goldfish-model), y DSH está en *developer preview* (pueden venir cambios que rompan cosas).

## ¿Qué es EGM?

Un plugin open source (Apache 2.0) para agentes de código. La idea:

- **Elefante:** el agente con todo el contexto del proyecto. Platica contigo y redacta la especificación. No escribe código.
- **Goldfish (pez dorado):** subagentes sin memoria que revisan la especificación sin ver la conversación. Si no la entienden solos, es que está ambigua.

Comandos principales: `/egm-brainstorm`, `/egm-draft`, `/egm-review`.

Repo oficial: <https://github.com/google/elephant-goldfish-model>

## ¿Por qué esta guía?

El repo oficial trae instalación para Antigravity, Claude Code y Codex. DSH no aparece, pero sí tiene lo que EGM necesita: **skills** y **subagentes con contexto limpio**. Aquí se documenta cómo hacerlo funcionar.

## Probado en

| Componente | Versión / detalle |
|---|---|
| DeepSeek Harness | App de escritorio, Windows, perfil `desktop` |
| EGM | v0.1.1 (commit `270b103`) |
| Modelo | DeepSeek-V4.1-Flash |

Si lo pruebas en otro sistema, perfil o versión, abre un issue o PR con tu resultado.

## Requisitos

- DeepSeek Harness instalado.
- Git.
- Que DSH pueda lanzar **subagentes** (sin eso EGM no puede hacer las revisiones Goldfish).

## Instalación

### Opción 1: que el agente de DSH lo haga

1. Abre una **Nueva sesión** en DSH.
2. Copia el contenido de [`prompts/instalar-con-agente.md`](prompts/instalar-con-agente.md) y pégalo.
3. Revisa y aprueba el cambio de configuración que te muestre antes de aplicarlo.
4. Cierra DSH por completo (desde la bandeja del sistema, *Salir*) y vuelve a abrirlo.

### Opción 2: a mano

**1. Clona el repo completo** (no solo la carpeta `skills/`):

```bash
git clone https://github.com/google/elephant-goldfish-model ~/.dsh/egm
```

Tiene que ser completo porque las skills leen archivos de `agents/` (los revisores) y `resources/` (los estándares de documentos) que están en la raíz del plugin.

**2. Registra la carpeta de skills en tu perfil.** En la app de escritorio el archivo es:

```
~/.dsh/profiles/desktop/cordis.patch.yml
```

Agrega esta entrada **al final de la lista**, sin tocar las que ya existen (ver [`config/cordis.patch.egm.yml`](config/cordis.patch.egm.yml)):

```yaml
- id: egm-skills
  name: "@deepseek-ai/dsh-skill-filesystem"
  config:
    providerName: egm
    includeDefaultRoots: false
    customSkillDirs:
      - "C:/Users/TU_USUARIO/.dsh/egm/skills"
```

Cambia la ruta por la tuya (en Linux/macOS sería algo como `/home/tu_usuario/.dsh/egm/skills`).

Por qué esa configuración:

- `id: egm-skills`: un id propio, para no chocar con la entrada `skill-filesystem` que pueda traer el perfil base.
- `includeDefaultRoots: false`: evita que este proveedor vuelva a escanear las carpetas de skills que DSH ya lee.
- No lo probé en perfiles distintos a `desktop` (web, headless). Ahí busca el `cordis.patch.yml` de tu perfil.

**3. Reglas para el agente (recomendado).** Copia [`AGENTS.egm.md`](AGENTS.egm.md) como `AGENTS.md` en la raíz de tu espacio de trabajo (o agrégalo al final si ya tienes uno). Ajusta las rutas a tu usuario. DSH solo lee el `AGENTS.md` del proyecto donde estás trabajando, así que hay que copiarlo en cada espacio donde uses EGM.

**4. Reinicia DSH por completo** y abre una sesión nueva.

## Verificar que quedó

1. En una sesión nueva escribe `/`.
2. Deben aparecer: `egm-bootstrap`, `egm-brainstorm`, `egm-draft`, `egm-drift-report`, `egm-resolve`, `egm-review`, `egm-review-loop`.
3. Revisa que tus otras skills de antes sigan apareciendo.
4. Prueba `/egm-brainstorm` con una idea chica.

Las skills de EGM traen `disable-model-invocation: true`: **no salen en el catálogo del modelo**, solo como comandos con `/`. Es lo esperado.

## Límites conocidos

- **Profundidad de subagentes = 1.** Un subagente no puede lanzar otros. Por eso en `/egm-review` hay que usar despacho directo (el agente principal lanza cada Goldfish) y no el *synthesizer*. EGM lo contempla como alternativa en su `docs/harnesses.md`.
- **Sin personas nativas.** Los revisores se lanzan como subagente *general-purpose* al que se le dice que lea primero `agents/<persona>.md`.
- **`${CLAUDE_PLUGIN_ROOT}` no se expande en DSH.** Usa rutas absolutas para `agents/` y `resources/`.
- **Contexto limpio:** si algún día tu versión de DSH hace que los subagentes hereden la conversación, la revisión Goldfish deja de servir. Pruébalo: lanza un subagente y pregúntale qué sabe de la conversación. Debe decir que nada.

## Problemas comunes

| Problema | Qué revisar |
|---|---|
| No salen las `egm-*` con `/` | Que la entrada siga en `cordis.patch.yml`, que la ruta exista, y que cerraste DSH por completo antes de reabrir |
| Se perdieron tus otras skills | Que el id sea `egm-skills` y no `skill-filesystem`, y que tenga `includeDefaultRoots: false` |
| Una skill no aparece | DSH toma el nombre del `name:` del `SKILL.md`, no de la carpeta. Deben coincidir |
| Error leyendo `agents/` o `resources/` | Usa rutas absolutas (ver `AGENTS.egm.md`) |
| El perfil se resetea | La app es dueña del perfil `desktop`. Si cambias ajustes y reescribe el archivo, vuelve a revisar la entrada |

## Créditos

- **Elephant-Goldfish Model:** Google, Apache 2.0. <https://github.com/google/elephant-goldfish-model>
- **DeepSeek Harness:** DeepSeek AI, MIT.
- Esta guía no incluye código de EGM, solo instrucciones de instalación.

**Esta guía:** © 2026 Julio Alberto Trejo Cruz, licencia Apache 2.0 (ver [LICENSE](LICENSE)).
