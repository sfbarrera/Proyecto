# CITAE Lab — Automatización de control, monitoreo y registro de equipos de laboratorio

> Proyecto Final II · Maestría en Ingeniería de Software (MISO) · Universidad de los Andes · **Grupo 13**

Software para automatizar el control, el monitoreo y el registro de los equipos del laboratorio de
electrónica del **Centro de Investigación y Desarrollo en Tecnologías Aeroespaciales (CITAE)** de la
**Fuerza Aeroespacial Colombiana**.

---

## Tabla de contenido

- [El problema](#el-problema)
- [Qué construimos](#qué-construimos)
- [Contexto institucional](#contexto-institucional)
- [Contexto académico](#contexto-académico)
- [Equipo](#equipo)
- [Arquitectura](#arquitectura)
- [Atributos de calidad](#atributos-de-calidad)
- [Stack tecnológico](#stack-tecnológico)
- [Estructura del repositorio](#estructura-del-repositorio)
- [Cómo ejecutar el proyecto](#cómo-ejecutar-el-proyecto)
- [Flujo de trabajo](#flujo-de-trabajo)
- [Plan de sprints](#plan-de-sprints)
- [Documentación](#documentación)
- [Aviso sobre manejo de información](#aviso-sobre-manejo-de-información)

---

## El problema

El laboratorio de electrónica del CITAE cuenta con un cuarto limpio ISO 8 con cumplimiento
ANSI/ESD S20.20 y equipos de instrumentación especializados: fuentes de alimentación,
osciloscopios, generadores de señales, buses de comunicación y un analizador EMI/EMC. Con ellos se
ejecutan las pruebas de **AIV** (*Assembly, Integration and Verification*) sobre nanosatélites y
componentes espaciales.

Hoy esas pruebas se ejecutan operando cada instrumento a mano: el técnico configura la fuente,
dispara la medición en el osciloscopio, anota el resultado y repite. Eso produce:

- Riesgo de error humano sobre componentes espaciales costosos e irremplazables.
- Tiempos de ejecución prolongados.
- Trazabilidad que depende de que alguien transcriba bien.
- Ensayos que no se pueden reproducir de forma confiable.

No existe hoy un software que centralice el control de los equipos, el manejo de los datos y el
almacenamiento de los resultados.

## Qué construimos

Un sistema que permite **definir un procedimiento de prueba una sola vez, ejecutarlo de forma
automática sobre los equipos del laboratorio, y dejar un registro completo y verificable de todo lo
que ocurrió**.

El sistema se opera de dos formas: **localmente**, desde la red del laboratorio, y **remotamente**,
por internet, desde una web de escritorio o una app móvil.

Tres condiciones del dominio determinan todo el diseño:

1. **Los instrumentos son locales.** USB y GPIB no salen del laboratorio, así que existe un agente
   que vive ahí y habla con ellos. La nube nunca le envía un comando directo a un equipo.
2. **Hay ensayos de más de 72 horas.** El agente es un proceso de larga vida con estado durable:
   sobrevive a reinicios del computador y a cortes de enlace de horas sin perder mediciones ni dejar
   un equipo en estado indeterminado.
3. **El catálogo de equipos va a crecer.** Agregar un instrumento nuevo se resuelve agregando un
   driver, sin tocar el núcleo del sistema.

## Contexto institucional

Este proyecto corresponde al anteproyecto de trabajo de grado **GH-JEAES-FR-630** presentado ante la
Fuerza Aeroespacial Colombiana por el TE. Sergio Fernando Barrera Molano, avalado por el
**MY. Méndez Gómez Javier Enrique**, Jefe del Centro de Investigación y Desarrollo en Tecnologías
Aeroespaciales.

El producto esperado por la FAC es un software funcional que automatice el uso en conjunto de los
equipos, garantizando la ejecución y la trazabilidad de las pruebas programadas mediante la
generación automática de registros y reportes.

## Contexto académico

| | |
|---|---|
| Programa | Maestría en Ingeniería de Software (MISO) |
| Institución | Universidad de los Andes |
| Curso | Proyecto Final II |
| Grupo | 13 |
| Duración | 8 semanas · 3 sprints |
| Capacidad | 4 personas × 8 h/semana = 224 horas-equipo |

El diseño de arquitectura, los requisitos de calidad y la estrategia de pruebas provienen del ciclo
de Proyecto Final I. Este repositorio contiene la construcción del producto.

## Equipo

| Nombre | Rol | Código | Correo |
|---|---|---|---|
| Edwin Hernan Hurtado Cruz | Desarrollo | 202326341 | eh.hurtado@uniandes.edu.co |
| Harold Andres Bartolo Moscoso | Desarrollo | 202513889 | h.bartolo@uniandes.edu.co |
| Sergio Fernando Barrera Molano | Desarrollo | 202517034 | sf.barreram1@uniandes.edu.co |
| Juan Jose Restrepo Bonilla | Desarrollo | 202516633 | jj.restrepob1@uniandes.edu.co |

**Monitor del proyecto:** Erick Julián Coral Crespo

Los cuatro integrantes trabajan como *full stack*. La división es por frente de riesgo, no por
habilidad:

| Frente | Alcance |
|---|---|
| Agente y drivers | Motor de ejecución, plug-ins de instrumento, comunicación SCPI |
| Servicios de ejecución y resultados | Orquestación de corridas, telemetría, bitácora |
| Web | Cliente de escritorio en React |
| Android y notificaciones | App nativa en Kotlin, alertas |

El *peer review* se hace cruzado entre frentes, para que ningún integrante sea el único que entiende
su parte.

## Arquitectura

La decisión que ordena todo lo demás: **la nube nunca le habla a un equipo**. Solo el agente local
emite comandos, y lo hace contra su propia copia del procedimiento. La nube coordina, guarda y
muestra.

```
                 ┌──────────────────┐   ┌──────────────────┐
   CLIENTES      │ Web de escritorio│   │   App Android    │
                 │ React+TypeScript │   │ Kotlin + Compose │
                 └────────┬─────────┘   └────────┬─────────┘
                          │                      │
 ┌────────────────────────┼──────────────────────┼─────────────────────────┐
 │ NUBE · PLANO DE CONTROL▼                      ▼                         │
 │  ┌──────────────────────────────────────────────────────────────────┐   │
 │  │      API Gateway · un BFF para la web, otro para el móvil        │   │
 │  └──────┬───────────────────────┬───────────────────────┬───────────┘   │
 │         ▼                       ▼                       ▼               │
 │  ┌─────────────┐        ┌──────────────┐        ┌────────────────┐      │
 │  │  Identidad  │        │   Catálogo   │        │ Procedimientos │      │
 │  │ Usuarios,   │        │ Equipos,     │        │ Autoría,       │      │
 │  │ roles, JWT  │        │ drivers,     │        │ versión,       │      │
 │  │             │        │ límites      │        │ aprobación     │      │
 │  └─────────────┘        └──────┬───────┘        └────────────────┘      │
 │                                ▼                                        │
 │  ┌─────────────┐        ┌──────────────┐        ┌────────────────┐      │
 │  │ Resultados  │        │  EJECUCIÓN   │        │ Notificaciones │      │
 │  │ Telemetría, │        │ Orquesta     │        │ Alertas y      │      │
 │  │ artefactos, │        │ runs y       │        │ push a Android │      │
 │  │ reportes y  │        │ reservas,    │        │                │      │
 │  │ bitácora    │        │ checkpoints  │        │                │      │
 │  └──────┬──────┘        └──────┬───────┘        └───────┬────────┘      │
 │         ▼                      ▼                        ▼               │
 │  ┌──────────────────────────────────────────────────────────────────┐   │
 │  │  Bus de eventos · RabbitMQ, el mismo en la nube y el laboratorio │   │
 │  └──────────────────────────────────────────────────────────────────┘   │
 └────────────────────────────────┬────────────────────────────────────────┘
                                  ▲
                                  │ HTTPS / WSS saliente · store-and-forward
                                  ▼
 ┌─────────────────────────────────────────────────────────────────────────┐
 │ LABORATORIO DEL CITAE · DESPLIEGUE LOCAL                                │
 │  ┌──────────────────────────────────┐        ┌───────────────────────┐  │
 │  │ AGENTE LOCAL                     │        │ Equipos del           │  │
 │  │ Motor de ejecución paso a paso   │ SCPI   │ laboratorio           │  │
 │  │ Gestor de drivers (plug-in)      │ ─────► │ Fuentes               │  │
 │  │ Supervisor de límites y estado   │        │ Osciloscopios         │  │
 │  │   seguro                         │        │ Generadores           │  │
 │  │ Buffer durable SQLite y outbox   │        │ Buses, analizador EMI │  │
 │  │ Servidor local para operar LAN   │        │ USB · GPIB · LXI      │  │
 │  └──────────────────────────────────┘        └───────────────────────┘  │
 └─────────────────────────────────────────────────────────────────────────┘
```

**Estilos arquitectónicos**

- **Microkernel / plug-in** para los drivers de equipo. Es el estilo protagonista: el núcleo no sabe
  qué instrumentos existen, solo conoce una interfaz.
- **Microservicios** para el plano de control en la nube, con despliegue independiente por servicio.
- **Cliente-servidor con agente de borde**, porque el agente es autónomo y no un cliente tonto.
- **Orientado a eventos** para telemetría, alertas y sincronización.

## Atributos de calidad

Los tres atributos dominantes son facilidad de modificación, disponibilidad y seguridad.

| ASR | Atributo | Medida de respuesta |
|---|---|---|
| ASR-M1 | Modificabilidad | Un instrumento nuevo modifica **un solo módulo**. Cero cambios en el núcleo. ≤ 8 horas-persona |
| ASR-M2 | Modificabilidad | Mismo artefacto en nube y laboratorio. Cero cambios de código, ≤ 1 archivo de configuración por entorno |
| ASR-M3 | Modificabilidad | Un tipo de paso nuevo no toca el motor de ejecución ni el esquema de la base |
| ASR-D1 | Disponibilidad | Corte de enlace de 8 h en una corrida de 72 h: cero mediciones perdidas, resincronización ≤ 5 min |
| ASR-D2 | Disponibilidad | Reinicio del agente a mitad de corrida: reanuda o aplica estado seguro en ≤ 60 s |
| ASR-D3 | Disponibilidad | Si caen notificaciones o reportes, la ejecución y el monitoreo siguen operando |
| ASR-S1 | Seguridad | Ningún comando fuera de la envolvente llega al instrumento. 0 de N intentos |
| ASR-S2 | Seguridad | Alteración de la bitácora detectada en el 100% de los casos, señalando el registro exacto |
| ASR-S3 | Seguridad | Solo un rol autorizado lanza o aborta; solo se ejecutan procedimientos aprobados |

## Stack tecnológico

Dos criterios decidieron casi todo: **portabilidad** (el ASR-M2 prohíbe servicios propietarios en el
camino crítico) y **lo que el equipo ya domina**.

| Capa | Elección |
|---|---|
| Agente local | Python 3.12 · PyVISA · FastAPI · SQLite |
| Drivers | SCPI sobre PyVISA (USB-TMC, GPIB, Ethernet-LXI) + simulador propio |
| Servicios de nube | Python 3.12 · FastAPI · SQLAlchemy · Alembic |
| Base de datos | PostgreSQL 16 |
| Mensajería | RabbitMQ |
| Caché y leases | Redis |
| Artefactos | GCS en la nube, MinIO en el laboratorio (API S3) |
| Gateway | Traefik + dos BFF en FastAPI |
| Web | React 18 · TypeScript · Vite · TanStack Query · react-i18next |
| Móvil | Kotlin · Jetpack Compose · Retrofit · WorkManager |
| Contenedores | Docker · GKE Autopilot (nube) · Docker Compose (laboratorio) |
| Infraestructura | Terraform |
| CI/CD | GitHub Actions |
| Calidad y cobertura | SonarCloud |
| Tablero | Jira |

## Estructura del repositorio

```
citae-lab/
├── agente/                  # Agente local: motor de ejecución y buffer durable
│   └── drivers/             # Plug-ins de instrumento (simulador, fuente, osciloscopio…)
├── servicios/
│   ├── identidad/           # Usuarios, roles, JWT
│   ├── catalogo/            # Equipos, drivers, límites
│   ├── procedimientos/      # Autoría, versionado, aprobación
│   ├── ejecucion/           # Orquestación de corridas y reservas
│   ├── resultados/          # Telemetría, artefactos, reportes, bitácora
│   └── notificaciones/      # Alertas y push
├── bff/
│   ├── web/
│   └── movil/
├── web/                     # Cliente React
├── movil/                   # App Android en Kotlin
├── infra/                   # Terraform, Docker Compose, manifiestos de Kubernetes
├── docs/                    # Documentación del proyecto
└── .github/workflows/       # Pipelines de CI/CD
```

## Cómo ejecutar el proyecto

### Prerrequisitos

- Docker y Docker Compose
- Python 3.12 (solo si se va a trabajar fuera de contenedores)
- Node.js 20 (para el cliente web)
- Android Studio (para el cliente móvil)

### Ambiente local completo

```bash
git clone https://github.com/<organizacion>/citae-lab.git
cd citae-lab
cp .env.example .env
docker compose up
```

Eso levanta PostgreSQL, Redis, RabbitMQ, MinIO, los seis servicios, los dos BFF y el agente local
con su **driver simulado**. Las migraciones se aplican solas y queda creado un usuario de prueba por
cada rol.

| Servicio | URL local |
|---|---|
| Web | http://localhost:5173 |
| API (BFF web) | http://localhost:8080 |
| Agente local | http://localhost:8090 |
| RabbitMQ (consola) | http://localhost:15672 |
| MinIO (consola) | http://localhost:9001 |

No hace falta ningún instrumento físico: el simulador SCPI responde el mismo juego de comandos que
un equipo real, y es la pieza que permite que todo el equipo avance y que la integración continua
corra sin hardware.

### Cliente móvil

```bash
cd movil
./gradlew assembleDebug
```

## Flujo de trabajo

- **Trunk-based con ramas cortas.** `main` siempre desplegable y protegida: nadie empuja directo,
  pull request obligatorio, CI en verde y **una aprobación** de otro integrante.
- **Nombre de rama:** `feat/PF2-123-reserva-exclusiva`, `fix/PF2-140-lease-expirado`.
- **Conventional commits:** `feat:`, `fix:`, `docs:`, `test:`, `refactor:`, `chore:`, con el código
  de Jira en el mensaje. De ahí sale el CHANGELOG automático.
- **Merge con squash**, para que el historial de `main` sea una línea legible de features.
- **SemVer por release:** `v0.1.0` al cierre del Sprint 1, `v0.2.0` al del Sprint 2, `v1.0.0` en la
  sustentación.
- Ninguna rama vive más de 3 días. Lo que no cabe va detrás de un *feature flag*.

### Definición de hecho

Una historia se cierra solo si cumple todo esto:

- [ ] Código en `main` por pull request aprobado por otro integrante
- [ ] Pruebas unitarias del código nuevo
- [ ] Cobertura del código nuevo ≥ 70%
- [ ] Quality gate de SonarCloud en verde
- [ ] Desplegada y funcionando en *staging*
- [ ] Criterios de aceptación verificados por alguien distinto de quien la implementó
- [ ] Issue de Jira en *Done*, con sus commits enlazados

## Plan de sprints

| Sprint | Semanas | Objetivo demostrable | Release |
|---|---|---|---|
| Sprint 1 | 1–2 | **Esqueleto vivo extremo a extremo.** Crear un procedimiento en la web, ejecutarlo con el agente contra un equipo simulado, ver mediciones y bitácora en web y celular | `v0.1.0` |
| Sprint 2 | 3–4 | **Equipos reales y operación segura.** Drivers de instrumentos reales, panel manual, límites aplicados, reservas exclusivas, monitoreo en vivo, abortar desde Android | `v0.2.0` |
| Sprint 3 | 5–7 | **Larga duración y resiliencia.** Checkpointing, reanudación, estado seguro, pruebas de caos, despliegue on-premise validado, reportes de trazabilidad | `v1.0.0` |
| Cierre | 8 | Sustentación del producto | |

Cada sprint retira el riesgo más grande que quede. El Sprint 1 no construye funcionalidad ancha:
construye un esqueleto que atraviesa todas las costuras de la arquitectura, para que cualquier error
de diseño aparezca en la semana 2 y no en la 7.

## Documentación

| Documento | Ubicación |
|---|---|
| Historias de usuario del Sprint 1 | [`docs/HISTORIAS_USUARIO_SPRINT_1.md`](docs/HISTORIAS_USUARIO_SPRINT_1.md) |
| Plan de desarrollo | `docs/PLAN_DE_DESARROLLO.md` |
| Anteproyecto GH-JEAES-FR-630 | `docs/anteproyecto/` |
| Documento de arquitectura | `docs/arquitectura/` |
| Estrategia de pruebas | `docs/pruebas/` |

**Tablero del proyecto (Jira):** _pendiente de enlazar_
**Calidad y cobertura (SonarCloud):** _pendiente de enlazar_

## Aviso sobre manejo de información

Este proyecto se desarrolla con **datos de prueba**. No se cargan a infraestructura en la nube datos
reales de ensayos, componentes o misiones de la Fuerza Aeroespacial Colombiana sin autorización
expresa del CITAE. La capacidad de desplegar el sistema completo dentro del laboratorio, sin
dependencia de servicios externos, existe precisamente por esta razón.

---

**Proyecto Final II** · Maestría en Ingeniería de Software · Universidad de los Andes · Grupo 13
