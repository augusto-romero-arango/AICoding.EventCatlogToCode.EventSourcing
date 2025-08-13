Documenta un evento completo ejecutando comandos internos secuencialmente.

**Parámetros requeridos (todos en $ARGUMENTS):**
```
--evento <nombre-evento>
--aplicacion <nombre-aplicacion>
--subdominio <nombre-subdominio>
--dominio <nombre-dominio>
--aggregateroot <nombre-aggregateroot>
--propiedades-evento <prop1:tipo,prop2:tipo,...>
--propiedades-comando <prop1:tipo,prop2:tipo,...>
--guards <regla1,regla2,...>
--business-rules <regla1,regla2,...>
```

**Ejemplo de uso:**
```
/documentar-evento-v2 --evento "UsuarioCreado" --aplicacion "MiApp" --subdominio "usuarios" --dominio "identidad" --aggregateroot "Usuario" --propiedades-evento "Id:string,Nombre:string,Email:string,Direccion:Direccion" --propiedades-comando "Id:string,Nombre:string,Email:string,DireccionCalle:string,DireccionCiudad:string" --guards "El email debe ser único,El nombre no puede estar vacío" --business-rules "Calcular puntos de lealtad al crear usuario,Enviar email de bienvenida"
```

**Nota:** Los summaries se generan automáticamente basándose en el contexto del evento, comando y CommandHandler.

## Validación y Recolección de Parámetros

### 0. Parseo de Argumentos Iniciales
```
1. **Parsear $ARGUMENTS** al inicio del comando:
   - Extraer todos los parámetros proporcionados: --evento, --aplicacion, --subdominio, etc.
   - Crear mapa de parámetros existentes vs faltantes
   - Solo recopilar interactivamente los parámetros que NO fueron proporcionados

2. **Logging de parámetros detectados:**
   "Parámetros detectados: {lista-de-parametros-existentes}"
   "Parámetros faltantes: {lista-de-parametros-faltantes}"
```

### 1. Detección Automática de Aplicación
- **Buscar directorios de aplicación** en el directorio actual
- **Si hay UNA sola aplicación**: asignar automáticamente con mensaje `✅ Aplicación detectada automáticamente: {nombre}`
- **Si hay múltiples**: preguntar al usuario con opciones numeradas
- **Si no hay ninguna**: solicitar crear una nueva

### 2. Recolección Interactiva de Parámetros Faltantes
Para cada parámetro faltante, preguntar **UNO A LA VEZ** en este orden:

#### a) **--evento** (si falta)
```
Pregunta: "Necesito el **nombre del evento**. ¿Cuál quieres usar?"
Validación: No debe estar vacío, se normalizará automáticamente
```

#### b) **--subdominio** y **--dominio** (si faltan)
```
1. Buscar subdominios existentes en la aplicación detectada
2. Para cada subdominio encontrado, identificar su dominio padre
3. Si existe un subdominio relevante basado en el nombre del evento:
   - Si el subdominio existe en UN SOLO dominio: Sugerirlo con formato "subdominio (dominio)" e inferir automáticamente el dominio
   - Si el subdominio existe en MÚLTIPLES dominios: Mostrar opciones "subdominio (dominio1)", "subdominio (dominio2)" para que el usuario elija
4. Pregunta: "Sugiero el subdominio **{sugerencia} ({dominio})** para el evento {evento}. ¿Usamos este o prefieres otro?"
5. Si no acepta la sugerencia o no hay sugerencias: "¿Qué subdominio prefieres usar? (formato: subdominio o subdominio(dominio) si hay ambigüedad)"
6. Una vez seleccionado el subdominio, inferir automáticamente el dominio correspondiente
7. Si hay ambigüedad en el nombre del subdominio, solicitar aclaración del dominio
```

