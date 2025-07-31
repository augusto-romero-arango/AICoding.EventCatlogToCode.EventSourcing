# Crear un Mensaje de comando en EventCatalog

## High-Level Objective

Crear un comando que es recibido por un CommandHandler dentro de un subdominio de EventCatalog.

## Mid-Level Objectives

- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista el subdominio al que pertenece el comando.
- Verificar que exista un CommandHandler que recibe el comando.
- Crear el comando dentro del service del CommandHandler en el subdominio del EventCatalog de la aplicación.

## Implementation Notes
- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Service Directory (CommandHandler)**: Directorio que contendrá el comando.
 
### Ending Context

- **Commands Directory**: Directorio que contendrá los comandos del CommandHandler.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del CommandHandler en EventCatalog:
   - Capturar y normalizar los nombres de aplicación, dominio, subdominio y CommandHandler del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory** → **Subdomain Directory** → **Service Directory (CommandHandler)**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Verificar que existe la **Commands Directory** dentro del CommandHandler, crearla si no existe.
   - Guardar las rutas encontradas en las variables correspondientes.
  
2. Obtener el nombre del comando:
   - Capturar del prompt el nombre del comando a crear.
   - El nombre del comando debe estar expresado como una acción, por ejemplo: `CrearPedido`, `ActualizarUsuario`, etc. 
     - Si el nombre del comando no está expresado como una acción, sugerir al usuario que lo corrija.
   - El nombre del comando debe estar relacionado con el CommandHandler que lo recibe, por ejemplo: si el CommandHandler es `CrearPedidoCommandHandler`, el comando debería ser `CrearPedido`.
   - Normalizar el nombre del comando según las reglas de **Command Names** en directory-structure.md.
   - Si el nombre del comando ya existe en la **Commands Directory**, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-comando}`.

3. Crear el directorio del comando:
   - Crear la **Command Directory** para `{nombre-comando}`.

4. Registrar el comando en el commandHandler:
   - Modificar el archivo `index.mdx` de la **Service Directory (CommandHandler)** para incluir el comando como un comando del CommandHandler. Agregando el `nombre-comando` al array `receives` en el frontmatter del archivo:
     ```mdx
     ---
     ...

     receives:
         - id: {nombre-comando}
     ...
     ---
  
     ```
    - Si el archivo `index.mdx` ya contiene un array `receives`, agregar el `nombre-comando` al final del array.
    - Si el archivo `index.mdx` no contiene un array `receives`, crearlo después de la propiedad `summary` con el comando.

5. Invitar al usuario a documentar las propiedades del comando:
   - Todos los comandos deben tener una propiedad con el id del aggregate root que se muta con la aplicación del comando. Esa propiedad debe corresponder al tipo de datos del id del aggregate root y su nombre debe ser `id{aggregate-root-name}`. Por ejemplo, si el aggregate root es `Pedido`, la propiedad debe ser `idPedido`.
   - Preguntar al usuario si el comando tiene propiedades adicionales que deben documentarse.
   - Si el usuario responde afirmativamente, invitarlo a documentar las propiedades del comando:
   - Preguntar por cada propiedad su nombre, tipo de dato y una breve descripción.

   - Si existe una propiedad que sea un objeto complejo, se debe verificar si existe en las entidades.
     - Si no existe, se debe crear una entidad con `aggregateRoot` `false` usando `crear-entidad.md` y documentar las propiedades de esa entidad.
     - Si existe, use las reglas de `documentar-propiedades-entidad.md` con las propiedades de la entidad existente, para documentar las propiedades del objeto complejo usado dentro del comando.
   - Documentar el resto de las propiedades del `mensaje` usando las reglas de `documentar-propiedades-comando.md`.

7. Crear el archivo de especificación del comando:
   - Crear el archivo `index.mdx` dentro de la **Command Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Sugerir un summary para el comando basado en el nombre del comando y la aplicación. El usuario lo puede aceptar o modificar.
   - El contenido del archivo debe ser:
   ```mdx
   ---
   id: {nombre-comando}
   name: {nombre-comando}
   version: 0.0.1
   summary: |
      {summary}
   ---
   
   ## Overview

   {summary}

   ## Properties
   <Schema file="schema.avro" />

   <SchemaViewer file="schema.avro" />
        
   ```  