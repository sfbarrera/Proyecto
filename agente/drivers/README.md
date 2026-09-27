# Drivers de instrumento

Cada driver es un **plug-in**. El nucleo del agente no sabe que instrumentos existen: solo conoce la
interfaz `DriverEquipo`. Aqui vive la facilidad de modificacion del sistema (ASR-M1).

## El contrato

Todo driver implementa estos cinco metodos:

| Metodo | Que hace |
|---|---|
| `identificar()` | Devuelve marca, modelo y version de firmware (`*IDN?` en SCPI) |
| `configurar(magnitud, valor, unidad)` | Fija un parametro del instrumento |
| `medir(magnitud)` | Toma una lectura y la devuelve con su unidad |
| `capturar()` | Pide al instrumento que genere un artefacto (pantallazo, senal grabada) |
| `estado_seguro()` | Lleva el instrumento a un estado inofensivo (salidas apagadas) |

## Agregar un instrumento nuevo

1. Crear un modulo en esta carpeta que implemente `DriverEquipo`.
2. Declararlo en el punto de entrada de plug-ins.
3. Registrar el equipo en el catalogo con ese driver.

**No se toca nada fuera de esta carpeta.** Ese es literalmente el criterio de aceptacion de HA-05 y
la medida del ASR-M1: en el `git diff` del pull request, los archivos modificados fuera de
`agente/drivers/` deben ser cero.

## Drivers previstos

| Driver | Estado | Sprint |
|---|---|---|
| `simulado/` | Responde el mismo juego de comandos SCPI que un equipo real. Determinista, apto para CI | 1 |
| Fuente de alimentacion | Pendiente de modelo | 2 |
| Osciloscopio | Pendiente de modelo | 2 |
| Generador de senales | Pendiente de modelo | 3 |

El simulador no es un parche: es ciudadano de primera clase. Permite que tres de los cuatro
integrantes avancen sin acceso fisico al laboratorio, y que la integracion continua corra sin
hardware.
