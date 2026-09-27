# Pipelines de CI/CD

Un workflow por componente, filtrado por ruta, para que un cambio en la web no dispare las pruebas
del agente.

| Workflow | Se dispara con cambios en | Hace |
|---|---|---|
| `ci-agente.yml` | `agente/**` | Lint, pruebas, cobertura a SonarCloud, build de imagen |
| `ci-servicios.yml` | `servicios/**`, `bff/**` | Matriz por servicio: lint, pruebas, cobertura, imagen |
| `ci-web.yml` | `web/**` | Lint, pruebas, cobertura, build |
| `ci-movil.yml` | `movil/**` | ktlint, pruebas unitarias, APK de depuracion |
| `ci-infra.yml` | `infra/**` | `terraform fmt`, `validate` y `plan` en el pull request |
| `cd-staging.yml` | merge a `main` | Publica imagenes y despliega a staging |
| `release.yml` | tag `v*` | Nota de version y despliegue a produccion |

Implementa la historia **HA-02** (integracion continua) y **HA-04** (despliegue continuo).