#### c) **--aggregateroot** (si falta)
```
1. **Buscar AggregateRoots existentes en el subdominio:**
   - Buscar en `{aplicacion}/{aplicacion}-catalog/domains/{dominio}/subdomains/{subdominio}/entities/`
   - Identificar entidades con `aggregateRoot: true`
   - Crear lista de AggregateRoots disponibles

2. **Presentar opciones al usuario:**
   - **Si existen AggregateRoots**: 
     Pregunta: "¿Cuál **AggregateRoot** modifica el evento {evento}?"
     Opciones:
     1. {AggregateRoot1}
     2. {AggregateRoot2}
     3. [Otro AggregateRoot específico]
     "Selecciona el número del AggregateRoot existente o escribe el nombre del nuevo AggregateRoot a crear:"
   
   - **Si NO existen AggregateRoots**:
     Pregunta: "No se encontraron AggregateRoots en el subdominio {subdominio}. ¿Cuál es el **AggregateRoot** que modifica este evento {evento}?"

3. **Validación:**
   - Si selecciona un AggregateRoot existente: usar directamente
   - Si especifica un nombre nuevo: validar que no esté vacío y normalizar
   - Se validará/creará posteriormente según corresponda
```


#### d) **--propiedades-evento** (si falta)
```
1. Pregunta inicial: "¿Qué **propiedades** debe tener el evento {evento}? 
   Puedes ingresar una o varias separadas por comas.
   Formato: Prop1:tipo,Prop2:tipo
   Ejemplos: 
   - Tipos primitivos: Id:string,Nombre:string,Activo:boolean
   - Tipos complejos: Usuario:Usuario,Direccion:Direccion,Producto:Producto"

2. Validación y registro de propiedades:
   - Validar formato correcto: {Propiedad}:{Tipo}
   - **Para tipos primitivos**: registrar directamente
   - **Para tipos complejos**: 
     a) Detectar que el tipo no es primitivo (no es string, int, decimal, boolean, datetime, guid)
     b) Registrar como objeto complejo para posterior creación de entidad
     c) Validar que el nombre del tipo sigue convenciones (PascalCase)
     d) **Definición recursiva de propiedades del objeto complejo**:
        - Pregunta: "El tipo **{Tipo}** es un objeto complejo. ¿Qué propiedades debe tener?"
        - Formato: "Prop1:tipo,Prop2:tipo (ej: Id:string,Nombre:string,Activo:boolean)"
        - **Validar recursivamente**: si alguna propiedad es también un objeto complejo, repetir el proceso
        - **Registrar esquema completo** del objeto para creación posterior de entidad
        - **Evitar ciclos**: detectar referencias circulares y abortar con error claro

3. Bucle de confirmación:
   Pregunta: "¿Necesitas agregar **más propiedades** al evento {evento}? (s/n)"
   - Si "s": "¿Qué **propiedades adicionales**?"
   - Si "n": Continuar con validación de modificación de AggregateRoot
   
4. Para cada propiedad del evento:
   Pregunta: "¿La propiedad **{propiedad}** modifica el AggregateRoot {aggregateroot}? (s/n)"
   - Por defecto: "s" (sí modifica)
   - Marcar propiedades que modifican el AggregateRoot
   
5. **Registro de objetos complejos detectados**:
   - Mantener lista de todos los tipos complejos encontrados
   - Excluir el AggregateRoot de la lista (se maneja por separado)
   - Esta lista se usará en el paso 3 para crear las entidades necesarias

6. **Flujo de definición recursiva de objetos complejos**:
```
Para cada objeto complejo detectado (ej: Direccion:Direccion):
1. Pregunta: "El tipo **Direccion** es un objeto complejo. ¿Qué propiedades debe tener?"
2. Usuario responde: "Calle:string,Numero:int,Ciudad:string,Coordenadas:Coordenadas"
3. Analizar respuesta:
   - Calle:string → primitivo, registrar
   - Numero:int → primitivo, registrar  
   - Ciudad:string → primitivo, registrar
   - Coordenadas:Coordenadas → complejo, procesar recursivamente
4. Para Coordenadas:
   Pregunta: "El tipo **Coordenadas** es un objeto complejo. ¿Qué propiedades debe tener?"
   Usuario responde: "Latitud:decimal,Longitud:decimal"
   Analizar: ambos primitivos, registrar
5. Registrar esquema completo:
   Direccion: { Calle:string, Numero:int, Ciudad:string, Coordenadas:Coordenadas }
   Coordenadas: { Latitud:decimal, Longitud:decimal }
