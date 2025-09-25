# Formato de Schema Avro para EventCatalog

## Formato Requerido
Los archivos `schema.avro` deben contener **JSON Schema de Apache Avro**, NO formato binario.

## Estructura Obligatoria

### Para Comandos:
```json
{
  "type": "record",
  "name": "{NombreComando}",
  "namespace": "{aplicacion}.{dominio}.{subdominio}.commands",
  "fields": [
    {
      "name": "nombreCampo",
      "type": "string|int|boolean|long|double|float",
      "doc": "Descripción del campo"
    }
  ]
}
```

### Para Eventos:
```json
{
  "type": "record",
  "name": "{NombreEvento}",
  "namespace": "{aplicacion}.{dominio}.{subdominio}",
  "fields": [
    {
      "name": "nombreCampo",
      "type": "string|int|boolean|long|double|float",
      "doc": "Descripción del campo"
    }
  ]
}
```

## Mapeo de Tipos .NET a Avro
- `string` → `"string"`
- `int` → `"int"`
- `decimal` → `"double"`
- `boolean` → `"boolean"`
- `datetime` → `"string"` (ISO 8601)
- `guid` → `"string"` (UUID format)

## Reglas de Nomenclatura

### Namespace
- **Comandos**: `{aplicacion}.{dominio}.{subdominio}.commands`
- **Eventos**: `{aplicacion}.{dominio}.{subdominio}`

### Name
- Debe coincidir exactamente con el nombre del comando o evento
- Usar PascalCase (ej: `CrearPedido`, `PedidoCreado`)

### Fields
- Nombre del campo en camelCase
- Tipo debe ser un tipo primitivo Avro válido
- `doc` es obligatorio y debe describir claramente el propósito del campo

## Validación
Todos los schemas generados deben:
1. Ser JSON válido
2. Cumplir con la especificación Apache Avro JSON Schema 1.12.0
3. Incluir la propiedad `doc` en todos los campos
4. Usar solo tipos primitivos Avro
5. Seguir las convenciones de nomenclatura definidas