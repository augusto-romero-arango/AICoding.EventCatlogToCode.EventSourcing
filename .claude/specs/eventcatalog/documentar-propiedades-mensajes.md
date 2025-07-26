# Documentar las propiedades de un mensaje para EventCatalog

## High-Level Objective

Documentar las propiedades de un mensaje existente en esquema Avro y publicarlo en EventCatalog. Los mensajes pueden ser eventos, comandos o consultas.

## Mid-Level Objectives

- Verificar que exista el evento en EventCatalog.
- Crear un esquema Avro para el evento.
- Publicar el esquema en EventCatalog.

## Implementation Notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- Usar la especificación de Avro en la versión 1.12.0. Referenciada en [Avro 1.12.0](https://avro.apache.org/docs/1.12.0/specification/)
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

### Beginning Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/{nombre-evento}/` Directorio que contendrá el evento.
- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/{nombre-comando}/` Directorio que contendrá el comando.


### Ending Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/{nombre-evento}/schema.avro` Archivo de documentación del evento.
- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/{nombre-comando}/schema.avro` Archivo de documentación del comando.

## Low-Level Tasks
> Ordered from start to finish

1. Ubicar el evento o el comando en EventCatalog:
   - Determinar del prompt si se trata de un evento o un comando. Los eventos se pueden identificar porque están nombrados en participio pasado, por ejemplo: `PedidoCreado`, `UsuarioRegistrado`, etc. Los comandos suelen tener un nombre que indica una acción, como `CrearPedido`.
   - Buscar en todas las carpetas de `events` o `commands`  del EventCatalog mensaje con el nombre `{nombre-mensaje}`.
   - Si no encuentra el mensaje, listar los posibles eventos o comandos disponibles y solicitar al usuario que seleccione uno o que aborte la operación.
   - Si encuentra más de un mensaje con el mismo nombre, sugerir al usuario que seleccione uno de los eventos o comandos encontrados mostrándole el subdominio al que cada uno pertenece.
   - Guardar en la variable `{ruta-evento}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/{nombre-evento}/` encontrado.
   - Guardar en la variable `{ruta-comando}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/{nombre-comando}/` encontrado.

2. Verificar que el mensaje tenga un archivo `index.mdx`:
   - Si el archivo `index.mdx` no existe, abortar la operación y mostrar un mensaje de error al usuario.
   - Si el archivo `index.mdx` existe, continuar al siguiente paso.

3. Crear el esquema Avro del mensaje:
   - Iterar sobre las propiedades que enuncie el usuario en el prompt.
   - Para cada propiedad, capturar el nombre, tipo y descripción.
   - Generar el archivo `schema.avro` en la ruta `{ruta-evento}` o `{ruta-comando}` siguiendo la especificación de Avro 1.12.0.
   - Preguntar al usuario si desea agregar más propiedades.
   - Iterar hasta que el usuario indique que no desea agregar más propiedades.
  
4. Registrar el esquema en el mensaje.
   - Agregar el esquema Avro al archivo `index.mdx` del mensaje:
     - Modificar el frontmatter del archivo `index.mdx` para incluir la sección `schema` con la ruta al esquema Avro.
     - El frontmatter debe tener la siguiente estructura:
        ```yaml
        ---
        ...

        schemaPath: 'schema.avro'
        ---
        ```
    - Si el frontmatter ya contiene una sección `schema`, no es necesario modificar el archivo.
    - Si el frontmatter no contiene una sección `schema`, agregarla al final del frontmatter.
   
   