6. Continuar con siguiente objeto complejo o siguiente paso
```

7. **Control de ciclos y validaciones**:
   - **Detectar referencias circulares**: si TipoA referencia TipoB y TipoB referencia TipoA
   - **Error**: "Error: Referencia circular detectada entre {TipoA} y {TipoB}. Los objetos no pueden referenciarse mutuamente."
   - **Validar profundidad**: límite máximo de 5 niveles de anidamiento
   - **Auto-referencia**: permitida (ej: Categoria que contiene Categoria padre)

8. Repetir hasta que el usuario confirme que no faltan más propiedades
```

#### e) **--business-rules** (si falta)
```
1. Pregunta inicial: "¿El evento {evento} requiere **reglas de negocio específicas** en el CommandHandler?
   Las business rules son lógica de negocio que se ejecuta al procesar el comando.
   Ejemplos: 'Calcular puntos de lealtad', 'Enviar notificación por email', 'Actualizar estadísticas'
   Ingresa las reglas o 'ninguna' si no aplica."

2. Validación:
   - Aceptar 'ninguna', 'no', 'n' para omitir
   - Cada regla debe describir una acción de negocio específica
   - Separar múltiples reglas por comas

3. Bucle para múltiples business rules:
   Pregunta: "¿Necesitas documentar **otra regla de negocio** para el evento {evento}? (s/n)"
   - Si "s": "¿Qué **regla de negocio adicional** quieres documentar?"
   - Si "n": Continuar con el siguiente paso
   - Repetir hasta que el usuario confirme que no hay más business rules

4. Impacto en CommandHandler:
   - Estas reglas se incluirán en la documentación del CommandHandler
   - Servirán como guía para la implementación del handler
   - Se registrarán como parte de la lógica de procesamiento del comando
```

#### f) **--propiedades-comando** (si falta)
```
1. Auto-generar propiedades por defecto:
   - SIEMPRE incluir como primera propiedad: Id{aggregateroot}:guid (ej: IdEmpleado:guid)
   - Tomar todas las propiedades del evento
   - **Aplanar objetos complejos** a tipos primitivos
   - Solo mantener: string, int, decimal, boolean, datetime, guid

2. Mostrar propiedades generadas:
   "Propiedades sugeridas para el comando {comando}: Id{aggregateroot}:guid,{propiedades-aplanadas}"
   
3. Pregunta de confirmación:
   "¿Estas propiedades son correctas para el comando {comando}? (s/n)"
   - Si "s": Usar propiedades generadas
   - Si "n": "¿Qué propiedades prefieres usar? (solo tipos primitivos, debe incluir Id{aggregateroot}:guid como primera propiedad)"

4. Validación: 
   - Permitir tipos primitivos: string, int, decimal, boolean, datetime, guid
   - **PERMITIR enums**: Los enums son válidos en comandos
   - OBLIGATORIO: La primera propiedad debe ser Id{aggregateroot}:guid

5. Bucle adicional (si es necesario):
   Pregunta: "¿Necesitas agregar **más propiedades primitivas** al comando {comando}? (s/n)"
```

#### g) **--guards** (si falta)
```
1. Pregunta inicial: "¿El comando {comando} requiere **reglas de validación o guards**?
   Los guards son reglas que se deben cumplir antes de ejecutar el comando.
   Ejemplos: 'El email debe ser único', 'El usuario debe estar activo', 'La edad debe ser mayor a 18'
   Ingresa las reglas o 'ninguna' si no aplica."

2. Validación:
   - Aceptar 'ninguna', 'no', 'n' para omitir
   - Cada regla debe ser una descripción clara en texto
   - Separar múltiples reglas por comas

3. Bucle para múltiples guards:
   Pregunta: "¿Necesitas agregar **más reglas de validación** al comando {comando}? (s/n)"
   - Si "s": "¿Qué **regla adicional** quieres agregar?"
   - Si "n": Continuar con el siguiente paso
   - Repetir hasta que el usuario confirme que no hay más guards

4. Registro de guards:
   - Mantener lista de todos los guards especificados
   - Estos se utilizarán en la documentación del comando y CommandHandler
```

