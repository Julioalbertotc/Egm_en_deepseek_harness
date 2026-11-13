## EGM

Reglas para usar Elephant-Goldfish Model (EGM) en DeepSeek Harness.
Cambia `C:\Users\TU_USUARIO\.dsh\egm` por la ruta donde clonaste EGM.

- Las skills de EGM están en `C:\Users\TU_USUARIO\.dsh\egm`. Usa SIEMPRE rutas absolutas para `agents\` y `resources\` (ejemplo: `C:\Users\TU_USUARIO\.dsh\egm\agents\design_critic.md`). Nunca rutas relativas ni `${CLAUDE_PLUGIN_ROOT}`.
- Los subagentes solo pueden bajar 1 nivel de profundidad. En `/egm-review` y `/egm-brainstorm` NO uses el synthesizer. Usa despacho directo: tú (el agente principal) lanzas cada Goldfish como subagente general-purpose y le dices que lea primero su persona en `C:\Users\TU_USUARIO\.dsh\egm\agents\<persona>.md`.
- Cada Goldfish debe lanzarse con contexto limpio: solo pásale el documento a revisar, las rutas absolutas y su persona. Nada de la conversación.
- EGM artifact directory: docs/designs
