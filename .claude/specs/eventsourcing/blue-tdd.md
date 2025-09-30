# TDD Blue stage eventos

## High-Level Objective

- Refactorizar el código implementado en la fase verde (🟢) para mejorar su calidad, legibilidad y mantenibilidad sin cambiar el comportamiento funcional

## Mid-Level Objective

- Eliminar duplicación de código en el command handler y métodos Apply
- Optimizar la estructura del código manteniendo todas las pruebas en verde

## Implementation notes

- Usa la sintaxis de C# >=12 (https://learn.microsoft.com/es-es/dotnet/csharp/whats-new/csharp-12)
- Todas las pruebas deben seguir pasando durante y después del refactor
- No cambiar el comportamiento funcional del código
- Los métodos Apply en el aggregate root deben seguir siendo privados
- Mantener el patrón de Event Sourcing con IEventStore
- Todos los records **deben** usar el primary constructor
- Las propiedades del aggregate root deben tener el set privado
- Los value objects deben ser records
- Las entidades intermedias deben ser records
- No usar comentarios en el código

## Context

### Beginning context

- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs` archivo con command handler implementado funcionando
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs` archivo con métodos Apply implementados
- Todas las pruebas unitarias pasando (estado verde 🟢)
- Solution compilando sin errores ni warnings

### Ending context

- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Servicios/{CommandHandler}.cs` archivo refactorizado con mejor estructura
- `./{nombre-aplicacion}/src/{nombre-solucion}/{nombre-solucion}.Dominio/Entidades/{AggregateRoot}AggregateRoot.cs` archivo refactorizado con mejor organización
- Código más legible y mantenible
- Eliminación de duplicación
- Todas las pruebas unitarias siguen pasando (estado verde 🟢)
- Solution compilando sin errores ni warnings
- OBLIGATORIO: NO se deben cambiar las pruebas unitarias dentro del refactor.

## Low-Level Tasks

> Ordered from start to finish

### 1. Analizar el código actual para identificar oportunidades de refactor:

- Revisar el command handler para identificar:
  - Código duplicado entre diferentes validaciones
  - Lógica compleja que puede extraerse a métodos privados
  - Validaciones similares que pueden unificarse
  - Métodos largos que pueden dividirse
- Revisar el aggregate root para identificar:
  - Patrones repetitivos en métodos Apply
  - Lógica que puede extraerse a métodos de utilidad
  - Propiedades calculadas que pueden optimizarse

### 2. Refactorizar business rules:

- Si hay lógica compleja de transformación de datos:
  - Extraer métodos privados con nombres descriptivos:

  ```c#
  private static {TipoDato} CalcularValor({Comando} command)
  {
      // Lógica de transformación
      return valor;
  }
  ```

- Simplificar la creación de eventos usando métodos de fábrica si es necesario

### 5. Refactorizar el aggregate root

- Si hay lógica repetitiva en métodos Apply:
  - Extraer métodos de utilidad privados.
  - Consolidar asignaciones similares.
- Optimizar el orden de las propiedades para mejor legibilidad.
- Extraer validaciones o cálculos complejos a métodos privados.

### 6. Aplicar principios de clean code

- Verificar que los nombres de variables y métodos sean expresivos.
- Simplificar expresiones complejas.
- Reducir el nivel de anidación cuando sea posible.
- Aplicar el principio de responsabilidad única en métodos privados.
- Salidas rapidas en los métodos.

### 7. Optimizar imports y organización

- Revisar y optimizar las declaraciones using.
- Organizar el código siguiendo convenciones de C#.
- Asegurar consistencia en el estilo de código.

### 8. Ejecutar pruebas después de cada refactor

- Ejecutar `dotnet test` después de cada cambio significativo.
- Verificar que todas las pruebas sigan pasando.
- Asegurar que no hay regresiones funcionales.

### 9. Validación final

- Ejecutar todas las pruebas: `dotnet test`
- Verificar compilación: `dotnet build`
- Confirmar que el código es más legible y mantenible
- Asegurar que no se ha cambiado el comportamiento funcional

## Validation Checklist

- [ ] Todas las pruebas unitarias siguen pasando
- [ ] El solution compila sin errores ni warnings
- [ ] Se ha eliminado código duplicado
- [ ] Los métodos complejos se han dividido en métodos más pequeños y específicos
- [ ] Los nombres de métodos y variables son expresivos
- [ ] Se han aplicado principios de clean code
- [ ] El código es más legible y mantenible
- [ ] No se ha cambiado el comportamiento funcional
- [ ] Se han extraído métodos privados apropiados
- [ ] La estructura del código sigue los patrones establecidos

## Refactoring Patterns

### Guards Validation Pattern, dentro del record del comando:
```c#
public record [Comando]([propiedades del comando]) {
  public void Validar()
  {
      if (condition1) throw new ArgumentException("Message1");
      if (condition2) throw new ArgumentException("Message2");
  }
}

```

### Business Rules Extraction Pattern:
```c#
private {TipoRetorno} AplicarReglaNegocio({Comando} command)
{
    // Lógica de negocio específica
    return resultado;
}
```

### Event Creation Pattern:
```c#
private {Evento} CrearEvento({Comando} command, [...ParametrosAdicionales])
{
    return new {Evento}(
        // Mapeo optimizado de propiedades
    );
}
```