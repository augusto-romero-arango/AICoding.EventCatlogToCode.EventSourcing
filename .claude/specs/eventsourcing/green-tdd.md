# TDD Green stage eventos

## High-Level Objective

- Implementar el código mínimo necesario para hacer que las pruebas unitarias del command handler pasen (estado verde 🟢) sin implementar funcionalidad adicional

## Mid-Level Objective

- Implementar la lógica del command handler para procesar el comando específico
- Aplicar las business rules documentadas en el CommandHandler
- Validar los guards documentados en el comando
- Emitir el evento correspondiente con las mutaciones de estado correctas
- Actualizar el aggregate root con los métodos de aplicación de eventos necesarios

## Implementation notes

- Usa la sintaxis de C# >=12 (https://learn.microsoft.com/es-es/dotnet/csharp/whats-new/csharp-12)
- Implementa ÚNICAMENTE el código necesario para hacer pasar las pruebas existentes
- No agregues funcionalidad extra que no esté cubierta por las pruebas
- Los métodos Apply en el aggregate root deben ser privados y seguir el patrón `Apply({Evento} evento)`
- El command handler debe cargar el aggregate, aplicar las business rules, emitir el evento y guardar el aggregate
- Usar el patrón de Event Sourcing con IEventStore para persistencia
- Todos los records **deben** usar el primary constructor para instanciar el objeto
- Las propiedades del aggregate root deben tener el set privado
- Los value objects deben ser records
- Las entidades intermedias deben ser records
- No usar comentarios en el código.

## Context

### Beginning context

- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio.Test/{CommandHandler}Test.cs` archivo de prueba con tests en estado fallido
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs` archivo con command handler no implementado (NotImplementedException)
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs` archivo con aggregate root sin métodos Apply
- `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/{nombre-aplicacion}-llm.txt` archivo de especificación de EventCatalog

### Ending context

- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs` archivo con command handler completamente implementado
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs` archivo con métodos Apply implementados
- Todas las pruebas unitarias pasando (estado verde 🟢)
- Solution compilando sin errores ni warnings

## Low-Level Tasks

> Ordered from start to finish

### 1. Analizar las pruebas existentes para entender los requisitos:

- Leer el archivo de pruebas `{CommandHandler}Test.cs` para identificar:

  - Qué eventos deben ser emitidos
  - Qué propiedades del aggregate root deben ser modificadas
  - Qué valores esperados tienen las propiedades
  - Qué business rules deben aplicarse
  - Qué guards deben validarse

### 2. Implementar los métodos Apply en el aggregate root:

- En el archivo `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs`:

- Agregar métodos Apply privados para cada evento que modifique el aggregate:

```c#
public void Apply({Evento} evento)
{
    // Asignar las propiedades del aggregate root basado en el evento
    [propiedad] = evento.[propiedad];
}
```

- Los métodos Apply deben seguir exactamente las mutaciones documentadas en la sección "State Mutations" del evento en el archivo LLM
- Agregar métodos Apply privados para cada evento que modifique el aggregate:

```c#
public void Apply({Evento} evento)
{
    // Asignar las propiedades del aggregate root basado en el evento
    [propiedad] = evento.[propiedad];
}
```

- Los métodos Apply deben seguir exactamente las mutaciones documentadas en la sección "State Mutations" del evento en el archivo LLM

### 3. Implementar la lógica del command handler:

- En el archivo `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs`:
- Reemplazar el `NotImplementedException` con la lógica completa:

```c#
public async Task HandleAsync({Comando} command, CancellationToken cancellationToken)
{
    // 1. Validar guards del comando (si existen)
    if ([condición de guard])
    {
        throw new [ExcepcionEspecifica]("Mensaje de error");
    }

    // 2. Cargar o crear el aggregate root si aplica
    var aggregate = await eventStore.GetAggregateRootAsync<{AggregateRoot}AggregateRoot>(command.[IdPropiedad], cancellationToken);

    // 3. Aplicar business rules (si existen)
    // Ejemplo: if ([condición business rule]) { ... }

    // 4. Crear y aplicar el evento
    var evento = new {Evento}(
        [mapear propiedades del comando al evento aplicando business rules si es necesario]
    );

    // 5. Aplicar el evento al aggregate
    eventStore.AppendEvent(command.[IdPropiedad], evento);
}
```

### 4. Implementar validaciones de guards

- Para cada guard documentado en el comando en el archivo LLM:
  - Lanzar excepciones específicas cuando los guards no se cumplan
  - Implementar la validación correspondiente en el command handler
  - Asegurarse de que las excepciones coincidan con lo esperado en las pruebas

### 5. Implementar business rules

- Para cada business rule documentado en el CommandHandler en el archivo LLM:
  - Implementar la lógica correspondiente en el command handler
  - Aplicar las transformaciones necesarias a los datos antes de crear el evento
  - Ejemplo: si el business rule dice "El borrador siempre se crea en estado activo", forzar el estado en el evento

### 6. Verificar que las pruebas pasen

- Ejecutar las pruebas unitarias: `dotnet test`
- Verificar que todas las pruebas pasen (estado verde 🟢)
- Asegurarse de que no hay errores de compilación
- Confirmar que el command handler procesa correctamente los comandos y emite los eventos esperados

### 7. Refactorizar si es necesario

- Si hay código duplicado, extraer métodos privados
- Asegurarse de que el código es legible y mantenible
- Verificar que todas las pruebas siguen pasando después del refactor

## Validation Checklist

- [ ] Todas las pruebas unitarias pasan
- [ ] El solution compila sin errores
- [ ] Los métodos Apply del aggregate root están implementados
- [ ] El command handler procesa el comando completamente
- [ ] Los guards están validados (si existen)
- [ ] Las business rules están aplicadas (si existen)
- [ ] Los eventos son emitidos correctamente
- [ ] Las propiedades del aggregate root se actualizan según las "State Mutations"
- [ ] No se ha implementado funcionalidad adicional no cubierta por las pruebas