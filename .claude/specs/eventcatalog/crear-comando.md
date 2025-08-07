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

5. Solicitar las propiedades del comando:
   - OBLIGATORIO: Preguntar explícitamente "¿Qué propiedades debe tener el comando {nombre-comando}?"
   - NUNCA asumir que las propiedades son iguales al evento relacionado o a cualquier otro elemento
   - ESPERAR respuesta completa del usuario antes de continuar
   - Todos los comandos deben tener una propiedad con el id del aggregate root que se muta con la aplicación del comando. Esa propiedad debe corresponder al tipo de datos del id del aggregate root y su nombre debe ser `Id{aggregate-root-name}`. Por ejemplo, si el aggregate root es `Pedido`, la propiedad debe ser `IdPedido`.
   - Para cada propiedad proporcionada por el usuario, preguntar: nombre exacto, tipo de dato, si es requerida, descripción detallada.
   - Si el usuario menciona objetos complejos, anotar sus nombres para el siguiente paso.

5.1. OBLIGATORIO - Por cada objeto complejo identificado en las propiedades del comando:
   - DETENER el workflow actual
   - Verificar si existe como entidad en el subdominio usando LS
   - Si NO existe:
     - Ejecutar COMPLETAMENTE el spec `crear-entidad.md` para crear la entidad con `aggregateRoot: false`
     - ESPERAR confirmación de que la entidad fue creada exitosamente
     - Solicitar al usuario todas las propiedades de esta entidad objeto complejo
   - Repetir este proceso para CADA objeto complejo antes de continuar
   - Solo continuar al siguiente paso cuando TODAS las entidades de objetos complejos estén creadas

6. Crear el archivo de especificación del comando:
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
   - Crear el archivo `schema.avro` correspondiente con todas las propiedades especificadas por el usuario.

7. Solicitar las reglas de validación del comando distintas a tipos de datos:
   - Preguntar al usuario si el comando tiene reglas de validación adicionales.
   - No se deben incluir validaciones de tipo de dato, ya que estas se manejan automáticamente por el esquema Avro.
   - Si el usuario responde afirmativamente, Solicitar las reglas de validación del comando.
   - Las reglas de validación deben ser documentadas en la sección `## Guards` del archivo `index.mdx` del comando. Esta sección debe estar debajo de la sección `## Properties`.
   - Las reglas de validación deben ser expresadas como una lista de verificación, por ejemplo:
     ```mdx
     ## Guards
     - La propiedad `cantidad` debe ser mayor a 0.
     - La propiedad `fechaEntrega` debe ser una fecha futura.
     ```

   - Si el usuario no responde afirmativamente, continuar con el siguiente paso.

8. Validación final:
   - Confirmar con el usuario que todos los pasos se completaron correctamente
   - Listar todos los archivos creados para verificación:
     - Archivo del comando: `{Command Directory}/index.mdx`
     - Schema del comando: `{Command Directory}/schema.avro`
     - Todas las entidades de objetos complejos creadas
   - Solo marcar como completado si el usuario confirma explícitamente