# Crear solución Cosmos EventSourcing

## High-Level Objective

- Crear una o varias nuevas soluciones de eventSourcing a partir de la plantilla de solución de .Net  `Cosmos.EventSourcing.Template`.

## Mid-Level Objective

- Crear soluciones a partir de la plantilla de solución de .Net `Cosmos.EventSourcing.Template`.
- Impedir que hayan soluciones con nombres duplicados.

## Implementation Notes

- Normalizar el nombre de la solución.
- Debe verificar que esté instalado el SDK de .NET 9.
- Debe verificar que tenga acceso al feed de de nuget `https://pkgs.dev.azure.com/sincosoftsascosmos/Nugets/_packaging/Cosmos.Packages/nuget/v3/index.json`
- En caso que la conexión al feed falle con error Forbidden. Recuerdele al usuario que debe registrar su Personal Access Token (PAT) en su configuración de NuGet.
- Debe verificar que la variable de entorno `COSMOS_FEED_PAT` esté configurada. En caso que no lo esté debe darle la siguiente instrucción al usuario: "Por favor en el archivo .env, configure la variable de entorno `COSMOS_FEED_PAT` con su Personal Access Token (PAT)"
- Debe verificar que la solución no exista antes de crearla. Si hay soluciones con un nombre similar, debe mostrarle las que podrían ser una posible coincidencia y solicitarle aprobación antes de continuar.
- Si solicitan crear más de una solución, cada solución se debe crear secuencialmente, es decir, debe realizar los Low-Level Tasks completos para una solución antes de seguir con la siguiente. En caso de un error en una solución, debe sugerir al usuario corregir el error o continuar con la siguiente solución.

## Context

### Beginning context
- `./src/`  Solo los directorios que estén en este nivel, no se debe adicionar al contexto los contenidos de los subdirectorios.

### Ending context  
- `/src/{nombre-solucion-creada}/`  

## Low-Level Tasks
> Ordered from start to finish

### 1. Verificar prerrequisitos:

**Instrucciones optimizadas:**
```
Ejecutar en paralelo:
- Bash: dotnet --version
- Read: .env (verificar COSMOS_FEED_PAT)
- Read: .gitignore (verificar que contenga .env)
- Bash: dotnet new list | grep -i cosmos
- Bash: dotnet nuget list source
```

**Validaciones:**
- SDK .NET 9 debe estar instalado (output: 9.x.xxx)
- Variable COSMOS_FEED_PAT debe existir en .env
- .gitignore debe contener .env
- Template "cosmos-eventsourcing" debe aparecer en la lista
- Feed Cosmos debe estar en la lista de sources de NuGet

### 2. Normalizar el nombre de la solución

**Instrucciones optimizadas:**
```
Aplicar transformaciones al nombre en este orden exacto:
1. trim() - eliminar espacios inicio/fin
2. toLowerCase() - convertir a minúsculas
3. replace(/\s+/g, '_') - espacios a guiones bajos
4. replace(/[áàäâ]/g, 'a').replace(/[éèëê]/g, 'e').replace(/[íìïî]/g, 'i').replace(/[óòöô]/g, 'o').replace(/[úùüû]/g, 'u')
5. replace(/ñ/g, 'n')
6. replace(/[^a-z0-9_]/g, '') - eliminar caracteres especiales
```

### 3. Verificar que no exista una solución con el nombre normalizado

**Instrucciones optimizadas:**
```
LS: path="./src"
1. Verificar si existe directorio con nombre-solucion-normalizado exacto
2. Buscar nombres similares usando distancia de Levenshtein o similitud de cadenas
3. Si existe el nombre exacto: mostrar error y terminar proceso
4. Si existen nombres similares (>70% similitud): 
   - Mostrar lista de nombres similares encontrados
   - Solicitar aprobación explícita del usuario con el mensaje:
     "Se encontraron soluciones con nombres similares:
     - {nombre_existente_1}
     - {nombre_existente_2}
     
     ¿Desea continuar creando la solución '{nombre-solucion-normalizado}'? (s/n)"
   - Si el usuario responde 'n' o 'no': terminar proceso
   - Si el usuario responde 's' o 'sí': continuar con el proceso
```

### 4. Crear el directorio de la solución

**Instrucciones optimizadas:**
```
Bash: mkdir -p "./src/{nombre-solucion-normalizado}"
```

### 5. Identificación de puertos

**Instrucciones optimizadas:**
```
Ejecutar para cada solución existente:
- Read: ./src/{solucion}/docker-compose.override.yaml

Extraer puertos con regex:
- Comandos API: /^\s*-\s*"(\d+):8080"/m (contexto: {solucion}comandos-api)
- Consultas API: /^\s*-\s*"(\d+):8080"/m (contexto: {solucion}consultas-api)
- Database: /^\s*-\s*"(\d+):5432"/m (contexto: {solucion}database)

Seleccionar puertos libres:
- projectComandosHttpPort: primer puerto libre en [8010-8050]
- projectConsultasHttpPort: primer puerto libre en [9010-9050]
- projectNpgDatabasePort: primer puerto libre en [5433-5499]
```

### 6. Crear la solución de eventSourcing

**Instrucciones optimizadas:**
```
Bash: cd "./src/{nombre-solucion-normalizado}" && dotnet new cosmos-eventsourcing -n {nombre-solucion-normalizado} --projectComandosHttpPort {port1} --projectConsultasHttpPort {port2} --projectNpgDatabasePort {port3}
```

### 7. Verificar que la solución se haya creado correctamente

**Instrucciones optimizadas:**
```
Bash: cd "./src/{nombre-solucion-normalizado}" && dotnet build {nombre-solucion-normalizado}.sln
```

**Validación:**
- Build debe completar con "Build succeeded" y "0 Error(s)"

### 8. Limpia la memoria del agente

**Instrucciones optimizadas:**
```claude
/clear
```