# Backends for Frontends

Dos BFF, uno por cliente, detras del mismo gateway. Cada uno compone las respuestas que su cliente
necesita, para que la web y el movil no tengan que orquestar llamadas a seis servicios.

| BFF | Sirve a | Caracteristica |
|---|---|---|
| `web/` | Cliente React de escritorio | Respuestas completas, pensadas para pantallas grandes |
| `movil/` | App Android | Cargas livianas: solo lo que cabe en una tarjeta de lista |

El BFF movil existe porque un operador en cuarto limpio, con senal irregular, no deberia descargar la
ficha completa de una corrida para ver en que paso va.
