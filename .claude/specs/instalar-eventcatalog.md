# Instalar Event Catalog

## High-level objective

Crear una instancia limpia de EventCatalog compatible para entornos Windows y macOS.

## Mid-level objectives

- Instalar EventCatalog en un directorio específico.

## Implementation notes

- Se usarán devcontainers para la instalación.
- IMPORTANTE: No se deben afectar archivos por fuera del directorio de la aplicación que se creará con este proceso.

## Low-level steps

Los pasos se deben ejecutar secuencialmente y en el orden indicado. Cada paso debe completarse antes de pasar al siguiente.

1. Capturar el nombre de la aplicación para usar como nombre del proyecto EventCatalog, y guardarlo en una variable llamada `{nombre-aplicacion}`.
2. Verificar que el host esté corriendo docker desktop.
3. Crear el archivo `.devcontainer/devcontainer.json` en el directorio de trabajo actual. El contenido del archivo debe ser:

   ```json
    {
        "name": "EventCatalog Development",
        "image": "node:18",
        "features": {
            "ghcr.io/devcontainers/features/common-utils:2": {
                "installZsh": false,
                "installOhMyZsh": false,
                "upgradePackages": true
            }
        },
        "containerEnv": {
        "NODE_ENV": "development",
        "NODE_OPTIONS": "--max-old-space-size=8192",
        "HOST": "0.0.0.0",
        "PORT": "3000",
        "CHOKIDAR_USEPOLLING": "true"
        },
        "runArgs": ["--memory=8g", "--cpus=4", "--shm-size=2g"],
        "customizations": {
        "vscode": {
            "extensions": [
                "esbenp.prettier-vscode",
                "bradlc.vscode-tailwindcss",
                "ms-vscode.vscode-typescript-next",
                "astro-build.astro-vscode",
                "unifiedjs.vscode-mdx",
                "ms-vscode.vscode-json"
            ],
            "settings": {
                "terminal.integrated.defaultProfile.linux": "bash",
                "editor.formatOnSave": true,
                "editor.defaultFormatter": "esbenp.prettier-vscode",
                "files.associations": {
                    "*.mdx": "mdx"
                },
                "typescript.preferences.includePackageJsonAutoImports": "off",
                "typescript.suggest.autoImports": false,
                "extensions.autoUpdate": false,
                "files.watcherExclude": {
                    "**/node_modules/**": true,
                    "**/.git/objects/**": true,
                    "**/.git/subtree-cache/**": true,
                    "**/dist/**": true,
                    "**/.astro/**": true
                },
                "search.exclude": {
                    "**/node_modules": true,
                    "**/dist": true,
                    "**/.astro": true
                },
                "typescript.disableAutomaticTypeAcquisition": true,
                "extensions.autoCheckUpdates": false,
                "telemetry.telemetryLevel": "off"
            }
        },
        "forwardPorts": [3000, 4321, 4322],
        "portsAttributes": {
        "3000": {
            "label": "EventCatalog Dev Server",
            "onAutoForward": "openBrowser"
            },
        "4321": {
            "label": "Astro Dev Server",
            "onAutoForward": "ignore"
            },
        "4322": {
            "label": "HMR Port",
            "onAutoForward": "ignore"
            }
        },
        "appPort": [3000],
        "remoteUser": "node"
    }
    ```
4. Crear el archivo `.devcontainer/postCreateCommand.sh` en el directorio de trabajo actual:
   - IMPORTANTE: reemplazar `{nombre-aplicacion}` por el nombre de la aplicación que se capturó en el prompt. 
   - El contenido del archivo debe ser:  
   ```bash
   #!/bin/bash

    echo "🚀 Setting up EventCatalog development environment..."

    # Install system dependencies for native modules
    echo "📦 Installing system dependencies..."
    apt-get update
    apt-get install -y python3 make g++ libc6-dev

    # Change to workspace directory 
    cd /workspaces/{nombre-aplicacion}

    # Create EventCatalog project if it doesn't exist
    if [ ! -d "{nombre-aplicacion}" ] || [ ! -f "{nombre-aplicacion}/package.json" ]; then
        echo "🏗️ Creating new EventCatalog project..."
        npx --yes @eventcatalog/create-eventcatalog@latest "{nombre-aplicacion}" --empty --organization-name "Cosmos-Sincosoft"
    fi

    # Change to project directory
    cd {nombre-aplicacion}

    # Install dependencies with npm (better native module support)
    echo "📚 Installing dependencies..."
    if [ ! -d "node_modules" ]; then
        npm install --include=optional
    fi

    echo "✅ EventCatalog development environment ready!"
    echo "🎯 Run 'cd {nombre-aplicacion} && npm run dev' to start the development server"
    echo "🌐 EventCatalog will be available at http://localhost:3000"
   ```
5. Instalar el CLI de devcontainer e iniciar el devcontainer:
   ```bash
   # Instalar el CLI globalmente (si no está instalado)
   npm install -g @devcontainers/cli
   
   # Iniciar el devcontainer desde el directorio de trabajo actual
   devcontainer up --workspace-folder .
   ```

6. Ejecutar el script postCreateCommand.sh dentro del devcontainer:
   ```bash
   # Ejecutar el script de instalación con timeout de 10 minutos
   devcontainer exec --workspace-folder . bash .devcontainer/postCreateCommand.sh
   ```
   **IMPORTANTE para Claude:** Debes monitorear activamente la ejecución de este comando y usar un timeout de 600000ms (10 minutos). No continúes hasta que la instalación de EventCatalog termine exitosamente. Si el comando falla o se interrumpe, reporta el error inmediatamente.


7. Eliminar el contenedor usando comandos de Docker:
   ```bash
   # Listar contenedores para obtener el ID del contenedor
   docker ps -a | grep "{nombre-aplicacion}"
   
   # Detener y eliminar el contenedor específico
   docker stop <container-id>
   docker rm <container-id>
   
   ```

8. Instalar dependencias de npm en el proyecto EventCatalog:
   ```bash
   # Navegar al directorio del proyecto EventCatalog
   cd {nombre-aplicacion}
   
   # Instalar todas las dependencias
   npm install
   
   ```

9. Notificar al usuario que la instalación ha finalizado:
    ```bash
    echo "🎉 La instalación de EventCatalog ha finalizado exitosamente."
    echo "Puedes iniciar el servidor de desarrollo con 'npm run dev' dentro del directorio {nombre-aplicacion}."
    ```



