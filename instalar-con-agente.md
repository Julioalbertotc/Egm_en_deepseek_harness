# Prompt para que el agente de DSH instale EGM

Pégalo en una **Nueva sesión** de DeepSeek Harness. Cambia `TU_USUARIO` por tu usuario.

```
Instala el plugin Elephant-Goldfish Model (EGM) de https://github.com/google/elephant-goldfish-model
Hazlo directo, sin analizar de más.

1. Clona el repo COMPLETO (no solo la carpeta skills/) en ~/.dsh/egm, porque las skills necesitan las carpetas agents/, resources/ y docs/ en la raíz del plugin.

2. Lee docs/harnesses.md del repo para ver cómo se usa en un harness que no está en la lista oficial.

3. Registra la carpeta skills/ del repo como directorio de skills de DSH: agrega al FINAL del cordis.patch.yml de mi perfil una entrada nueva con estos datos, sin tocar ni borrar las existentes:
   - id: egm-skills
   - name: "@deepseek-ai/dsh-skill-filesystem"
   - config: providerName: egm, includeDefaultRoots: false, customSkillDirs apuntando a la carpeta skills/ del clon.
   Antes de aplicar, dime qué archivo vas a tocar, confirma que no existe ya un id egm-skills y muéstrame el cambio exacto.

4. Verifica que las skills egm-brainstorm, egm-draft, egm-review y egm-review-loop se descubren. Si el name: del SKILL.md no coincide con el de la carpeta, avísame.

5. Comprueba que puedes crear subagentes con contexto limpio: lanza uno y pídele que diga qué sabe de esta conversación. Si hereda el contexto, avísame, porque entonces las revisiones Goldfish no sirven.

6. Crea (o actualiza sin borrar lo que haya) el AGENTS.md en la raíz del espacio de trabajo actual con las reglas de AGENTS.egm.md de este repo: rutas absolutas, despacho directo sin synthesizer, Goldfish con contexto limpio, y "EGM artifact directory: docs/designs".

7. Al final dime en pocas líneas qué quedó instalado, qué no funcionó y cómo reinicio DSH para usar /egm-brainstorm.

No instales nada más ni modifiques archivos fuera de lo necesario para esto.
```

Después: cierra DSH por completo desde la bandeja del sistema, ábrelo, abre una sesión nueva y escribe `/` para ver si salen las skills `egm-*`.
