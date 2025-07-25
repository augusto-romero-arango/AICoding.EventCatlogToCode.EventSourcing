# Crear un subdominio en EventCatalog

## High-level objective

Crear un subdominio dentro de un dominio en EventCatalog para una aplicación existente.

## Mid-level objectives
- Verificar que exista una aplicación creada.
- Verificar que exista un EventCatalog para la aplicación.
- Verificar que exista el dominio padre del subdominio dentro del EventCatalog de la aplicación.
- Crear un subdominio dentro del EventCatalog de la aplicación.

## Implementation notes

- EventCatalog es un proyecto para documentar arquitecturas eventuales. Su documentación se encuentra en [EventCatalog](https://github.com/Sinco/Cosmos.EventCatalog).
- IMPORTANTE:No se deben afectar archivos por fuera del directorio enunciado en la sección `Context` de esta especificación.

## Context

### Beginning context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/` Directorio que contendrá el dominio padre.

### Ending context

- `./{nombre-aplicacion}/{nombre-aplicacion-catalog}/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/}` Directorio que contendrá el subdominio creado.

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

3. Verificar que el dominio padre exista:
   - Capturar del prompt el nombre del dominio padre. Buscarlo dentro del directorio `domains` en su forma normalizada. Si lo encuentra, debe guardarlo en la variable `{nombre-dominio}`.
   - Si no se encuentra el dominio padre, sugerir al usuario los nombre de dominios disponibles y solicitarle que seleccione uno o que aborte la operación.
   - Guardar en la variable `{ruta-dominio}` el nombre del directorio `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/` encontrado.
   

4. Obtener el nombre del subdominio
   - Capturar del prompt el nombre del subdominio a crear y guardarlo en la variable `{nombre-subdominio-original}`.
   - Normalizar el nombre del subdominio con las siguientes reglas:
     - Convertir a minúsculas.
     - Reemplazar espacios en blanco por guiones bajos.
     - Eliminar caracteres especiales.     
     - Reemplazar tildes por su versión sin tilde.
     - Reemplazar letras "ñ" por "n".
     - Limpiar espacios iniciales y finales.
     - El nombre normalizado debe guardarse en memoria con la variable `{nombre-subdominio}`.
     - Si el nombre del subdominio ya existe en el directorio `subdomains`, abortar la operación y mostrar un mensaje de error al usuario.
5. Crear el directorio del subdominio
   - Crear el directorio `{ruta-dominio}/subdomains/{nombre-subdominio}`

6. Crear el archivo de especificación del subdominio
   - Crear el archivo `index.mdx` dentro del directorio del subdominio.
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
7. Asociar el subdominio al dominio padre
   - Agregar una referencia al subdominio en el archivo `index.mdx` del dominio padre:
   - Modificar el archivo `{ruta-dominio}/index.mdx`:
     - En el frontmatter, agregar el subdominio a la propiedad `domains`:
      ```mdx
      ---
      ...

      domains:
         - {nombre-subdominio}

      ...
      
      ---
      ```
      - Si el frontmatter ya contiene la propiedad `domains`, agregar el subdominio a la lista existente.
      - Si no existe debe crear la propiedad `domains` debajo de la propiedad `summary` del frontmatter.




