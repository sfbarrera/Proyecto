# Estrategia de pruebas

| Nivel | Que cubre | Herramienta |
|---|---|---|
| Unitarias | Logica de dominio en servicios, agente, web y movil | pytest, Vitest, JUnit |
| De contrato | Que un cambio de API rompa el pull request y no el demo | schemathesis / pact |
| De integracion | Ejecucion completa contra el driver simulado, levantada en el pipeline | pytest + Docker |
| De interfaz | Navegacion y accesibilidad | Testing Library, Espresso |
| De resiliencia | Matar el agente y cortar el enlace a mitad de corrida (Sprint 3) | scripts de caos |

Umbral de cobertura: **70% del codigo nuevo**, como quality gate de SonarCloud que bloquea el merge.

La pieza que hace todo esto posible sin hardware es el driver simulado: permite probar la ejecucion
completa en integracion continua sin un solo instrumento fisico.