### 3. Validaciones Post-Recolección
1. **Normalizar nombres:** Aplicar reglas de normalización a todos los nombres
2. **Validar unicidad:** Verificar que no existe el evento en la aplicación
3. **Deducir nombres y summaries derivados:**
   - **Comando**: Convertir evento pasado a verbo infinitivo
     - Ejemplos: `PedidoAgregado` → `AgregarPedido`, `UsuarioCreado` → `CrearUsuario`
     - Lógica: Remover sufijos pasados (-ado, -ido) y convertir a verbo infinitivo
   - **CommandHandler**: `{comando}CommandHandler`
     - Ejemplos: `AgregarPedido` → `AgregarPedidoCommandHandler`
   - **Summaries contextualizados:**
     - **Evento**: Analizar el contexto completo para generar un summary específico:
       - Considerar: dominio, subdominio, propiedades del evento, AggregateRoot afectado
       - Redactar descripción que explique el propósito de negocio específico
       - Incluir información sobre qué cambios representa en el sistema
     - **Comando**: Analizar la intención de negocio para generar summary específico:
       - Considerar: propiedades del comando, guards, business rules, AggregateRoot objetivo
       - Redactar descripción que explique la acción de negocio específica que ejecuta
       - Incluir información sobre las validaciones y reglas que aplica
     - **CommandHandler**: Analizar la responsabilidad completa para generar summary específico:
       - Considerar: comando que recibe, evento que produce, business rules, guards
       - Redactar descripción que explique el procesamiento específico que realiza
       - Incluir información sobre las validaciones y transformaciones que ejecuta

### 4. Lógica de Conversión de Nombres y Summaries
```
Transformación Evento → Comando:
1. Identificar el patrón del evento y extraer componentes
2. Aplicar reglas de transformación específicas
3. Construir comando: {Verbo}{Entidad}

Patrones de transformación:
- *Creado → Crear*
- *Agregado → Agregar* 
- *Actualizado → Actualizar*
- *Eliminado → Eliminar*
- *Enviado → Enviar*
- *Procesado → Procesar*
- *Registrado → Registrar*
- *Asignado → Asignar*

Ejemplos específicos:
- "UsuarioCreado" → "CrearUsuario"
- "PedidoAgregado" → "AgregarPedido"  
- "InformacionSalarialRegistrada" → "RegistrarInformacionSalarial"
- "RolUsuarioAsignado" → "AsignarRolUsuario"

Algoritmo de conversión:
1. Si el evento termina en "Registrada": Extraer parte antes de "Registrada" → "Registrar{Parte}"
2. Si el evento termina en "Creado": Extraer parte antes de "Creado" → "Crear{Parte}"  
3. Si el evento termina en "Asignado": Extraer parte antes de "Asignado" → "Asignar{Parte}"
4. Para patrones complejos, analizar semánticamente y aplicar transformación más apropiada
```

### 5. Lógica de Aplanamiento de Propiedades para Comandos
```
Los comandos pueden contener tipos primitivos y enums. Objetos complejos deben ser aplanados:

Ejemplo de aplanamiento:
┌─────────────────────────────┬─────────────────────────────────┐
│ Propiedades del Evento      │ Propiedades del Comando         │
├─────────────────────────────┼─────────────────────────────────┤
│ Id:string                   │ Id:string                       │
│ Nombre:string               │ Nombre:string                   │
│ Usuario:Usuario             │ UsuarioId:string               │
│ Fecha:datetime              │ Fecha:datetime                  │
│ Estado:enum(Activo,Inactivo)│ Estado:enum(Activo,Inactivo)    │
│ Direccion:Direccion         │ DireccionCalle:string          │
│                             │ DireccionCiudad:string         │
│                             │ DireccionCodigoPostal:string   │
└─────────────────────────────┴─────────────────────────────────┘

Reglas de aplanamiento:
1. Tipos primitivos: mantener igual
2. **Enums: mantener igual** (los enums son permitidos en comandos)
3. Objetos complejos: convertir a {ObjetoPropiedad}:{tipo-primitivo}
4. Si es entidad principal: usar {Objeto}Id:string
5. Propiedades anidadas: usar {Objeto}{Propiedad}:{tipo-primitivo}
```

