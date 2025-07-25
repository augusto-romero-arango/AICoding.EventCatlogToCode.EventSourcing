# Crear una aplicación con arquitectura eventual a partir de la especificación de EventCatalog

## High-level objective

Crear el esqueleto para una aplicación que implemente un EventCatalog y a partir de él, crear soluciones que implementen un EventSourcing segín la plantilla de proyecto Cosmos.EventSourcing.Templante.

## Mid-level objectives

- Crear el proyecto de EventCatalog.
- Crear la carpeta de las soluciones.

## Implementation notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/event-catalog/eventcatalog)
- Las soluciones se crearán en el directorio `src/`

## Context

### Beginning context


### Final context

- `./{nombre-aplicacion}/` Directorio que contendrá el proyecto de EventCatalog y la solución.
- `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/` Directorio que contendrá el EventCatalog de la aplicación.
- `./{nombre-aplicacion}/src/` Directorio que contendrá las soluciones que implementen EventSourcing.

## Lowlevel Tasks

1. Capturar del prompt el nombre de la aplicación y guadarlo en la variable `nombre-aplicacion-original`.
2. Normalizar el nombre de la aplicación con las siguientes reglas:
    - El nombre de la aplicación debes estar en minúsculas.
    - Se deben reemplazar los espacios por guiones bajos.
    - Se deben eliminar caracteres especiales.
    - Las tildes debes ser reemplazadas por su versión sin tilde.
    - Las letras "ñ" deben ser reemplazadas por "n".
    - Se deben limpiar los espacios iniciales y finales.
    - El nombre normalizado debe guardarse en memoria con la variable `nombre-aplicacion`.
  
3. Crear los directorios necesarios:
      
   ```bash
   #!/bin/bash

   # Variables
   NOMBRE_APLICACION="[nombre-aplicacion]"
   NOMBRE_APLICACION_ORIGINAL="[nombre-aplicacion-original]"

   # Step 2: Create application directory
   if [ -d "./${NOMBRE_APLICACION}" ]; then
      echo "Error: El directorio './${NOMBRE_APLICACION}' ya existe. Abortando operación."
      exit 1
   else
      mkdir "./${NOMBRE_APLICACION}"
      echo "Directorio './${NOMBRE_APLICACION}' creado exitosamente."
   fi

   # Step 3: Create src directory
   if [ -d "./${NOMBRE_APLICACION}/src" ]; then
      echo "Aviso: El directorio 'src' ya existe. Continuando al siguiente paso."
   else
      mkdir "./${NOMBRE_APLICACION}/src"
      echo "Directorio 'src' creado exitosamente."
   fi
   ```

4. Instalar EventCatalog:
   1. Instalar en el directorio {nombre-aplicacion} una aplicación con el nombre `{nombre-aplicacion}-catalog` usando el archivo de especificación `.claude\specs\instalar-eventcatalog.md`.
