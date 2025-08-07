Crea un un evento en la sección events del commandHandler de EventCatalog dentro de una aplicación existente.

**Nombre aplicacion:** $ARGUMENTS
**Nombre commandHandler:** $ARGUMENTS
**Nombre subdominio:** $ARGUMENTS
**Nombre dominio:** $ARGUMENTS
**Summary:** $ARGUMENTS

## Pasos a seguir

1. Sugerir `Nombre aplicacion` si solo existe una aplicación en la carpeta raiz del proyecto.
2. Buscar el `Nombre commandHandler` dentro de todos los subdominios de la aplicación `Nombre aplicacion`. Si solo existe un commandHandler con ese nombre, usarlo e inferir `Nombre dominio` y `Nombre subdominio` por la jerarquía de directorios.
3. Si no existe, sugerir al usuario los commandHandlers disponibles y solicitarle que seleccione uno o que aborte la operación.
4. Sugerir el nombre del evento a partir del `Nombre commandHandler` usando el participio pasado del nombre del commandHandler, por ejemplo: si el commandHandler es `CrearPedidoCommandHandler`, sugerir `PedidoCreado`.
5. Crear el evento `Nombre evento` dentro del commandHandler `Nombre commandHandler` que está en el subdominio `Nombre subdominio` del dominio `Nombre dominio` del EventCatalog de la aplicación `Nombre aplicacion` usando `.\.claude\specs\eventcatalog\crear-evento.md`.

## ⚠️ EJECUCIÓN ESTRICTA DE SPECS

### Al ejecutar `.claude\specs\eventcatalog\crear-evento.md`:
- **SEGUIR TODOS LOS PASOS** del spec sin saltar ninguno
- **UNA PREGUNTA A LA VEZ** - nunca hacer múltiples preguntas en el mismo mensaje
- **ESPERAR respuesta** del usuario antes de continuar al siguiente paso
- **NUNCA inventar datos** - siempre preguntar por información faltante

### Al crear entidades (paso 7 del spec crear-evento.md):
- **EJECUTAR COMPLETAMENTE** `.claude\specs\eventcatalog\crear-entidad.md` 
- **PREGUNTAR UNA POR UNA** las propiedades de cada entidad
- **NO ASUMIR** tipos de datos o descripciones
- **VALIDAR** cada propiedad con el usuario antes de continuar
- **DOCUMENTAR** correctamente el schema Avro de cada entidad

### Control de Flujo:
- **DETENER** si falta cualquier información
- **NO CONTINUAR** hasta recibir confirmación explícita
- **VALIDAR** cada paso completado antes del siguiente
- **PREGUNTAR EXPLÍCITAMENTE** por cada dato requerido en el spec
