Refactoriza el código del evento para mejorar su calidad manteniendo las pruebas en verde

## Argumentos

### Obligatorios

**Evento**: $ARGUMENTS

### Ejemplo de uso

```claude
/tdd-blue BorradorIniciado
```

### 1. Validación inicial

- **VALIDAR** el nombre del evento debe cumplir el siguiente formato {Sustantivo}{VerboEnPasado}.
- **VERIFICAR** que el evento exista en el archivo {nombre-aplicacion}-llm.txt
- **VERIFICAR** que existan las pruebas unitarias del command handler correspondiente
- **VERIFICAR** que las pruebas estén pasando (estado verde 🟢)
- **VERIFICAR** que el command handler esté completamente implementado
- **DETENER** si hay errores de formato, argumentos faltantes, pruebas fallando o código no implementado

## ⚠️ REGLAS DE EJECUCIÓN

### Ejecución del spec `.claude\specs\eventsourcing\blue-tdd.md`

- **SEGUIR TODOS LOS PASOS** del spec sin saltar ninguno
- **MANTENER TODAS LAS PRUEBAS** en estado verde durante todo el proceso
- **NO CAMBIAR** el comportamiento funcional del código
- **REFACTORIZAR ÚNICAMENTE** para mejorar calidad, legibilidad y mantenibilidad