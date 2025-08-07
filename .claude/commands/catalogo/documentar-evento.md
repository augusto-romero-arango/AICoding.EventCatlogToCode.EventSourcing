Documenta un evento nuevo con workflow interactivo controlado por state machine.

**Parámetros:**
- Nombre evento: $ARGUMENTS
- Nombre aplicacion: $ARGUMENTS
- Nombre subdominio: $ARGUMENTS  
- Nombre dominio: $ARGUMENTS
- Summary: $ARGUMENTS

## State Machine Workflow

Estados del workflow:
- `init` → Inicialización y validaciones
- `commandhandler_pending` → CommandHandler listo para crear
- `commandhandler_done` → CommandHandler creado exitosamente
- `event_pending` → Evento listo para crear
- `event_done` → Evento creado exitosamente  
- `command_pending` → Comando listo para crear
- `command_done` → Workflow completado

## Implementación con Control de Estado

### Fase 1: Inicialización (Estado: init)
1. **Crear TodoWrite state machine** con todos los estados del workflow
2. **Solicitar parámetros faltantes (UNA PREGUNTA A LA VEZ):**
   - Si falta `Nombre evento`: PREGUNTAR al usuario y ESPERAR respuesta
   - Si falta `Nombre aplicacion`: PREGUNTAR al usuario y ESPERAR respuesta (sugerir si solo hay una)
   - Si falta `Nombre subdominio`: PREGUNTAR al usuario y ESPERAR respuesta
   - Si falta `Nombre dominio`: PREGUNTAR al usuario y ESPERAR respuesta  
   - Si falta `Summary`: PREGUNTAR al usuario y ESPERAR respuesta
3. **Validar con usuario INDIVIDUALMENTE** todos los nombres deducidos (comando, commandHandler)
4. Verificar que no exista el evento
5. **NUNCA inventar datos** - siempre preguntar o confirmar
6. **Transición a:** `commandhandler_pending` SOLO cuando todos los datos estén confirmados

### Fase 2: CommandHandler (Estado: commandhandler_pending)
7. **Ejecutar spec:** `.claude/specs/eventcatalog/crear-commandHandler.md` completo
   - SEGUIR TODOS LOS PASOS sin saltar ninguno
   - PREGUNTAR por cualquier dato faltante del spec (UNA PREGUNTA A LA VEZ)
   - ESPERAR respuesta antes de continuar
8. **Validar resultado** antes de continuar
9. **Actualizar TodoWrite:** marcar como `commandhandler_done`
10. **Transición a:** `event_pending`

### Fase 3: Evento (Estado: event_pending)  
11. **Validar dependencia:** CommandHandler debe existir
12. **CONTROL DE DEPENDENCIAS OBLIGATORIAS:** Antes de crear el evento
    - **PREGUNTAR EXPLÍCITAMENTE:** "¿Qué propiedades debe tener el evento {nombre-evento}?"
    - **IDENTIFICAR objetos complejos** en la respuesta del usuario
    - **Para CADA objeto complejo:**
      - DETENER el workflow del evento
      - Verificar si existe como entidad usando LS
      - Si NO existe: Ejecutar COMPLETAMENTE `crear-entidad.md` con `aggregateRoot: false`
      - ESPERAR confirmación de entidad creada antes de continuar
    - Solo continuar cuando TODAS las entidades estén creadas
13. **Ejecutar spec:** `.claude/specs/eventcatalog/crear-evento.md` completo
    - SEGUIR TODOS LOS PASOS sin saltar ninguno (incluyendo validaciones de entidades)
    - PREGUNTAR por cualquier dato faltante del spec (UNA PREGUNTA A LA VEZ)
    - ESPERAR respuesta antes de continuar
14. **Validar resultado** antes de continuar
15. **Actualizar TodoWrite:** marcar como `event_done`
16. **Transición a:** `command_pending`

