# TDD Red stage eventos

## High-Level Objective

- Crear las pruebas unitarias de un command handler y el comando de un evento específico recibidos en el prompt

## Mid-Level Objective

- Escribir una prueba unitaria por cada bussiness rule
- Escribir una prueba unitaria por cada guard
- Escribir una prueba unitaria por cada evento emitido y cambios en propiedades en el AggregateRoot

## Implementation notes

- Usa la sintaxis de C# >=12 (https://learn.microsoft.com/es-es/dotnet/csharp/whats-new/csharp-12)
- Las pruebas están escritas en xUnit 3
- Las pruebas deben compilar, pero no debe haber código de implementación, solo debe haber código en los tests.
- Los nombres de los métodos debe cumplir con el patrón Si_{Condicion}_Debe_{Evento|Error} o Si_{Condicion}_NoDebe_{Evento|Error}

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

### 1. Leer desde el prompt el evento que será evaluado para descubrir las pruebas:

- Del evento proporcionado en el prompt encontrar en el archivo {nombre-aplicacion}-llm.txt las siguientes secciones:
    - Evento
    - Comando
    - CommandHandler
- Para el CommandHandler encontrar los bussines rules
- Para el comando encontrar los guards
- En la seccion State Mutations del evento encontrar que propiedades del aggregate root que son modificadas

### 2. Crear el archivo de pruebas:
    - En el directorio `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio.Test/` crear el archivo `{CommandHandler}Test.cs`.
    - Usar la siguiente plantilla para la creación de la clase de pruebas:
    ``` c#
    public class {CommandHandler}Tests : CommandHandlerAsyncTest<{Comando}>
    {
        protected override ICommandHandlerAsync<{Comando}> Handler =>
            new {CommandHandler}(eventStore);
    }
    ```
- La primera prueba debe ser verificar que el evento sea emitido y que el aggregate root sea afectado

### 3. Escribir las pruebas unitarias una por una

### 4. Garantizar que la solución compile **SIN** implementar el código para que las pruebas pasen.