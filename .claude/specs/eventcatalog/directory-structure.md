# EventCatalog Directory Structure Reference

This file defines the centralized directory structure patterns used across all EventCatalog specifications.

## Base Paths

### Application Root
```
./{nombre-aplicacion}/
```

### EventCatalog Root  
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/
```

### Domains Root
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/
```

## Domain Structure

### Domain Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/
```

### Subdomains Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/
```

### Subdomain Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/
```

## Service Structure

### Services Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/
```

### Service Directory (CommandHandler)
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/
```

## Message Structure

### Events Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/
```

### Event Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/{nombre-evento}/
```

### Commands Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/
```

### Command Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/{nombre-comando}/
```

## Entity Structure

### Entities Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/entities/
```

### Entity Directory
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/entities/{nombre-entidad}/
```

## Path Variables Reference

| Variable | Description | Example |
|----------|-------------|---------|
| `{nombre-aplicacion}` | Normalized application name | `mi_aplicacion` |
| `{nombre-aplicacion}-catalog` | EventCatalog directory name | `mi_aplicacion-catalog` |
| `{nombre-dominio}` | Normalized domain name | `ventas` |
| `{nombre-subdominio}` | Normalized subdomain name | `pedidos` |
| `{nombre-commandHandler}` | Normalized CommandHandler name | `CrearPedidoCommandHandler` |
| `{nombre-evento}` | Normalized event name (past participle) | `PedidoCreado` |
| `{nombre-comando}` | Normalized command name | `CrearPedido` |
| `{nombre-entidad}` | Normalized entity name | `Pedido` |

## Common Path Patterns

### Application Validation Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/
```

### Domain Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/
```

### Subdomain Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/
```

### Service Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/
```

### Event Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/events/
```

### Command Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/subdomains/{nombre-subdominio}/services/{nombre-commandHandler}/commands/
```

### Entity Listing Path
```
./{nombre-aplicacion}/{nombre-aplicacion}-catalog/domains/{nombre-dominio}/entities/
```

## Normalization Rules

### Application Names
- Normalize from user input to directory name
- Convert to lowercase, replace spaces with underscores, remove special characters

### Domain Names
- Convert to lowercase
- Replace spaces with underscores  
- Remove special characters
- Replace accented characters
- Replace "ñ" with "n"
- Trim whitespace

### Subdomain Names
- Same rules as domain names

### CommandHandler Names
- PascalCase
- Remove special characters
- Ensure ends with "CommandHandler" suffix

### Event Names
- PascalCase
- Remove special characters
- Must be in past participle form
- Never use "Event" suffix

### Command Names
- PascalCase
- Remove special characters
- Action-oriented naming

### Entity Names
- PascalCase
- Remove special characters
- Noun-oriented naming (represents domain objects)