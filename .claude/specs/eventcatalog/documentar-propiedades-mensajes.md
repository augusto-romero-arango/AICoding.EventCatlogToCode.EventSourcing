# Documentar las propiedades de un mensaje para EventCatalog

## High-Level Objective

Documentar las propiedades de un mensaje existente en esquema Avro y publicarlo en EventCatalog. Los mensajes pueden ser eventos, comandos o consultas.

## Mid-Level Objectives

- Verificar que exista el evento en EventCatalog.
- Crear un esquema Avro para el evento.
- Publicar el esquema en EventCatalog.

## Implementation Notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- Usar la especificación JSON Schema de Apache Avro en la versión 1.12.0. Referenciada en [Avro 1.12.0](https://avro.apache.org/docs/1.12.0/specification/)
- **FORMATO**: Usar JSON Schema de Avro, NO formato binario Avro.
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Event Directory**: Directorio que contendrá el evento.
- **Command Directory**: Directorio que contendrá el comando.

### Ending Context

- **Event Directory** + `schema.avro`: Archivo de documentación del evento.
- **Command Directory** + `schema.avro`: Archivo de documentación del comando.

## Low-Level Tasks
> Ordered from start to finish

1. Ubicar el evento o el comando en EventCatalog:
   - Capturar del prompt el nombre del mensaje y determinar su tipo según las reglas de **Event Names** y **Command Names** en directory-structure.md.
   - Buscar el mensaje en todas las **Events Directory** o **Commands Directory** del EventCatalog.
   - Si no se encuentra, listar los mensajes disponibles y solicitar al usuario que seleccione uno o aborte la operación.
   - Si hay múltiples coincidencias, mostrar las opciones con su subdominio correspondiente para que el usuario seleccione.
   - Guardar la **Event Directory** o **Command Directory** encontrada en la variable correspondiente.

2. Verificar que el mensaje tenga un archivo `index.mdx`:
   - Si el archivo `index.mdx` no existe, abortar la operación y mostrar un mensaje de error al usuario.
   - Si el archivo `index.mdx` existe, continuar al siguiente paso.

3. Crear el esquema Avro del mensaje:
   - Iterar sobre las propiedades que enuncie el usuario en el prompt.
   - Para cada propiedad, capturar el nombre, tipo y descripción.
   - Generar el archivo `schema.avro` en la **Event Directory** o **Command Directory** correspondiente usando formato JSON Schema de Avro 1.12.0.
   - **ESTRUCTURA REQUERIDA**:
     ```json
     {
       "type": "record",
       "name": "{nombre-mensaje}",
       "namespace": "{aplicacion}.{dominio}.{subdominio}[.commands|.events]",
       "fields": [
         {
           "name": "nombrePropiedad",
           "type": "tipoAvro",
           "doc": "descripción de la propiedad"
         }
       ]
     }
     ```
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
   
   