Ejemplos de summaries contextualizados (reemplazan las plantillas genéricas):

**Ejemplo 1: InformacionOrganizacionalRegistrada**
- **Evento**: "Evento que se dispara cuando se completa el registro de la información organizacional de un empleado, incluyendo cargo, área, sede y nómina asignados"
- **Comando**: "Comando que actualiza la información organizacional de un empleado especificando su cargo, área de trabajo, sede de asignación y tipo de nómina"
- **CommandHandler**: "Handler responsable de procesar la asignación de información organizacional a empleados, validando la existencia del empleado y la coherencia de los datos organizacionales"

**Ejemplo 2: PedidoCancelado** 
- **Evento**: "Evento que se dispara cuando un pedido es cancelado por el cliente o automáticamente por el sistema debido a problemas de inventario o pago"
- **Comando**: "Comando para cancelar un pedido existente, validando que esté en estado cancelable y especificando el motivo de cancelación"
- **CommandHandler**: "Handler responsable de procesar cancelaciones de pedidos, liberando inventario reservado y gestionando reembolsos cuando aplique"

**Patrones de Generación Contextual:**
1. **Analizar contexto de dominio**: ¿En qué área de negocio opera?
2. **Identificar propósito específico**: ¿Qué problema de negocio resuelve?
3. **Considerar validaciones**: ¿Qué reglas y guards aplica?
4. **Explicar impacto**: ¿Qué cambios produce en el sistema?
5. **Usar lenguaje de negocio**: Evitar tecnicismos, enfocarse en valor
```

## Ejecución Secuencial de Comandos

Una vez validados todos los parámetros, ejecutar en orden:

### 1. Crear CommandHandler
Ejecutar comando interno: `crear-commandHandler.md` con parámetros:
- Nombre: `{comando}CommandHandler`
- Aplicación: `{aplicacion}`
- Subdominio: `{subdominio}`
- Dominio: `{dominio}`
- Summary: **Generar contextualizadamente**: Analizar comando que recibe, evento que produce, business rules, guards y dominio para redactar summary específico que explique la responsabilidad completa del handler

### 2. Validar y Crear AggregateRoot
**IMPORTANTE**: El AggregateRoot es un parámetro obligatorio y debe ser validado/creado

1. **Validar AggregateRoot existente:**
   - Buscar en `{aplicacion}/{aplicacion}-catalog/domains/{dominio}/subdomains/{subdominio}/entities/{aggregateroot}/`
   - **Si NO existe**: Crear AggregateRoot con solo la propiedad ID

2. **Crear AggregateRoot (si no existe):**
   ```
   Ejecutar: crear-entidad.md con parámetros:
   - Nombre: {aggregateroot}
   - Aplicación: {aplicacion}
   - Subdominio: {subdominio}
   - Dominio: {dominio}
   - AggregateRoot: true
   - Propiedades: "Id:string" (solo Id inicialmente)
   ```

3. **Actualizar propiedades del AggregateRoot:**
   - Identificar propiedades del evento que modifican el AggregateRoot
   - **Para cada propiedad marcada como "modifica AggregateRoot":**
     - Verificar si ya existe en el AggregateRoot
     - Si NO existe: Agregarla a las propiedades del AggregateRoot
     - Actualizar archivo del AggregateRoot

4. **Propiedades adicionales del AggregateRoot (mutaciones con valores específicos):**
   ```
   Pregunta: "¿El evento {evento} establece **propiedades adicionales** en el AggregateRoot {aggregateroot} con valores específicos (no del evento)?
   Por ejemplo: Estado='En Registro', FechaCreacion=DateTime.Now, Version=1, etc.
   Ingresa las propiedades con sus valores o 'ninguna' si no aplica.
   Formato: Propiedad=Valor,Propiedad2=Valor2"
   
   Validación:
   - Aceptar 'ninguna', 'no', 'n' para omitir
   - Formato: Propiedad=Valor separado por comas
   - Valores pueden ser: strings ("Estado"), numbers (1), booleans (true), expressions (DateTime.Now), enums (EstadoEmpleado.EnRegistro)
   
   Procesamiento de propiedades:
   1. **Para cada propiedad especificada:**
      - Verificar si ya existe en el AggregateRoot
      - Si NO existe: Agregarla a las propiedades del AggregateRoot
      
   2. **Para propiedades de tipo enum:**
      - Si la propiedad no existe: Registrar como nueva propiedad enum en el AggregateRoot
      - Si la propiedad ya existe como enum: Verificar si el valor especificado está en los valores aceptados
      - Si es un nuevo valor del enum: Agregarlo a los valores aceptados de la propiedad enum
      - Formato del valor: TipoEnum.Valor (ej: EstadoEmpleado.EnRegistro)
      
   3. **Actualización del archivo del AggregateRoot:**
      - Agregar nuevas propiedades en la sección properties
      - Para enums nuevos: crear la definición completa del enum
      - Para enums existentes: actualizar los valores aceptados
   ```

### 3. Validar y Crear Entidades del Evento
**IMPORTANTE**: Solo crear entidades para tipos complejos que NO sean el AggregateRoot

1. **Analizar propiedades del evento:**
   - Identificar todos los tipos complejos en `--propiedades-evento`
   - Los tipos primitivos (string, int, decimal, boolean, datetime, guid) se ignoran
   - **Excluir el AggregateRoot** (ya procesado en el paso anterior)

2. **Para cada tipo complejo encontrado (excepto AggregateRoot):**
   - Buscar entidad existente en `{aplicacion}/{aplicacion}-catalog/domains/{dominio}/subdomains/{subdominio}/entities/`
   - **Si NO existe**: Ejecutar automáticamente `crear-entidad.md` con `aggregateRoot: false`
   - **Si existe**: Validar compatibilidad con schema.avro del evento

### 4. Crear Evento
Ejecutar comando interno: `crear-evento.md` con parámetros:
- Nombre: `{evento}`
- Aplicación: `{aplicacion}`
- Subdominio: `{subdominio}`
- Dominio: `{dominio}`
- Summary: **Generar contextualizadamente**: Analizar dominio, subdominio, propiedades del evento y AggregateRoot afectado para redactar summary específico que explique el propósito de negocio y los cambios que representa
- Propiedades: `{propiedades-evento}`

**Post-creación del evento:**
- El evento generará automáticamente su `schema.avro`
- **Análisis del schema.avro generado:**
  1. Leer el archivo `{aplicacion}/{aplicacion}-catalog/events/{evento}/schema.avro`
  2. Identificar todos los tipos complejos definidos en el schema
  3. Verificar que cada tipo complejo tenga su entidad correspondiente
  4. Si falta alguna entidad, ejecutar automáticamente `crear-entidad.md`
  5. Validar que las propiedades de las entidades coincidan con el schema
- Si hay inconsistencias, reportar error y abortar ejecución

### 5. Crear Comando
Ejecutar comando interno: `crear-comando.md` con parámetros:
- Nombre: `{comando}`
- Aplicación: `{aplicacion}`
- Subdominio: `{subdominio}`
- Dominio: `{dominio}`
- Summary: **Generar contextualizadamente**: Analizar propiedades del comando, guards, business rules y AggregateRoot objetivo para redactar summary específico que explique la acción de negocio y las validaciones que aplica
- Propiedades: `{propiedades-comando}`
- **Guards**: `{guards}` (si se recopilaron)
- **Business-rules**: `{business-rules}` (si se recopilaron)

**IMPORTANTE**: 
- Los comandos NO crean entidades
- Los comandos pueden usar tipos primitivos Y enums
- Cualquier objeto complejo del evento debe ser aplanado a tipos primitivos en el comando
- Guards y business-rules deben pasarse al comando interno si fueron recopilados

## Lógica de Sugerencias

### Para Subdominios
```
1. Listar directorios en {aplicacion}/{aplicacion}-catalog/domains/{dominio}/
2. Analizar nombres existentes y buscar similitudes léxicas con el evento
3. Priorizar por:
   - Coincidencias parciales de palabras
   - Contexto semántico (ej: "usuario" para eventos de autenticación)
   - Subdominios más recientemente utilizados
