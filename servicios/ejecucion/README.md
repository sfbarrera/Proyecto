# Servicio de ejecucion

El corazon del sistema. Crea la corrida, la asigna a un agente, mantiene su estado, gestiona las reservas exclusivas de equipo con lease renovable y coordina los checkpoints.

Una corrida puede durar mas de 72 horas, asi que su estado autoritativo vive en el agente y este servicio lo refleja, no al reves.

**Sostiene** ASR-D1, ASR-D2.

**Historias** HU-04.
