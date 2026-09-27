# Infraestructura

El mismo sistema se despliega en la nube o dentro del laboratorio, a partir del mismo artefacto y sin
cambios de codigo. Solo cambia la configuracion. Eso es el ASR-M2, y es lo que obliga a usar piezas
portables: PostgreSQL, Redis, RabbitMQ y almacenamiento compatible con S3, nada propietario en el
camino critico.

| Carpeta | Para que |
|---|---|
| `compose/` | Ambiente local y despliegue on-premise en el laboratorio |
| `terraform/` | Infraestructura en GCP: GKE, Cloud SQL, Memorystore, Artifact Registry, redes |
| `k8s/` | Manifiestos de Kubernetes para staging y produccion |

## Ambientes

| Ambiente | Donde | Se despliega |
|---|---|---|
| Local | Docker Compose en la maquina de cada quien | A mano, con `docker compose up` |
| Staging | GKE Autopilot | Automatico en cada merge a `main` |
| Laboratorio | Docker Compose en un servidor del CITAE | A mano, validado al menos una vez por sprint |

El ambiente de laboratorio se valida cada sprint precisamente para que el ASR-M2 no se quede en el
papel.
