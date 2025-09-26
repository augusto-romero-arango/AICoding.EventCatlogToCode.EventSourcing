# Crear una Entidad en EventCatalog

## High-Level Objective

Crear una entidad que representa un objeto de dominio dentro de un subdominio de EventCatalog.

## Mid-Level Objectives

- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista el subdominio al que pertenece la entidad.
- Crear la entidad dentro del subdominio del EventCatalog de la aplicación.
- Crear las propiedades de la entidad dentro del frontmatter de la entidad.
- Asociar la entidad al subdominio correspondiente.

## Implementation Notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://eventcatalog.dev).
- **Directory Structure**: All paths follow the patterns defined in [directory-structure.md](./directory-structure.md).
- Las entidades pueden tener propiedades con relaciones a otras entidades usando `references`, `relationType` y `referencesIdentifier`.
- IMPORTANTE: No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

> **Directory Structure Reference**: See [directory-structure.md](./directory-structure.md) for complete path definitions.

### Beginning Context

- **Subdomain Directory**: Directorio que contendrá el directorio de la entidad a crear.

### Ending Context

- **Entities Directory**: Directorio que contendrá la entidad creada, el cual debe estar en el direcotorio del subdominio correspondiente.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio del subdominio en EventCatalog:
   - Capturar y normalizar los nombres de aplicación, dominio y subdominio del prompt según las reglas correspondientes en directory-structure.md.
   - Validar la existencia de la estructura: **EventCatalog Root** → **Domain Directory** → **Subdomain Directory**.
   - Si algún directorio no existe, mostrar opciones disponibles al usuario o abortar según corresponda.
   - Verificar que existe la **Entities Directory** dentro del subdominio, crearla si no existe.
   - Guardar las rutas encontradas en las variables correspondientes.

2. Obtener el nombre de la entidad:
   - Capturar del prompt el nombre de la entidad a crear.
   - Normalizar el nombre de la entidad según las reglas de **Entity Names** en directory-structure.md.
   - Si el nombre de la entidad ya existe en **Entities Directory**, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar el nombre normalizado en la variable `{nombre-entidad}`.

3. Crear el directorio de la entidad dentro del directorio del subdominio:
   - OBLIGATORIO:Nunca crear el directorio de entidades en el directorio de dominio. Asegurarse que esté dentro del directorio de subdominio.
   - Crear la **Entities Directory** para `{nombre-entidad}`.

4. Crear el archivo de especificación de la entidad:
   - Crear el archivo `index.mdx` dentro de la **Entities Directory**.
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

5. OBLIGATORIO - Solicitar las propiedades de la entidad:
   - OBLIGATORIO: Preguntar explícitamente "¿Qué propiedades debe tener la entidad {nombre-entidad}?"
   - NUNCA asumir propiedades basadas en otros elementos o contexto
   - ESPERAR respuesta completa del usuario antes de continuar
   - Para cada propiedad proporcionada por el usuario, preguntar: nombre exacto, tipo de dato, si es requerida, descripción detallada
   - OBLIGATORIO: **El Id de la entidad debe ser nombrado como Id de tipo string.** Esto se debe respetar así el evento o comando tenga la estructura Id{AggregateRoot}. Aunque internamente sea un GUID, en las propiedades del EventCatalog se debe documentar como string.
   - Crear propiedades en el frontmatter del archivo `index.mdx` de la entidad con las propiedades especificadas por el usuario
   - Si el usuario menciona objetos complejos anidados, crear entidades separadas para cada uno usando este mismo spec
   - Permitir configurar relaciones entre entidades usando `references`, `relationType` y `referencesIdentifier`
   - Importante: Las descripciones de las propiedades no pueden usar los caracteres reservados de la sintaxis yaml.
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

6. Registrar la entidad en el subdominio:
   - Modificar el archivo `index.mdx` de la **Subdomain Directory** para incluir la entidad. Agregando el `nombre-entidad` al array `entities` en el frontmatter del archivo:

     ```mdx
     ---
     ...

     entities:
         - id: {nombre-entidad}
     ---
  
     ```

   - Si el archivo `index.mdx` ya contiene un array `entities`, agregar la entidad al final del array.
   - Si el archivo `index.mdx` no contiene un array `entities`, crearlo después de la propiedad `summary` con la entidad.

7. Validación final:
   - Confirmar con el usuario que todos los pasos se completaron correctamente
   - Listar todos los archivos creados para verificación:
     - Archivo de la entidad: `{Entity Directory}/index.mdx`
     - Registro en subdominio actualizado
   - Solo marcar como completado si el usuario confirma explícitamente