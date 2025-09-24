Crea las pruebas unitarias del evento dado en el prompt

## Argumentos

### Obligatorios:
**Evento**: $ARGUMENTS

### Ejemplo de uso:
```
/tdd-red ProveedorAsignado
```

### 1. Validación inicial
- **VALIDAR** el nombre del evento debe cumplir el siguiente formato {Sustantivo}{VerboEnPasado}.
- **VERIFICAR** que el evento exista en el archivo {nombre-aplicacion}-llm.txt
- **DETENER** si hay errores de formato o argumentos faltantes

## ⚠️ REGLAS DE EJECUCIÓN

### Ejecución del spec `.claude\specs\eventsourcing\red-tdd.md`:
- **SEGUIR TODOS LOS PASOS** del spec sin saltar ninguno