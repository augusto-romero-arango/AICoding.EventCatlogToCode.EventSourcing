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
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Services Directory**: Directorio que contendrá los servicios del subdominio.

### Ending Context

- **Service Directory (CommandHandler)**: Directorio que contendrá el CommandHandler creado. 

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del subdominio en EventCatalog:
   - Capturar y normalizar los nombres de aplicación, dominio y subdominio del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory** → **Subdomain Directory**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Verificar que existe la **Services Directory** dentro del subdominio, crearla si no existe.
   - Guardar las rutas encontradas en las variables correspondientes.

2. Obtener el nombre del CommandHandler:
   - Capturar del prompt el nombre del CommandHandler a crear.
   - Normalizar el nombre del CommandHandler según las reglas de **CommandHandler Names** en directory-structure.md.
   - Si el nombre del CommandHandler ya existe en la **Services Directory**, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-commandHandler}`.
 
 3. Crear el directorio del CommandHandler:
   - Crear la **Service Directory (CommandHandler)** para `{nombre-commandHandler}`.

4. Crear el archivo de especificación del CommandHandler:
   - Crear el archivo `index.mdx` dentro de la **Service Directory (CommandHandler)**.
   - La versión inicial debe ser `0.0.1`.
   - Generar un summary contextualizado para el CommandHandler:
     - Si se proporciona el parámetro "Summary" con instrucciones de generación contextual, analizar:
       - Dominio y subdominio donde opera
       - Comando que recibirá y evento que producirá
       - Business rules y guards que implementará
       - AggregateRoot que modificará
     - Redactar un summary específico que explique la responsabilidad completa del handler
     - Si no se proporciona contexto específico, sugerir basándose en el nombre y permitir modificación
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

        <EntityMap  collection="domains" id="{nombre-subdominio}" />

        ```
5. Registrar el CommandHandler en el subdominio:
   - Modificar el archivo `index.mdx` de la **Subdomain Directory** para incluir el CommandHandler como un servicio del subdominio. Agregando el `nombre-commandHandler` al array `services` en el frontmatter del archivo:
        ```mdx
        ---
        ...

        services:
            - id: {nombre-commandHandler}

        ...
        ---

        ```
    - Si el archivo `index.mdx` ya contiene un array `services`, agregar el `nombre-commandHandler` al final del array.
    - Si el archivo `index.mdx` no contiene un array `services`, crearlo después de la propiedad `summary`.
6. Solicitar al usuario los business rules que deban ser consideradas por el CommandHandler:
   - Preguntar al usuario si el CommandHandler tiene business rules que deben documentarse.
   - Si el usuario responde afirmativamente, solicitarle las business rules del CommandHandler:
     - Preguntar por cada regla su descripción y cualquier detalle relevante.
   - Documentar las business rules en la sección `## Business Rules` del archivo `index.mdx` del CommandHandler, la cual se debe crear si no existe inmediatamente después de la sección `## Overview` y antes del tag `<NodeGraph />`.
   - Si el archivo `index.mdx` ya contiene una sección `## Business Rules`, agregar las nuevas reglas al final de la sección.
   - Registrar las reglas de negocio en el archivo `index.mdx` del CommandHandler de la siguiente manera:
     ```mdx
     ## Business Rules

     - {descripción-regla-1}
     - {descripción-regla-2}
     ...
     ``` 
