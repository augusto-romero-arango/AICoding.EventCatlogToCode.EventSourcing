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

6. Invitar al usuario a documentar las propiedades del evento:
   - Todas los eventos deben tener una propiedad con el id del agregate root que se muta con la aplicación del evento. Esa propiedad debe corresponder al tipo de datos del id del aggregate root y su nombre debe ser `id{aggregate-root-name}`. Por ejemplo, si el aggregate root es `Pedido`, la propiedad debe ser `idPedido`.
   - Preguntar al usuario si el evento tiene propiedades adicionales que deben documentarse.
   - Si el usuario responde afirmativamente, invitarlo a documentar las propiedades del evento:
   - Preguntar por cada propiedad su nombre, tipo de dato y una breve descripción.

   - Si existe una propiedad que sea un objeto complejo, se debe verificar si existe en las entidades.
     - Si no existe, se debe crear una entidad con `aggregateRoot` `false` usando `crear-entidad.md` y documentar las propiedades de esa entidad.
     - Si existe, use las reglas de `documentar-propiedades-entidad.md` con las propiedades de la entidad existente, para documentar las propiedades del objeto complejo usado dentro del evento.
   - Documentar el resto de las propiedades del `mensaje` usando las reglas de `documentar-propiedades-evento.md`.

7. Solicitar al usuario las propiedades del agregado que va a afectar el evento:
   - Preguntar al usuario el nombre del aggregate root que se muta con la aplicación del evento.
   - Buscar en las entidades del dominio si existe una entidad marcada con `aggregateRoot` `true` con el nombre del agregado.
   - Si no existe, se debe crear una entidad con `aggregateRoot` `true` usando `crear-entidad.md` y documentar las propiedades de esa entidad.
   - Si existe, usar las reglas de `documentar-propiedades-entidad.md` con las propiedades que no estén documentadas en el agregado.
   - En el archivo `index.mdx` del evento, agregar un unordered list dentro de la sección  `State mutetions` con las propiedades afectadas.
 
 8. Crear el archivo de especificación del evento:
   - Crear el archivo `index.mdx` dentro de la **Event Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Sugerir un summary para el evento basado en el nombre del evento y la aplicación. El usuario lo puede aceptar o modificar.
   - El contenido del archivo debe ser:
   ```mdx
   ---
   id: {nombre-evento}
   name: {nombre-evento}
   version: 0.0.1
   summary: |
      {summary}
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
