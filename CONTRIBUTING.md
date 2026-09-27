# Guia de contribucion

Documento de referencia del flujo de trabajo del Grupo 13. Sostiene la historia HA-01 y los puntos
de *flujo de trabajo y estrategia de versionamiento* de la rubrica.

## Ramas

`main` esta protegida: nadie empuja directo. Todo cambio entra por pull request.

| Prefijo | Para que | Ejemplo |
|---|---|---|
| `feat/` | Funcionalidad nueva | `feat/PF2-123-reserva-exclusiva` |
| `fix/` | Correccion de defecto | `fix/PF2-140-lease-expirado` |
| `docs/` | Solo documentacion | `docs/PF2-101-guia-despliegue` |
| `chore/` | Infraestructura, dependencias, CI | `chore/PF2-118-actualizar-fastapi` |
| `refactor/` | Cambio interno sin cambio de comportamiento | `refactor/PF2-130-extraer-driver-base` |
| `test/` | Solo pruebas | `test/PF2-135-contrato-ejecucion` |

El codigo de Jira va siempre en el nombre de la rama. Ninguna rama vive mas de 3 dias; lo que no
cabe va detras de un *feature flag*.

## Commits

Conventional Commits, con el codigo de Jira en el asunto:

```
feat(catalogo): PF2-12 registrar equipo con limites de seguridad
fix(agente): PF2-40 aislar excepcion de driver sin tumbar el motor
docs(readme): PF2-03 agregar guia de ambiente local
```

Tipos permitidos: `feat`, `fix`, `docs`, `test`, `refactor`, `chore`, `perf`, `ci`.

El hook de `commit-msg` rechaza los mensajes que no cumplan el formato.

## Pull requests

1. Rama desde `main` actualizado.
2. Llenar la plantilla de pull request completa.
3. CI en verde: pruebas, cobertura y quality gate de SonarCloud.
4. **Una aprobacion** de otro integrante. El peer review es cruzado entre frentes.
5. Merge con **squash**, para que el historial de `main` sea una linea legible de features.

## Versionamiento

Versionamiento semantico (`MAJOR.MINOR.PATCH`). Se etiqueta al cierre de cada sprint:

| Tag | Cuando |
|---|---|
| `v0.1.0` | Cierre del Sprint 1 |
| `v0.2.0` | Cierre del Sprint 2 |
| `v1.0.0` | Sustentacion |

El tag dispara el pipeline de release, que genera la nota de version a partir de los commits.

## Definicion de hecho

Una historia se cierra solo si cumple todo esto:

- [ ] Codigo en `main` por pull request aprobado por otro integrante
- [ ] Pruebas unitarias del codigo nuevo
- [ ] Cobertura del codigo nuevo >= 70%
- [ ] Quality gate de SonarCloud en verde
- [ ] Desplegada y funcionando en *staging*
- [ ] Criterios de aceptacion verificados por alguien distinto de quien la implemento
- [ ] Issue de Jira en *Done*, con sus commits enlazados

## Antes de abrir el pull request

```bash
# Servicios y agente (Python)
ruff check .
pytest --cov

# Web
npm run lint && npm run test

# Movil
./gradlew ktlintCheck test
```
