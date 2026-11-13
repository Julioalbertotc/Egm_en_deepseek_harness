# Prompt para que el agente de DSH instale EGM

Pégalo en una **Nueva sesión** de DeepSeek Harness, dentro del espacio de trabajo donde quieras usar EGM. No necesitas cambiar nada: el agente calcula las rutas con tu usuario real.

```
Instala el plugin Elephant-Goldfish Model (EGM) de https://github.com/google/elephant-goldfish-model
Hazlo directo, sin analizar de más.

1. Clona el repo COMPLETO (no solo la carpeta skills/) en ~/.dsh/egm, porque las skills necesitan las carpetas agents/, resources/ y docs/ en la raíz del plugin. Si ~/.dsh/egm ya existe, no lo borres: dímelo y espera.

2. Lee docs/harnesses.md del repo para ver cómo se usa en un harness que no está en la lista oficial.

3. Registra la carpeta skills/ del repo como directorio de skills de DSH. Mi perfil es el de la app de escritorio: ~/.dsh/profiles/desktop/cordis.patch.yml.
   Agrega al FINAL de la lista una entrada nueva, DENTRO de un bloque "- insert:" (sin insert, DSH la descarta en silencio porque cree que modificas una fila que ya existe). No toques ni borres las entradas existentes. Formato exacto, con 2 espacios por nivel, sin tabulaciones y con barras "/" en la ruta:

   - insert:
       - id: egm-skills
         name: "@deepseek-ai/dsh-skill-filesystem"
         config:
           providerName: egm
           includeDefaultRoots: false
           customSkillDirs:
             - "<RUTA_ABSOLUTA_DEL_CLON>/skills"

   Reemplaza <RUTA_ABSOLUTA_DEL_CLON> por la ruta absoluta real del clon (por ejemplo C:/Users/<mi_usuario>/.dsh/egm).
   Antes de aplicar: haz una copia de respaldo cordis.patch.yml.bak en la misma carpeta, dime qué archivo vas a tocar, confirma que no existe ya un id egm-skills y muéstrame el diff exacto. Espera mi confirmación.
   Después de aplicar: relee el archivo y confirma que es YAML válido, que solo hay un egm-skills, que está dentro de insert y que las demás entradas quedaron intactas.

4. Verifica las skills leyendo el disco, no con la herramienta skill (las skills de EGM son solo para el usuario, así que el modelo no las ve en su catálogo y eso es normal). Comprueba que existen las carpetas egm-bootstrap, egm-brainstorm, egm-draft, egm-drift-report, egm-resolve, egm-review y egm-review-loop dentro de skills/, y que el SKILL.md de cada una empieza con "---" en la primera línea y tiene name: en kebab-case. Si el name: no coincide con el nombre de la carpeta, avísame.

5. Comprueba que puedes crear subagentes con contexto limpio: lanza uno y pídele que diga qué sabe de esta conversación. Si hereda el contexto, avísame, porque entonces las revisiones Goldfish no sirven.

6. Crea (o actualiza sin borrar lo que haya) el AGENTS.md en la raíz del espacio de trabajo actual, agregando al final esta sección con la ruta absoluta real del clon:

## EGM
- Las skills de EGM están en <RUTA_ABSOLUTA_DEL_CLON>. Usa SIEMPRE rutas absolutas para agents\ y resources\. Nunca rutas relativas ni ${CLAUDE_PLUGIN_ROOT}.
- Los subagentes solo pueden bajar 1 nivel de profundidad. En /egm-review y /egm-brainstorm NO uses el synthesizer. Usa despacho directo: tú (el agente principal) lanzas cada Goldfish como subagente general-purpose y le dices que lea primero su persona en <RUTA_ABSOLUTA_DEL_CLON>\agents\<persona>.md.
- Cada Goldfish debe lanzarse con contexto limpio: solo pásale el documento a revisar, las rutas absolutas y su persona. Nada de la conversación.
- EGM artifact directory: docs/designs

7. Al final dime en pocas líneas qué quedó instalado, qué no funcionó y cómo reinicio DSH para usar /egm-brainstorm.

No instales nada más ni modifiques archivos fuera de lo necesario para esto.
```

## Después de que termine

1. Cierra DSH por completo: clic derecho en el ícono de la bandeja del sistema → **Salir**.
2. Abre el Administrador de tareas (Ctrl+Shift+Esc) y comprueba que no queda ningún proceso de DeepSeek.
3. Abre DSH, entra al espacio de trabajo donde pusiste el `AGENTS.md` y crea una **Nueva sesión**.
4. Escribe `/egm`. Deben salir las 7 skills `egm-*`.

Si no salen y no hay ningún error, lo más probable es que la entrada no esté dentro de `- insert:`. Revisa [la sección de problemas comunes del README](../README.md#problemas-comunes).
