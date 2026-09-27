# Agente local

Proceso de larga vida que corre **dentro del laboratorio del CITAE** y es el unico componente que
emite comandos a los instrumentos. La nube nunca le habla directo a un equipo.

## Responsabilidades

| Pieza | Que hace |
|---|---|
| Motor de ejecucion | Recorre el procedimiento paso a paso y confirma cada paso antes de avanzar |
| Gestor de drivers | Descubre y carga los plug-ins de `drivers/` sin conocerlos en compilacion |
| Supervisor de limites | Valida la envolvente antes de emitir cualquier comando, y aplica el estado seguro |
| Buffer durable | SQLite local con outbox: la corrida sigue aunque se caiga el enlace |
| Servidor local | API HTTP para operar desde la red del laboratorio sin pasar por internet |

## Por que vive aqui y no en la nube

USB y GPIB son buses locales: no salen del laboratorio. Y hay ensayos de mas de 72 horas, asi que el
agente tiene que sobrevivir a reinicios del computador y a cortes de enlace de horas sin perder
mediciones ni dejar un equipo en estado indeterminado.

## Ejecutar

```bash
# Desde la raiz del repositorio, como parte del ambiente completo
docker compose up agente

# O directo, contra el simulador
cd agente
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
AGENTE_MODO=simulado python -m agente
```

## Sostiene

ASR-M1 (equipo nuevo = un solo modulo), ASR-D1 (corte de enlace), ASR-D2 (reinicio del agente),
ASR-S1 (ningun comando fuera de envolvente).
