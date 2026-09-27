# Servicios del plano de control

Seis servicios en la nube, con despliegue independiente. Ninguno depende de un servicio propietario
de Google en el camino critico: eso es lo que permite desplegar el sistema completo dentro del
laboratorio a partir del mismo artefacto (ASR-M2).

| Servicio | Responsabilidad |
|---|---|
| `identidad/` | Usuarios, roles, JWT con refresh, bitacora de acceso |
| `catalogo/` | Equipos, drivers registrados, capacidades, limites de seguridad, agentes |
| `procedimientos/` | Autoria, versionado y aprobacion de procedimientos de prueba |
| `ejecucion/` | Orquesta corridas, asigna al agente, reservas con lease, checkpoints |
| `resultados/` | Ingesta de telemetria y artefactos, reportes, bitacora append-only |
| `notificaciones/` | Alertas y push a Android |

## Estructura de cada servicio

```
<servicio>/
|-- app/
|   |-- api/           # Rutas FastAPI
|   |-- dominio/       # Entidades y reglas de negocio
|   |-- infraestructura/  # Repositorios, mensajeria, clientes
|   `-- main.py
|-- migraciones/       # Alembic
|-- tests/
|-- Dockerfile
|-- pyproject.toml
`-- README.md
```

Todos parten de la misma plantilla, para no repetir el andamiaje seis veces.

## Convenciones

- Python 3.12, FastAPI, SQLAlchemy, Alembic.
- Cada servicio tiene su propio esquema en PostgreSQL. Ningun servicio lee tablas de otro.
- Comunicacion sincrona por HTTP entre servicios solo cuando hace falta; lo demas, por eventos en
  RabbitMQ.
- Endpoint `/salud` obligatorio en todos.
