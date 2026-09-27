# Servicio de resultados

Ingesta de mediciones escalares y de los artefactos que genera el propio instrumento (pantallazos, senales grabadas), mas la bitacora append-only con cadena de hash y la generacion de reportes de trazabilidad.

La bitacora se protege en tres capas: permisos de rol, triggers que rechazan UPDATE y DELETE, y encadenamiento por hash verificable.

**Sostiene** ASR-S2.

**Historias** HU-04.
