Crea un un evento en la sección events del commandHandler de EventCatalog dentro de una aplicación existente.

## Argumentos

### Obligatorios:
**Nombre aplicacion:** $ARGUMENTS
**Nombre commandHandler:** $ARGUMENTS
**Summary:** $ARGUMENTS
**Propiedades evento:** $ARGUMENTS (lista de propiedades en formato "nombre:tipo" o "nombre:tipo:descripcion", separadas por comas)

### Opcionales (se infieren automáticamente si es posible):
**Nombre subdominio:** $ARGUMENTS
**Nombre dominio:** $ARGUMENTS

### Ejemplo de uso:
```
/crear-evento "MiApp" "CrearPedidoCommandHandler" "Evento que se dispara al crear un pedido" "id:string:Identificador único,nombre:string,fecha:datetime:Fecha de creación"
```

## Pasos a seguir

### 1. Validación inicial
- **VALIDAR** formato de propiedades del evento
- **VERIFICAR** que todos los argumentos obligatorios estén presentes
- **DETENER** si hay errores de formato o argumentos faltantes

### 2. Resolución de contexto
- Sugerir `Nombre aplicacion` si solo existe una aplicación en la carpeta raiz del proyecto
- Buscar el `Nombre commandHandler` dentro de todos los subdominios de la aplicación `Nombre aplicacion`
- Si solo existe un commandHandler con ese nombre, usarlo e inferir `Nombre dominio` y `Nombre subdominio` por la jerarquía de directorios
- Si no existe, sugerir al usuario los commandHandlers disponibles y solicitarle que seleccione uno o que aborte la operación

### 3. Generación del evento
- Sugerir el nombre del evento a partir del `Nombre commandHandler` usando el participio pasado del nombre del commandHandler, por ejemplo: si el commandHandler es `CrearPedidoCommandHandler`, sugerir `PedidoCreado`
- Crear el evento `Nombre evento` dentro del commandHandler `Nombre commandHandler` que está en el subdominio `Nombre subdominio` del dominio `Nombre dominio` del EventCatalog de la aplicación `Nombre aplicacion` usando `.\.claude\specs\eventcatalog\crear-evento.md`

## ⚠️ REGLAS DE EJECUCIÓN

### Ejecución del spec `.claude\specs\eventcatalog\crear-evento.md`:
- **SEGUIR TODOS LOS PASOS** del spec sin saltar ninguno
- **UNA PREGUNTA A LA VEZ** - nunca hacer múltiples preguntas en el mismo mensaje
- **ESPERAR respuesta** del usuario antes de continuar al siguiente paso
- **PREGUNTAR EXPLÍCITAMENTE** por cada dato requerido que falte

### Creación de entidades (paso 7 del spec):
- **EJECUTAR COMPLETAMENTE** `.claude\specs\eventcatalog\crear-entidad.md`
- **USAR** las propiedades proporcionadas en el argumento "Propiedades evento"
- **PREGUNTAR** solo por descripciones faltantes si son opcionales
- **DOCUMENTAR** correctamente el schema Avro de cada entidad

### Manejo de errores:
- **DETENER** inmediatamente si hay errores de validación
- **REPORTAR** claramente el error encontrado
- **SOLICITAR** corrección antes de continuar
- **NO ASUMIR** o inventar datos faltantes
