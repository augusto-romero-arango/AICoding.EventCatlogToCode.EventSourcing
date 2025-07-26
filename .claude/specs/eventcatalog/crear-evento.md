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
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

### Beginning Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/` Directorio que contendrá el evento.
 
### Ending Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/` Directorio que contendrá los eventos del CommandHandler.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del subdominio en EventCatalog:
   - El nombre de la aplicación se debe capturar del prompt. A partir del nombre recibido en el prompt, buscar en la raiz del proyecto un directorio que coincida con el nombre de la apliación ya normalizado. Guardar en la variable `{nombre-aplicacion}` el nombre del directorio encontrado.
   - Verificar que el directorio encontrado contenga un directorio `{nombre-aplicacion}-catalog`.
   - Si no se encuentra el directorio o no contiene el directorio `{nombre-aplicacion}-catalog`, abortar la operación y mostrar un mensaje de error al usuario.
   - Verificar que el directorio `{nombre-aplicacion}-catalog` contenga un directorio `domains`.
   - Si no se encuentra el directorio `domains`, abortar la operación y mostrar un mensaje de error al usuario.
   - Verificar que el directorio `domains` contenga un directorio `{nombre-dominio}`.
   - Si no se encuentra el directorio `{nombre-dominio}`, abortar la operación y mostar las opiciones de dominios disponibles al usuario.
   - Verificar que el directorio `{nombre-dominio}` contenga un directorio `subdomains`.
   - Si no se encuentra el directorio `subdomains`, abortar la operación y mostrar un mensaje de error al usuario.
   - Verificar que el directorio `subdomains` contenga un directorio `{nombre-subdominio}`.
   - Si no se encuentra el directorio `{nombre-subdominio}`, sugerir al usuario los nombre de subdominios disponibles y solicitarle que seleccione uno o que aborte la operación.
   - Verificar que el directorio `{nombre-subdominio}` contenga un directorio `services`.
   - Si no se encuentra el directorio `services`, abortar la operación y mostrar un mensaje de error al usuario.
   - Verificar que el directorio `services` contenga un directorio `{nombre-commandHandler}`.
   - Si no se encuentra el directorio `{nombre-commandHandler}`, sugerir al usuario los nombre de CommandHandlers disponibles y solicitarle que seleccione uno o que aborte la operación.
   - Guardar en la variable `{ruta-commandHandler}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/` encontrado.
   - Verificar que el directorio `{ruta-commandHandler}` contenga un directorio `events`.
   - Si no se encuentra el directorio `events`, crearlo.
   - Guardar en la variable `{ruta-eventos}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/` encontrado.
  
2. Obtener el nombre del evento:
   - Capturar del prompt el nombre del evento a crear.
   - El nombre del evento debe estar expresado en participio pasado, por ejemplo: `PedidoCreado`, `UsuarioRegistrado`, etc. 
     - Si el nombre del evento no está en participio pasado, sugerir al usuario que lo corrija.
   - El nombre del evento debe estar relacionado con el CommandHandler que lo emite, por ejemplo: si el CommandHandler es `CrearPedidoCommandHandler`, el evento debería ser `PedidoCreado`.
   - Normalizar el nombre del evento con las siguientes reglas:
     - PascalCase.
     - Eliminar caracteres especiales.
     - IMPORTANTE: Nunca usar el sufijo `Event` en el nombre del evento. Si el usuario lo incluye, eliminarlo.
   - Si el nombre del evento ya existe en el directorio `events`, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-evento}`.

3. Crear el directorio del evento:
   - Crear el directorio `{ruta-eventos}/{nombre-evento}`.

4. Crear el archivo de especificación del evento:
   - Crear el archivo `index.mdx` dentro del directorio del evento.
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

    <Schema file="schema.avro" />

    <SchemaViewer file="schema.avro" />
        
    ```

5. Registrar el evento en el commandHandler:
   - Modificar el archivo `{ruta-commandHandler}/index.mdx` para incluir el evento como un evento del CommandHandler. Agregando el `nombre-evento` al array `sends` en el frontmatter del archivo:
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

6. TODO: Invitar al usuario a documentar las propiedades del evento:
   - TODO: Crear un archivo `schema.avro` dentro del directorio del evento con las propiedades especificadas.
        