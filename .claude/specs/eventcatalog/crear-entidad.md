# Crear una Entidad en EventCatalog

## High-Level Objective

Crear una entidad que representa un objeto de dominio dentro de un dominio de EventCatalog.

## Mid-Level Objectives

- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista el dominio al que pertenece la entidad.
- Crear la entidad dentro del dominio del EventCatalog de la aplicación.
- Asociar la entidad al dominio correspondiente.

## Implementation Notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- Las entidades pueden tener propiedades con relaciones a otras entidades usando `references`, `relationType` y `referencesIdentifier`.
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Domain Directory**: Directorio que contendrá la entidad.

### Ending Context

- **Entity Directory**: Directorio que contendrá la entidad creada.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del subdominio en EventCatalog:
   - Capturar y normalizar los nombres de aplicación y dominio del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory** → **Subdomain Directory**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Verificar que existe la **Entities Directory** dentro del subdominio, crearla si no existe.
   - Guardar las rutas encontradas en las variables correspondientes.

2. Obtener el nombre de la entidad:
   - Capturar del prompt el nombre de la entidad a crear.
   - Normalizar el nombre de la entidad según las reglas de **Entity Names** en directory-structure.md.
   - Si el nombre de la entidad ya existe en la **Entities Directory**, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-entidad}`.

3. Crear el directorio de la entidad:
   - Crear la **Entity Directory** para `{nombre-entidad}`.

4. Crear el archivo de especificación de la entidad:
   - Crear el archivo `index.mdx` dentro de la **Entity Directory**.
   - La versión inicial debe ser `0.0.1`.
   - Sugerir un summary para la entidad basado en el nombre de la entidad y la aplicación. El usuario lo puede aceptar o modificar.
   - Preguntar al usuario si la entidad es un aggregate root (true/false).
   - El contenido del archivo debe ser:
     ```mdx
     ---
     id: {nombre-entidad}
     name: {nombre-entidad}
     version: 0.0.1
     aggregateRoot: {aggregate-root-value}
     summary: |
         {summary}
     ---
     
     ## Overview

     {summary}

     ## Properties

     <EntityPropertiesTable />
        
     ```

5. Registrar la entidad en el dominio:
   - Modificar el archivo `index.mdx` de la **Domain Directory** para incluir la entidad. Agregando el `nombre-entidad` al array `entities` en el frontmatter del archivo:
     ```mdx
     ---
     ...

     entities:
         - id: {nombre-entidad}
     ---
  
     ```
   - Si el archivo `index.mdx` ya contiene un array `entities`, agregar la entidad al final del array.
   - Si el archivo `index.mdx` no contiene un array `entities`, crearlo después de la propiedad `summary` con la entidad.

6. Invitar al usuario a documentar las propiedades de la entidad:
   -  Crear propiedades en el frontmatter del archivo `index.mdx` de la entidad con las propiedades especificadas.
   - Permitir configurar relaciones entre entidades usando `references`, `relationType` y `referencesIdentifier`.
   - Ejemplos de relaciones:
     - Para una relación uno a muchos:
       ```yaml
       properties:
        -  name: orderItems
           type: array
           items:
              type: OrderItem # Assuming an OrderItem entity exists
           required: true
           references: OrderItem
           referencesIdentifier: OrderItemId
           relationType: hasMany
           description: List of items included in the order
       ```
       - Para una relación uno a uno:
          ```yaml
          properties:
            - name: convertedFromCartId
              type: UUID
              required: false
              description: Shopping cart that was converted to this order
              references: ShoppingCart
              referencesIdentifier: Id
              relationType: hasOne
          ```