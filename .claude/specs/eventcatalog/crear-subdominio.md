# Crear un subdominio en EventCatalog

## High-level objective

Crear un subdominio dentro de un dominio en EventCatalog para una aplicación existente.

## Mid-level objectives
- Verificar que exista una aplicación creada.
- Verificar que exista el dominio padre del subdominio dentro del EventCatalog de la aplicación.
- Crear un subdominio dentro del EventCatalog de la aplicación.

## Implementation notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/Sinco/Cosmos.EventCatalog).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning context

- **Domain Directory**: Directorio que contendrá el dominio padre.

### Ending context

- **Subdomain Directory**: Directorio que contendrá el subdominio creado.

## Low-Level Tasks
> Ordered from start to finish

1. Validar estructura y obtener el dominio padre:
   - Capturar y normalizar los nombres de aplicación y dominio del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Guardar las rutas encontradas en las variables correspondientes.
   

2. Obtener el nombre del subdominio
   - Capturar del prompt el nombre del subdominio a crear y guardarlo en la variable `{nombre-subdominio-original}`.
   - Normalizar el nombre del subdominio según las reglas de **Subdomain Names** en directory-structure.md.
   - El nombre normalizado debe guardarse en memoria con la variable `{nombre-subdominio}`.
   - Si el nombre del subdominio ya existe en la **Subdomains Directory**, abortar la operación y mostrar un mensaje de error al usuario.
3. Crear el directorio del subdominio
   - Crear la **Subdomain Directory** para `{nombre-subdominio}`.

4. Crear el archivo de especificación del subdominio
   - Crear el archivo `index.mdx` dentro de la **Subdomain Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Sugiere un summary para el subdominio basado en el nombre del subdominio y la aplicación. El usuario lo puede aceptar o modificar.
   - El summary debe ser una descripción breve del subdominio y su propósito dentro de la aplicación.
   - El contenido del archivo debe ser:
      ```mdx

      ---
      id: {nombre-subdominio}
      name: {nombre-subdominio-original}
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
5. Asociar el subdominio al dominio padre
   - Agregar una referencia al subdominio en el archivo `index.mdx` de la **Domain Directory**:
   - Modificar el archivo `index.mdx` del dominio padre:
     - En el frontmatter, agregar el subdominio a la propiedad `domains`:
      ```mdx
      ---
      ...

      domains:
         - id: {nombre-subdominio}

      ...

      ---
      ```
      - Si el frontmatter ya contiene la propiedad `domains`, agregar el subdominio a la lista existente.
      - Si no existe debe crear la propiedad `domains` debajo de la propiedad `summary` del frontmatter.




