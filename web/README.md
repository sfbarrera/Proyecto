# Cliente web de escritorio

React 18 + TypeScript + Vite. Es donde se **define**: catalogo de equipos, autoria de procedimientos
y lanzamiento de corridas. El monitoreo tambien esta aqui, con vista responsive.

## Pantallas del Sprint 1

| Pantalla | Historia |
|---|---|
| Ingreso | HU-01 |
| Catalogo de equipos (lista y ficha) | HU-02 |
| Editor de procedimientos | HU-03 |
| Lanzar y monitorear una corrida | HU-04 |

## Patrones de interfaz

- Retroalimentacion de validacion **por campo**, nunca en un cuadro aparte.
- Maestro-detalle en catalogo y en procedimientos.
- Acordeon por paso en el editor de procedimientos, para ver la secuencia completa de un vistazo.
- Colores de fila alternos en las listas largas.
- Menus desplegables para conjuntos cerrados: tipo de equipo, interfaz, tipo de paso, magnitud.

## Accesibilidad e internacionalizacion

Contraste WCAG AA y cadenas externalizadas con `react-i18next` desde el primer dia. Es criterio de
usabilidad del curso y se verifica en el pipeline.

## Ejecutar

```bash
cd web
npm install
npm run dev
```
