# Crear un CommandHandler como un servicio de EventCatalog

## High-Level Objective

Crear un CommandHandler que se registre como un servicio dentro de un subdominio de EventCatalog.

## Mid-Level Objectives

- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista un subdominio creado.
- Crear el servicio CommandHandler dentro del subdominio del EventCatalog de la aplicación.
- Asociar el commandHandler a un subdominio específico.

## Implementation Notes
- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/Sinco/Cosmos.EventCatalog).

- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

### Beginning Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/` Directorio que contendrá los servicios del subdominio.

### Ending Context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/` Directorio que contendrá el CommandHandler creado.- 

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
   - Guardar en la variable `{ruta-subdominio}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/` encontrado.
   - Verificar que el directorio `{ruta-subdominio}` contenga un directorio `services`.
   - Si no se encuentra el directorio `services`, crearlo.
   - Guardar en la variable `{ruta-servicios}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/` encontrado.

2. Obtener el nombre del CommandHandler:
   - Capturar del prompt el nombre del CommandHandler a crear.
   - Normalizar el nombre del CommandHandler con las siguientes reglas:
     - PascalCase.
     - Eliminar caracteres especiales.
     - Si en el nombre del CommandHandler no se encuentra el sufijo `CommandHandler`, agregarlo al nombre.
   - Si el nombre del CommandHandler ya existe en el directorio `services`, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-commandHandler}`.
 
 3. Crear el directorio del CommandHandler:
   - Crear el directorio `{ruta-servicios}/{nombre-commandHandler}`.

4. Crear el archivo de especificación del CommandHandler:
   - Crear el archivo `index.mdx` dentro del directorio del CommandHandler.
   - La versión inicial debe ser `0.0.1`.
   - Sugerir un summary para el CommandHandler basado en el nombre del CommandHandler y la aplicación. El usuario lo puede aceptar o modificar.
   - El contenido del archivo debe ser:
        ```mdx
        ---
        id: {nombre-commandHandler}
        name: {nombre-commandHandler}
        version: 0.0.1
        summary: |
            {summary}
       
        ---
        ## Overview

        {summary}

        <NodeGraph />

        ## Entity Map

        <EntityMap id="{nombre-subdominio}" />

        ```
5. Registrar el CommandHandler en el subdominio:
   - Modificar el archivo `{ruta-subdominio}/index.mdx` para incluir el CommandHandler como un servicio del subdominio. Agregando el `nombre-commandHandler` al array `services` en el frontmatter del archivo:
        ```mdx
        ---
        ...

        services:
            - {nombre-commandHandler}

        ...
        ---

        ```
    - Si el archivo `index.mdx` ya contiene un array `services`, agregar el `nombre-commandHandler` al final del array.
    - Si el archivo `index.mdx` no contiene un array `services`, crearlo después de la propiedad `summary`.