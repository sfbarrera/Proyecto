# Pruebas del agente

| Tipo | Que cubre |
|---|---|
| Unitarias | Motor de ejecucion, supervisor de limites, buffer durable |
| De contrato | Que todo driver cumpla la interfaz `DriverEquipo` |
| De integracion | Ejecucion completa de un procedimiento contra el driver simulado |
| De resiliencia | Matar el agente a mitad de corrida y verificar reanudacion o estado seguro (Sprint 3) |

Las de integracion corren en el pipeline sin ningun instrumento fisico, gracias al simulador.

```bash
pytest --cov=agente --cov-report=xml
```
