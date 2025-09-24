# Instalar Event Catalog

## High-level objective

Crear una instancia limpia de EventCatalog compatible para entornos Windows y macOS sin usar devcontainers.

## Mid-level objectives

- Instalar EventCatalog directamente en el sistema local.

## Implementation notes

- Se usará la instalación directa con npm/npx.
- IMPORTANTE: No se deben afectar archivos por fuera del directorio de la aplicación que se creará con este proceso.

## Low-level steps

Los pasos se deben ejecutar secuencialmente y en el orden indicado. Cada paso debe completarse antes de pasar al siguiente.

1. Capturar el nombre de la aplicación para usar como nombre del proyecto EventCatalog, y guardarlo en una variable llamada `{nombre-aplicacion}`.

2. Verificar que Node.js esté instalado en el sistema:
   ```bash
   node --version
   npm --version
   ```

3. Crear el directorio del proyecto si no existe:
   ```bash
   mkdir {nombre-aplicacion}
   cd {nombre-aplicacion}
   ```

4. Crear el proyecto EventCatalog usando npx:

   Este comando se demora. Esperar a que termine con un timeout de 10 minutos.
   ```bash
   npx @eventcatalog/create-eventcatalog@latest . --empty --organization-name "Cosmos-Sincosoft"
   ```

5. Instalar las dependencias del proyecto:

   Este comando se demora. Esperar a que termine con un timeout de 10 minutos.
   ```bash
   npm install
   ```

6. Verificar que la instalación fue exitosa ejecutando el servidor de desarrollo:
   ```bash
   npm run dev
   ```

7. Notificar al usuario que la instalación ha finalizado:
   ```bash
   echo "🎉 La instalación de EventCatalog ha finalizado exitosamente."
   echo "El servidor de desarrollo está corriendo en http://localhost:3000"
   echo "Presiona Ctrl+C para detener el servidor cuando hayas terminado de verificar."
   ```

## Notas adicionales

- Asegúrate de tener Node.js versión 16 o superior instalado.
- El proceso es mucho más simple sin devcontainers pero requiere tener el entorno Node.js configurado localmente.
- El proyecto se creará en el directorio actual, no en un subdirectorio como en la versión con devcontainer.