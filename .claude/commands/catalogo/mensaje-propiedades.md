Crea el esquema Avro del mensaje en EventCatalog dentro de una aplicación existente.

**Nombre aplicacion:** $ARGUMENTS
**Nombre mensaje:** $ARGUMENTS
**Nombre commandHandler:** $ARGUMENTS



## Pasos a seguir

1. Ubicar el evento o el comando en EventCatalog:
   - Determinar del prompt si se trata de un evento o un comando. Los eventos se pueden identificar porque están nombrados en participio pasado, por ejemplo: `PedidoCreado`, `UsuarioRegistrado`, etc. Los comandos suelen tener un nombre que indica una acción, como `CrearPedido`.
   - Buscar en todas las carpetas de `events` o `commands`  del EventCatalog mensaje con el nombre `{nombre-mensaje}`.
   - Si no encuentra el mensaje, listar los posibles eventos o comandos disponibles y solicitar al usuario que seleccione uno o que aborte la operación.
   - Si encuentra más de un mensaje con el mismo nombre, sugerir al usuario que seleccione uno de los eventos o comandos encontrados mostrándole el subdominio al que cada uno pertenece.

2. Crear el esquema del evento o comando `Nombre mensaje` dentro del subdominio `Nombre commandHandler` en el que está registrado el evento o comando usando `.\.claude\specs\eventcatalog\mensaje-propiedades.md`.
