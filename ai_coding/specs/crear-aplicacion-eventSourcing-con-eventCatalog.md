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

- `.\{nombre-aplicacion}\` Directorio que contendrá el proyecto de EventCatalog y la solución.
- `.\{nombre-aplicacion}\{nombre-aplicacion}-catalog\` Directorio que contendrá el EventCatalog de la aplicación.
- `.\{nombre-aplicacion}\src\` Directorio que contendrá las soluciones que implementen EventSourcing.

## Lowlevel Tasks

1. Capturar del prompt el nombre de la aplicación y normalizarlo con las siguientes reglas:
    - El nombre de la aplicación debes estar en minúsculas.
    - Se deben reemplazar los espacios por guiones bajos.
    - Se deben eliminar caracteres especiales.
    - Las tildes debes ser reemplazadas por su versión sin tilde.
    - Las letras "ñ" deben ser reemplazadas por "n".
    - Se deben limpiar los espacios iniciales y finales.
    - El nombre normalizado debe guardarse en memoria con la variable `nombre-aplicacion`.
2. Crear el directorio de la aplicación con el nombre `.\{nombre-aplicacion}`.
   - Si el directorio ya existe, debe notificarse al usuario y abortar la operación.
   - Si el directorio no existe, debe crearse.
3. Crear el directorio del catálogo con el nombre `.\{nombre-aplicacion}\{nombre-aplicacion}-catalog`.
   - Si el directorio ya existe, debe notificarse al usuario y continuar al siguiente paso.
   - Si el directorio no existe, debe crearse.
4. Crear el directorio del código fuente de la aplicación `.\{nombre-aplicacion}\src`.
   - Si el directorio ya existe, debe notificarse al usuario y continuar al siguiente paso.
   - Si el directorio no existe, debe crearse.
5. Instalar EventCatalog en el directorio del catálogo.
   - Navegar al directorio de catálogo `.\{nombre-aplicacion}\{nombre-aplicacion}-catalog`.
   - Ejecutar el comando `npx @eventcatalog/create-eventcatalog@latest {nombre-aplicacion} --empty`.