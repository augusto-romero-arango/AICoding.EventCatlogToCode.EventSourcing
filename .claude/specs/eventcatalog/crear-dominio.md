# Crear un dominio en EventCatalog

## High-level objective

Crear un dominio en EventCatalog para una aplicación existente.

## Mid-level objectives
- Verificar que exista una aplicación creada.
- Verificar que exista un EventCatalog para la aplicación.
- Crear un dominio dentro del EventCatalog de la aplicación.

## Implementation notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/Sinco/Cosmos.EventCatalog).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning context

- **Domains Root**: Directorio que contendrá los dominios de la aplicación. 

### Ending context

- **Domain Directory**: Directorio que contendrá el dominio creado.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio de event catalog
   - Capturar el nombre de la aplicación del prompt y normalizarlo según las reglas de **Application Names** en directory-structure.md.
   - Verificar que existe la **EventCatalog Root** para la aplicación.
   - Si no se encuentra, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar en la variable `{ruta-catalogo}` la **EventCatalog Root** encontrada.

2. Verificar que el directorio de dominios exista
   - Verificar que existe la **Domains Root**. En caso que no exista, crear el directorio.
   - Si la **Domains Root** ya existe, continuar al siguiente paso.

3. Obtener el nombre del dominio
   - Capturar del prompt el nombre del dominio a crear y guardarlo en la variable `{nombre-dominio-original}`.
   - Normalizar el nombre del dominio según las reglas de **Domain Names** en directory-structure.md.
   - El nombre normalizado debe guardarse en memoria con la variable `{nombre-dominio}`.
   - Si el nombre del dominio ya existe en la **Domains Root**, abortar la operación y mostrar un mensaje de error al usuario.
  
4. Crear el directorio del dominio
   - Crear la **Domain Directory** para `{nombre-dominio}`.
   
  
5. Crear el archivo de especificación del dominio
   - Crear el archivo `index.mdx` dentro de la **Domain Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Sugiere un summary para el dominio basado en el nombre del dominio y la aplicación. El usuario lo puede aceptar o modificar.
   - El summary debe ser una descripción breve del dominio y su propósito dentro de la aplicación.
   - El contenido del archivo debe ser:
     ```mdx

    ---
    id: {nombre-dominio}
    name: {nombre-dominio-original}
    version: 0.0.1
    summary: |
        {summary}
    ---

    ## Overview
    
    {summary}
    
    <NodeGraph />

    ```

