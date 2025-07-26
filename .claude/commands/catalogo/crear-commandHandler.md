Crea un un commandHandler en la sección services de EventCatalog dentro de una aplicación existente.

**Nombre aplicacion:** $ARGUMENTS
**Nombre subdominio:** $ARGUMENTS
**Nombre dominio:** $ARGUMENTS
**Nombre commandHandler:** $ARGUMENTS
**Summary:** $ARGUMENTS

## Pasos a seguir

1. Sugerir `Nombre aplicacion` si solo existe una aplicación en la carpeta raiz del proyecto.
2. Buscar el subdominio dentro de todos los dominios de la aplicación `Nombre aplicacion`. Si solo existe un subdominio con ese nombre, usarlo e inferir `Nombre dominio` por la jerarquía de directorios. 
3. Si no existe, sugerir al usuario los subdominios disponibles y solicitarle que seleccione uno o que aborte la operación.
4. Crear el commandHandler `Nombre commandHandler` dentro del subdominio `Nombre subdominio` que está en el dominio `Nombre dominio` del EventCatalog de la aplicación `Nombre aplicacion` usando `.\.claude\specs\eventcatalog\crear-commandHandler.md`.
