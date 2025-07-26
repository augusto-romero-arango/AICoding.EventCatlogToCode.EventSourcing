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
