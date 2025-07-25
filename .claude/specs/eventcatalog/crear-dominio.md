# Crear un dominio en EventCatalog

## High-level objective

Crear un dominio en EventCatalog para una aplicación existente.

## Mid-level objectives
- Verificar que exista una aplicación creada.
- Verificar que exista un EventCatalog para la aplicación.
- Crear un dominio dentro del EventCatalog de la aplicación.

## Implementation notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/Sinco/Cosmos.EventCatalog).
- IMPORTANTE:No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

### Beginning context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/` Directorio que contendrá los dominios de la aplicación. 

### Ending context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/` Directorio que contendrá el dominio creado.

## Low-Level Tasks
> Ordered from start to finish

1. Obtener el directorio de event catalog
   - El nombre de la aplicación se debe capturar del prompt. A partir del nombre recibido en el prompt, buscar en la raiz del proyecto un directorio que coincida con el nombre de la apliación ya normalizado. Guardar en la variable `{nombre-aplicacion}` el nombre del directorio encontrado.
   - Verificar que el directorio encontrado contenga un directorio `{nombre-aplicacion}-catalog`.
   - Si no se encuentra el directorio o no contiene el directorio `{nombre-aplicacion}-catalog`, abortar la operación y mostrar un mensaje de error al usuario.
   - Guardar en la variable `{ruta-catalogo}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/` encontrado.

2. Verificar que el directorio de dominios exista
   - Buscar el directorio `domains` dentro del directorio `{ruta-catalogo}`. En caso que no exista, crear el directorio.
   - Si el directorio `domains` ya existe, continuar al siguiente paso.

3. Obtener el nombre del dominio
   - Capturar del prompt el nombre del dominio a crear y guardarlo en la variable `{nombre-dominio-original}`.
   - Normalizar el nombre del dominio con las siguientes reglas:
     - Convertir a minúsculas.
     - Reemplazar espacios en blanco por guiones bajos.
     - Eliminar caracteres especiales.     
     - Reemplazar tildes por su versión sin tilde.
     - Reemplazar letras "ñ" por "n".
     - Limpiar espacios iniciales y finales.
     - El nombre normalizado debe guardarse en memoria con la variable `{nombre-dominio}`.
     - Si el nombre del dominio ya existe en el directorio `Domains`, abortar la operación y mostrar un mensaje de error al usuario.
  
4. Crear el directorio del dominio
   - Crear el directorio `{ruta-catalogo}/domains/{nombre-dominio}`.
   
  
5. Crear el archivo de especificación del dominio
   - Crear el archivo `index.mdx` dentro del directorio del dominio.
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
    {resumen sugerido por la IA o dado por el usuario y mejorado por la IA}
    ---

    <NodeGraph />

    ```