```

### Para Dominios  
```
1. Listar directorios en {aplicacion}/{aplicacion}-catalog/domains/
2. Analizar nombres y buscar relevancia contextual
3. Priorizar por:
   - Palabras clave relacionadas al evento
   - Dominios más frecuentemente utilizados
   - Estructura jerárquica (dominios padre/hijo)
```

## Control de Errores y Flujo

- **Parámetros faltantes**: Recopilar interactivamente UNO A LA VEZ
- **Aplicación única**: Auto-asignar con notificación
- **Múltiples aplicaciones**: Mostrar opciones numeradas
- **Evento ya existe**: Abortar con mensaje claro
- **Error en comando interno**: Abortar ejecución y reportar error específico
- **AggregateRoot faltante**: Crear automáticamente con solo propiedad ID
- **Propiedades que modifican AggregateRoot**: Agregar al AggregateRoot si no existen
- **Entidad faltante en evento**: Crear automáticamente con `aggregateRoot: false` (EXCLUYENDO el AggregateRoot principal)
- **Objeto complejo en comando**: NO permitido - debe ser aplanado a tipos primitivos
- **Sugerencia rechazada**: Permitir entrada manual del usuario
- **Schema.avro inconsistente**: Reportar y corregir post-creación del evento

## Formato de Parámetros

### Propiedades
Formato: `"Prop1:tipo,Prop2:tipo,Prop3:tipo"`
Ejemplos:
- `"Id:string,Nombre:string,Activo:boolean"`
- `"Usuario:Usuario,Fecha:datetime,Monto:decimal"`

### Tipos Soportados
- Primitivos: `string`, `int`, `decimal`, `boolean`, `datetime`, `guid`
- Complejos: cualquier nombre que referencie una entidad existente o por crear

## Validación Final

Al completar todos los comandos:
1. Verificar automáticamente que todos los archivos fueron creados correctamente
2. Validar estructura de directorios
3. Reportar resumen de elementos creados:
   - CommandHandler creado
   - AggregateRoot validado/creado y actualizado
   - Entidades creadas (si aplica)
   - Evento creado
   - Comando creado

## ⚠️ REGLAS DE IMPLEMENTACIÓN

1. **Interactividad inteligente**: 
   - Auto-detectar aplicación si hay una sola
   - Sugerir subdominios/dominios existentes más relevantes
   - Preguntar parámetros faltantes UNO A LA VEZ
   - Permitir aceptar sugerencias o ingresar valores personalizados

2. **Validación progresiva**: 
   - Validar cada parámetro al recibirlo
   - Normalizar nombres automáticamente
   - Confirmar antes de ejecutar comandos

3. **Ejecución atómica**: Si un paso falla, todo el proceso debe fallar

4. **Mensajes claros**: 
   - Notificar auto-asignaciones: `✅ Aplicación detectada automáticamente: {nombre}`
   - Mostrar sugerencias: `Sugiero el subdominio **{sugerencia}** para el evento {evento}`
   - Reportar errores específicos con ejemplos de corrección

5. **Trazabilidad**: Mostrar progreso de cada comando ejecutado

6. **Flexibilidad**: 
   - Aceptar parámetros completos desde línea de comandos
   - Completar parámetros faltantes interactivamente
   - Permitir override de sugerencias automáticas

7. **Ejecución sin interactividad**:
   - Una vez que comience la ejecución de comandos internos, NO hacer preguntas adicionales
   - Todas las decisiones deben tomarse automáticamente basándose en los parámetros recopilados
   - Si hay errores durante la ejecución, abortar con mensaje claro (no solicitar input del usuario)

8. **Pasar parámetros completos a comandos internos**:
   - Guards y business-rules deben incluirse en las llamadas a comandos internos
   - Validar que todos los parámetros recopilados se usen efectivamente
   - Logging claro de qué parámetros se pasan a cada comando interno

9. **Manejo robusto de argumentos**:
   - Implementar parseo de argumentos desde el inicio
   - No asumir parámetros faltantes sin verificar $ARGUMENTS primero
   - Proporcionar retroalimentación clara sobre qué parámetros fueron detectados vs recopilados