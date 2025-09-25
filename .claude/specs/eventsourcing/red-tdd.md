# TDD Red stage eventos

## High-Level Objective

- Crear las pruebas unitarias de un command handler y el comando de un evento específico recibidos en el prompt

## Mid-Level Objective

- Escribir una prueba unitaria por cada business rule
- Escribir una prueba unitaria por cada guard
- Escribir una prueba unitaria por cada evento emitido y cambios en propiedades en el AggregateRoot

## Implementation notes

- Usa la sintaxis de C# >=12 (https://learn.microsoft.com/es-es/dotnet/csharp/whats-new/csharp-12)
- Las pruebas están escritas en xUnit 3. Revisar la documentación oficial: (https://xunit.net/?tabs=cs)
- Las pruebas deben compilar, pero no debe haber código de implementación, solo debe haber código en los tests.
- Los nombres de los métodos debe cumplir con el patrón Si_{Condicion}_Debe_{Evento|Error} o Si_{Condicion}_NoDebe_{Evento|Error}
- Todos los records **deben** usar el primary constructor para instanciar el objeto.
- Las propiedades del aggregate root deben tener el set privado.
- Los value objects deben ser records.
- Las entidades intermedias debe ser records.
- Usar el `_aggregateId` para el id del aggregate que dispone la clase base `CommandHandlerAsyncTest`.
- NO usar comentarios en el código.
- OBLIGATORIO: Sin importar la especificación de los objetos usados en los eventos o comandos, cuando se haga referencia al objeto `Dinero` se debe usar la definición del value object `Dinero` que se encuentra en el namespace `Cosmos.Types`. Por ninguna razón se debe crear un value object `Dinero` nuevo.

## Context

### Beginning context

- `./{nombre-aplicacion}/{nombre-aplicacion}-catalog/{nombre-aplicacion}-llm.txt` archivo de especificación de EventCatalog
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/` directorio del proyecto del dominio
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio.Test/` directorio del proyecto de pruebas del dominio

### Ending context

- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio.Test/{CommandHandler}Test.cs` archivo de prueba por command handler
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs` archivo que contendrá:
  - Clase del command handler
  - Record del comando
  - Record del evento
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/` directorio donde se almacenarán las clases y records del dominio tales como:
  - Aggreagate root
  - Entidades
  - ValueObjects
  - Enums
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs` archivo de la clase del aggregate root

## Low-Level Tasks

> Ordered from start to finish

### 1. Leer desde el prompt el evento que será evaluado para descubrir las pruebas

- Del evento proporcionado en el prompt encontrar en el archivo {nombre-aplicacion}-llm.txt las siguientes secciones:
  - Evento
  - Comando
  - CommandHandler
- Para el CommandHandler encontrar los business rules
- Para el comando encontrar los guards
- En la seccion State Mutations del evento encontrar que propiedades del aggregate root que son modificadas

### 2. Crear el archivo de pruebas

- En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio.Test/` crear el archivo `{CommandHandler}Test.cs`.
- Usar la siguiente plantilla para la creación de la clase de pruebas:

```c#
using AwesomeAssertions;
using Cosmos.EventSourcing.Abstractions.Commands;
using Cosmos.EventSourcing.Testing.Utilities;

namespace {nombre-solucion}.Dominio.Tests;

public class {CommandHandler}Tests : CommandHandlerAsyncTest<{Comando}>
{
    protected override ICommandHandlerAsync<{Comando}> Handler =>
        new {CommandHandler}(eventStore);

    [Fact]
    public async Task {nombre-del-test}()
    {
        ....
    }
```

- La primera prueba debe ser verificar que el evento sea emitido y que el aggregate root sea afectado. Con las siguientes instrucciones:
- Usar Given, When, Then y And por cada propiedad que el evento modifique del paquete:

```c#
    Given([eventos previos que debieron haber sido emitidos generalmente de los business rules]);

    await WhenAsync([comando]);

    Then([eventos esperados]);
    [...And<[AggregateRoot], [tipo de dato de la propiedad a evaluar]>(aggregate => aggregate.[propiedad], [valor esperado]);]
```

- Las siguientes pruebas que se escriban, deben ser los guards y los business rules.
- Las pruebas que son validaciones de existencias (por ejemplo validar si existe el aggregate id) deben arrojar un error.
- Las pruebas que son de inconsistencias en el aggregate root debe ser un evento de error.
- Los assert de Exceptions deben acertar que la excepción sea lanzada y que el mensaje sea el esperado.

### 3. Crear el archivo del command handler

- En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs`
- Usar la siguiente plantilla para la creación de la clase del command handler:

```c#
using Cosmos.EventSourcing.Abstractions.Commands;

namespace {nombre-solucion}.Dominio.Servicios;

public class {CommandHandler}(IEventStore eventStore): ICommandHandlerAsync<{Comando}>
{
    public async Task HandleAsync({Comando} command, CancellationToken cancellationToken)
    {
        throw new NotImplementedException("Método no implementado");
    }
}
```

### 4. Agregar el record del comando al archivo del command handler

- En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs`
- Usar la siguiente plantilla para la creación de la clase del command handler:

```c#
using Cosmos.EventSourcing.Abstractions.Commands;

namespace {nombre-solucion}.Dominio.Servicios;

public record {Comando}([propiedades del comando]);

/// Codigo actual de command handler
```

- Las propiedades del comando están documentadas en la sección `## Raw Schema:schema.avro` del comando.

### 5. Agregar el record del evento al archivo del command handler

- En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs`
- Usar la siguiente plantilla para la creación de la clase del command handler:

```c#
using Cosmos.EventSourcing.Abstractions.Commands;

namespace {nombre-solucion}.Dominio.Servicios;

public record {Evento}([propiedades del evento]);

/// Codigo actual del comando
/// Codigo actual de command handler
```

- Las propiedades del evento están documentadas en la sección `## Raw Schema:schema.avro` del evento.

### 6. Si no existe el aggregate root crearlo

- En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs`
- Usar la siguiente plantilla para la creación de la clase del aggregate root:

```c#
using Cosmos.EventSourcing.Abstractions;

namespace {nombre-solucion}.Entidades;

public class {AggregateRoot}AggregateRoot : AggregateRoot
{
    [propiedades del aggregate provenientes de la sección State Mutations]
}
```

- Las propiedades del aggregate root que se deben implementar son **UNICAMENTE** las propiedades documentadas en la sección State Mutation del evento.

### 7. Identificar los value objects o entidades

- Si alguna de las propiedades del aggregate root son value objects o enums crearlos en el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/[ValueObject|Enum].cs`

### 3. Garantizar que la solución compile **SIN** implementar el código para que las pruebas pasen (Estado en rojo 🔴)

## Validation Checklist

- [ ] Todas las pruebas unitarias no pasan
- [ ] El solution compila sin errores
- [ ] Todos los business rules están cubiertos
- [ ] Todos los guards están cubiertos
- [ ] El código sigue las mejores prácticas y sintaxis moderna de C#