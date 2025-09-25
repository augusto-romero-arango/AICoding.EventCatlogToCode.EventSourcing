# Crear un Mensaje de evento en EventCatalog

## High-Level Objective

Crear un evento que es emitido por un CommandHandler dentro de un subdominio de EventCatalog.

## Mid-Level Objectives

- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista el subdominio al que pertenece el evento.
- Verificar que exista un CommandHandler que emite el evento.
- Crear el evento dentro del service del CommandHandler en el subdominio del EventCatalog de la aplicación.

## Implementation Notes
- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Service Directory (CommandHandler)**: Directorio que contendrá el evento.
 
### Ending Context

- **Events Directory**: Directorio que contendrá los eventos del CommandHandler.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del CommandHandler en EventCatalog:
   - Capturar y normalizar los nombres de aplicación, dominio, subdominio y CommandHandler del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory** → **Subdomain Directory** → **Service Directory (CommandHandler)**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Verificar que existe la **Events Directory** dentro del CommandHandler, crearla si no existe.
   - Guardar las rutas encontradas en las variables correspondientes.
  
2. Obtener el nombre del evento:
   - Capturar del prompt el nombre del evento a crear.
   - El nombre del evento debe estar expresado en participio pasado, por ejemplo: `PedidoCreado`, `UsuarioRegistrado`, etc. 
     - Si el nombre del evento no está en participio pasado, sugerir al usuario que lo corrija.
   - El nombre del evento debe estar relacionado con el CommandHandler que lo emite, por ejemplo: si el CommandHandler es `CrearPedidoCommandHandler`, el evento debería ser `PedidoCreado`.
   - Normalizar el nombre del evento según las reglas de **Event Names** en directory-structure.md.
   - Si el nombre del evento ya existe en la **Events Directory**, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-evento}`.

3. Crear el directorio del evento:
   - Crear la **Event Directory** para `{nombre-evento}`.


5. Registrar el evento en el commandHandler:
   - Modificar el archivo `index.mdx` de la **Service Directory (CommandHandler)** para incluir el evento como un evento del CommandHandler. Agregando el `nombre-evento` al array `sends` en el frontmatter del archivo:
     ```mdx
     ---
     ...

     sends:
         - id: {nombre-evento}
     ...
     ---
  
     ```
    - Si el archivo `index.mdx` ya contiene un array `sends`, agregar el `nombre-evento` al final del array.
    - Si el archivo `index.mdx` no contiene un array `sends`, crearlo después de la propiedad `summary` con el evento.

6. Recibir las propiedades del evento:
   - Las propiedades del evento deben ser proporcionadas como parámetro
   - El formato debe ser: `Prop1:tipo,Prop2:tipo,...`
   - Los tipos pueden ser primitivos (string, int, decimal, boolean, datetime, guid) o complejos
   - Todas los eventos deben tener una propiedad con el id del agregado root que se muta con la aplicación del evento. Esa propiedad debe corresponder al tipo de datos del id del aggregate root y su nombre debe ser `Id{aggregate-root-name}`. Por ejemplo, si el aggregate root es `Pedido`, la propiedad debe ser `IdPedido`.

7. Recibir información del aggregate root:
   - El nombre del aggregate root debe ser proporcionado como parámetro
   - El aggregate root debe existir previamente o haber sido creado en el flujo anterior
   - En el archivo `index.mdx` del evento, agregar un unordered list dentro de la sección  `State Mutations` con las propiedades que modifican el aggregate root.
   - **IMPORTANTE**: El Id del aggregate root SOLO se documenta en State Mutations si este evento es el que CREA el aggregate root. Si el aggregate root ya existe, NO incluir el Id en las mutaciones del estado.
 
8. Crear el archivo de especificación del evento:
   - Crear el archivo `index.mdx` dentro de la **Event Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Generar un summary contextualizado para el evento:
     - Si se proporciona el parámetro "Summary" con instrucciones de generación contextual, analizar:
       - Dominio y subdominio donde se produce el evento
       - Propiedades del evento y su significado de negocio
       - AggregateRoot afectado y cambios que representa
       - Contexto del flujo de negocio donde se dispara
     - Redactar un summary específico que explique el propósito de negocio y cambios
     - Si no se proporciona contexto específico, solicitar al usuario que proporcione el summary
   - El contenido del archivo debe ser:
   ```mdx
   ---
   id: {nombre-evento}
   name: {nombre-evento}
   version: 0.0.1
   summary: |
      {summary}


   schemaPath: 'schema.avro'
   ---
   
   ## Overview

   {summary}

   ## State Mutations

   - {aggregate-root-property}: {event-property}
   - ...

   ## Properties
   <Schema file="schema.avro" />

   <SchemaViewer file="schema.avro" />
        
   ```
   - Crear el archivo `schema.avro` correspondiente con todas las propiedades especificadas por el usuario.
   - **IMPORTANTE**: El archivo `schema.avro` debe contener un esquema JSON de Avro (no formato binario), siguiendo la especificación Apache Avro JSON Schema.
   - El esquema debe seguir esta estructura:
     ```json
     {
       "type": "record",
       "name": "{nombre-evento}",
       "namespace": "{aplicacion}.{dominio}.{subdominio}",
       "fields": [
         {
           "name": "propiedad",
           "type": "tipo",
           "doc": "descripción de la propiedad"
         }
       ]
     }
     ```

9. Validación final:
   - Verificar automáticamente que todos los pasos se completaron correctamente
   - Listar todos los archivos creados:
     - Archivo del evento: `{Event Directory}/index.mdx`
     - Schema del evento: `{Event Directory}/schema.avro`
   - Reportar éxito o error en la creación del evento