### Fase 4: Comando (Estado: command_pending)
17. **Validar dependencias:** CommandHandler y Evento deben existir
18. **CONTROL DE DEPENDENCIAS OBLIGATORIAS:** Antes de crear el comando
    - **PREGUNTAR EXPLÍCITAMENTE:** "¿Qué propiedades debe tener el comando {nombre-comando}?"
    - **NUNCA asumir** que las propiedades son iguales al evento
    - **IDENTIFICAR objetos complejos** en la respuesta del usuario
    - **Para CADA objeto complejo:**
      - DETENER el workflow del comando
      - Verificar si existe como entidad usando LS
      - Si NO existe: Ejecutar COMPLETAMENTE `crear-entidad.md` con `aggregateRoot: false`
      - ESPERAR confirmación de entidad creada antes de continuar
    - Solo continuar cuando TODAS las entidades estén creadas
19. **Ejecutar spec:** `.claude/specs/eventcatalog/crear-comando.md` completo
    - SEGUIR TODOS LOS PASOS sin saltar ninguno (incluyendo validaciones de entidades)
    - PREGUNTAR por cualquier dato faltante del spec (UNA PREGUNTA A LA VEZ)
    - ESPERAR respuesta antes de continuar
20. **Actualizar TodoWrite:** marcar como `command_done`
21. **Estado final:** Workflow completado

## Control de Ejecución
- ✅ UNA PREGUNTA A LA VEZ - nunca hacer múltiples preguntas
- ✅ ESPERAR respuesta antes de continuar
- ✅ Validación de dependencias antes de cada paso
- ✅ Estado persistente visible en TodoWrite
- ✅ Rollback automático en caso de error
- ✅ Ejecución completa de specs internos SIN SALTAR PASOS
- ✅ Workflow minucioso y detallado
- ✅ **OBLIGATORIO:** Crear entidades para objetos complejos ANTES de crear mensajes
- ✅ **OBLIGATORIO:** Preguntar explícitamente propiedades sin asumir
- ✅ **OBLIGATORIO:** Validar completitud antes de marcar como completado

## Estados TodoWrite
```
☐ [init] Inicializar workflow y validar parámetros
☐ [commandhandler_pending] Ejecutar spec crear-commandHandler.md
☐ [commandhandler_done] CommandHandler creado exitosamente  
☐ [event_pending] Ejecutar spec crear-evento.md
☐ [event_done] Evento creado exitosamente
☐ [command_pending] Ejecutar spec crear-comando.md
☐ [command_done] ✅ Workflow completado
```

## ⚠️ REGLAS CRÍTICAS DE EJECUCIÓN

### Manejo de Datos
1. **NUNCA inventar información** - Todo debe ser proporcionado por el usuario
2. **SIEMPRE preguntar** por parámetros faltantes antes de continuar
3. **VALIDAR con el usuario** cualquier nombre deducido o sugerido
4. **CONFIRMAR** cada paso antes de ejecutar

### Formato de Preguntas
- "Necesito el **nombre del evento**. ¿Cuál quieres usar?"
- "El **nombre de la aplicación** no está especificado. ¿Cuál es?"
- "Sugiero usar `{nombre}` para el comando. ¿Estás de acuerdo?"
- **IMPORTANTE**: Solo hacer UNA pregunta por mensaje
- **IMPORTANTE**: Esperar respuesta antes de hacer la siguiente pregunta

### Bloqueo de Ejecución
- **DETENER** el workflow si falta cualquier parámetro
- **NO CONTINUAR** hasta recibir confirmación del usuario
- **PREGUNTAR EXPLÍCITAMENTE** por cada dato faltante
- **OBLIGATORIO:** Crear entidades de objetos complejos ANTES de continuar con mensajes
- **OBLIGATORIO:** Preguntar propiedades de cada elemento sin asumir
- **OBLIGATORIO:** Ejecutar validación final antes de marcar completado