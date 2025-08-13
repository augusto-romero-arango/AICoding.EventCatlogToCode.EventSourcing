Crea un un commandHandler en la sección services de EventCatalog dentro de una aplicación existente.

**Nombre aplicacion:** $ARGUMENTS
**Nombre subdominio:** $ARGUMENTS
**Nombre dominio:** $ARGUMENTS
**Nombre commandHandler:** $ARGUMENTS
**Business Rules:** $ARGUMENTS
**Summary:** $ARGUMENTS

## Pasos a seguir

1. Sugerir `Nombre aplicacion` si solo existe una aplicación en la carpeta raiz del proyecto.
2. Buscar el `Nombre subdominio` dentro de todos los dominios de la aplicación `Nombre aplicacion`:
   - Si el subdominio existe en UN SOLO dominio: Usarlo e inferir automáticamente `Nombre dominio` por la jerarquía de directorios.
   - Si el subdominio existe en MÚLTIPLES dominios: Mostrar las opciones disponibles con formato "subdominio (dominio)" y solicitar al usuario que especifique cuál usar.
   - Si el subdominio NO existe: Sugerir al usuario los subdominios disponibles organizados por dominio y solicitarle que seleccione uno o que aborte la operación.
3. Una vez identificado el par subdominio-dominio, continuar con la creación.
4. Crear el commandHandler `Nombre commandHandler` dentro del subdominio `Nombre subdominio` que está en el dominio `Nombre dominio` del EventCatalog de la aplicación `Nombre aplicacion` usando `.\.claude\specs\eventcatalog\crear-commandHandler.md`.
   - Si se proporcionan `Business Rules`: Usar estas reglas de negocio en la documentación del CommandHandler.
   - Pasar todos los argumentos disponibles a la spec para incluir las business rules en la documentación.
