Implementa el código mínimo para hacer pasar las pruebas del evento dado

## Argumentos

### Obligatorios:
**Evento**: $ARGUMENTS

### Ejemplo de uso:
```
/tdd-green BorradorIniciado
```

### 1. Validación inicial
- **VALIDAR** el nombre del evento debe cumplir el siguiente formato {Sustantivo}{VerboEnPasado}.
- **VERIFICAR** que el evento exista en el archivo {nombre-aplicacion}-llm.txt
- **VERIFICAR** que existan las pruebas unitarias del command handler correspondiente
- **VERIFICAR** que las pruebas estén fallando (estado rojo 🔴)
- **DETENER** si hay errores de formato, argumentos faltantes o pruebas no existen

## ⚠️ REGLAS DE EJECUCIÓN

### Ejecución del spec `.claude\specs\eventsourcing\green-tdd.md`:
- **SEGUIR TODOS LOS PASOS** del spec sin saltar ninguno
- **IMPLEMENTAR ÚNICAMENTE** el código necesario para hacer pasar las pruebas
- **NO AGREGAR** funcionalidad extra no cubierta por las pruebas existentes