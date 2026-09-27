# Arquitectura

| Vista | Que responde |
|---|---|
| Funcional | Que componentes hay, que hace cada uno y quien llama a quien |
| De despliegue | Donde corre cada componente, en la nube y en el laboratorio |
| De informacion | Que datos existen, como fluyen y donde se replican |

Aqui tambien viven las decisiones de arquitectura (ADR): que se decidio, que alternativas se
descartaron y contra que ASR se justifica.

## Decisiones ya tomadas

| Decision | Por que |
|---|---|
| El agente local es el unico que emite comandos a los equipos | USB y GPIB son buses locales; ademas aisla el riesgo fisico |
| Drivers como plug-in (estilo microkernel) | ASR-M1: un instrumento nuevo = un modulo nuevo |
| Solo infraestructura portable, nada propietario en el camino critico | ASR-M2: el mismo artefacto corre en GKE y en el laboratorio |
| Estado autoritativo de la corrida en el agente, no en la nube | Corridas de mas de 72 h que deben sobrevivir cortes de enlace |
| Bitacora append-only con cadena de hash | ASR-S2: alteracion detectable al 100% |
