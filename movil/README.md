# App Android

Kotlin + Jetpack Compose. El celular sirve para **mirar**, no para definir: en cuarto limpio ISO 8,
con bata y guantes ESD, nadie escribe una secuencia de 40 pasos en un telefono.

## Alcance

| Sprint | Que entra |
|---|---|
| 1 | Ingreso, lista de corridas en curso, detalle con mediciones |
| 2 | Alertas, notificaciones push, abortar una corrida |
| 3 | Operacion contra el agente por LAN cuando no hay internet |

## Decisiones

- **Retrofit + OkHttp** contra el BFF movil.
- **WorkManager** para el refresco en segundo plano.
- **FCM** para push en modo nube; WebSocket con reconexion en modo laboratorio, porque FCM no existe
  sin internet.
- Tema claro y oscuro segun el sistema, contraste WCAG AA.
- Cadenas externalizadas desde el primer dia.

## Compilar

```bash
cd movil
./gradlew assembleDebug
./gradlew test
```
