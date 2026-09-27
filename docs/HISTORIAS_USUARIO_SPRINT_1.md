# Historias de Usuario Detalladas — Sprint 1

> **Proyecto:** CITAE Lab · Automatización de control, monitoreo y registro de equipos de laboratorio
> **Curso:** Proyecto Final II · Maestría en Ingeniería de Software (MISO) · Universidad de los Andes
> **Grupo:** 13
> **Fecha:** 27 de septiembre de 2026

Sprint 1 del Proyecto Final II: diez historias, 21 puntos, dos semanas. Al cierre, un operador crea
un procedimiento en la web, lo lanza, el agente local lo ejecuta contra un equipo simulado, y las
mediciones y la bitácora se ven en la web y en el celular.

---

## Tabla de contenido

- [Objetivo del Sprint 1](#objetivo-del-sprint-1)
- [Backlog del Sprint 1](#backlog-del-sprint-1)
- [Convenciones](#convenciones)
- [Historias de habilitación: ambiente de trabajo](#historias-de-habilitación-ambiente-de-trabajo)
- [Historias de habilitación: despliegue y arquitectura](#historias-de-habilitación-despliegue-y-arquitectura)
- [Historias funcionales: acceso y catálogo](#historias-funcionales-acceso-y-catálogo)
- [Historia funcional: autoría de procedimientos](#historia-funcional-autoría-de-procedimientos)
- [Historia funcional: ejecución](#historia-funcional-ejecución)
- [Historia funcional: móvil](#historia-funcional-móvil)
- [Trazabilidad y orden de ejecución](#trazabilidad-y-orden-de-ejecución)

---

## Objetivo del Sprint 1

**Objetivo:** dejar funcionando un esqueleto vivo que atraviese todas las costuras de la
arquitectura — nube, agente local, plug-in de driver, web y Android — con la funcionalidad mínima
que haga ese recorrido creíble.

Las costuras son donde está el riesgo. Si el agente no logra hablar con la nube, si el cargador de
plug-ins no carga nada, o si la app no logra autenticarse, es mejor enterarse en la semana 2 que en
la 7, cuando ya no queda margen para cambiar el diseño.

### Guión del demo de cierre

Es lo que se graba para el video de la semana 2, que exige funcionalidad demostrada y no solo
evidencia de proceso.

1. Un administrador inicia sesión en la web y registra el equipo simulado `FUENTE-SIM-01` con su
   interfaz, su driver y sus límites (máximo 5 V, 2 A).
2. Un ingeniero de pruebas crea el procedimiento *Encendido básico* con tres pasos: fijar 3,3 V,
   esperar 5 s, medir corriente con criterio de aceptación entre 0,8 y 1,2 A.
3. Intenta agregar un cuarto paso que fija 12 V y el sistema lo rechaza por exceder el límite del
   equipo.
4. Un operador lanza la ejecución sobre la unidad bajo prueba `FACSAT-EM-01`.
5. El agente local recibe la orden, ejecuta los tres pasos contra el driver simulado y reporta las
   mediciones.
6. La web muestra el avance paso a paso y el veredicto; el operador abre el celular y ve la misma
   ejecución con sus mediciones.
7. Se abre la bitácora de comandos emitidos y se corre la verificación de la cadena de hash, que da
   conforme.

### Fuera del Sprint 1, a propósito

Drivers de instrumentos reales, panel manual, reserva exclusiva con lease, aprobación formal de
procedimientos, checkpointing y reanudación, estado seguro configurable, despliegue en el
laboratorio, captura de artefactos y notificaciones push. Cada uno entra en el sprint donde retira su
propio riesgo, y ninguno hace falta para probar que la arquitectura se sostiene.

---

## Backlog del Sprint 1

| ID | Historia | Tipo | Componente | Puntos | Prioridad | Depende de |
|---|---|---|---|---|---|---|
| HA-01 | Repositorio, flujo de ramas y convención de commits | Habilitación | DevOps | 1 | Crítica | — |
| HA-02 | Integración continua con pruebas y cobertura | Habilitación | DevOps | 2 | Crítica | HA-01 |
| HA-03 | Ambiente local reproducible con un comando | Habilitación | DevOps | 1 | Crítica | HA-01 |
| HA-04 | Despliegue continuo a staging | Habilitación | Infraestructura | 2 | Alta | HA-02, HA-03 |
| HA-05 | Contrato de driver y carga dinámica de plug-ins | Arquitectura | Agente local | 2 | Crítica | HA-03 |
| HU-01 | Autenticarse en el sistema | Funcional | Identidad, Web | 2 | Crítica | HA-03 |
| HU-02 | Registrar y consultar equipos en el catálogo | Funcional | Catálogo, Web | 2 | Crítica | HU-01 |
| HU-03 | Crear un procedimiento de prueba | Funcional | Procedimientos, Web | 3 | Crítica | HU-02 |
| HU-04 | Lanzar una ejecución contra el equipo simulado | Funcional | Ejecución, Resultados, Agente, Web | 4 | Crítica | HU-03, HA-05 |
| HU-05 | Consultar las ejecuciones en curso desde el celular | Funcional | Android | 2 | Alta | HU-01, HU-04 |
| | **Total** | | | **21** | | |

La capacidad estimada del sprint es de 20 puntos (4 personas × 8 h × 2 semanas = 64 h, a razón de
3 horas-persona por punto). El backlog suma 21, un punto por encima. Se compromete así a propósito,
con **HU-05 como la primera candidata a salir** si la velocidad real de la primera semana queda por
debajo de lo previsto: es la única historia que no bloquea a ninguna otra.

---

## Convenciones

### Escala de estimación

Fibonacci reducida: 1, 2, 3, 5, 8. Un punto equivale a unas 3 horas-persona. Toda historia de más de
5 puntos se parte antes de entrar al sprint. Se estima por póquer de planeación entre los cuatro; si
dos estimaciones quedan a más de una carta de distancia, se discute el porqué y se vuelve a votar.

### Definición de listo

Una historia entra al sprint solo si cumple todo esto:

- Está escrita en formato *Como / quiero / para*, con un rol real del CITAE.
- Tiene al menos tres criterios de aceptación en formato *Dado / Cuando / Entonces*, verificables sin
  ambigüedad.
- Está estimada por el equipo.
- Tiene sus dependencias identificadas.
- Cabe en el sprint y, de preferencia, en tres días de trabajo.

### Definición de hecho

Una historia se cierra solo si cumple todo esto:

- El código está en `main` por un pull request aprobado por otro integrante.
- Tiene pruebas unitarias del código nuevo, con cobertura del código nuevo ≥ 70%.
- El quality gate de SonarCloud está en verde.
- Está desplegada en staging y funciona ahí, no solo en la máquina de quien la escribió.
- Cada criterio de aceptación fue verificado por alguien distinto de quien la implementó.
- El issue de Jira quedó en *Done*, con sus commits enlazados.

### Cómo leer cada historia

Cada una trae la historia en formato *Como / quiero / para*, el contexto mínimo para entenderla, los
criterios de aceptación numerados, y una ficha con componente, puntos, prioridad, dependencias, el
ASR que empieza a sostener y notas de interfaz cuando aplican.

Las diez se escribieron contra los criterios con los que el curso las revisa: son **independientes**
salvo por las dependencias declaradas, **negociables** en la implementación (ningún criterio dice
cómo, solo qué), **valiosas** para un rol concreto del laboratorio, **estimables**, **pequeñas**
(ninguna pasa de 4 puntos) y **comprobables** (todo criterio se puede convertir en una prueba
automatizada).

---

## Historias de habilitación: ambiente de trabajo

### HA-01 · Repositorio, flujo de ramas y convención de commits

**Como** integrante del equipo, **quiero** un repositorio con reglas de rama y de commit ya
establecidas, **para** que todo el trabajo quede trazable a una historia y nadie pueda romper `main`.

Es la primera historia del proyecto y bloquea a todas las demás. También es la que sostiene los 10
puntos de *flujo de trabajo y estrategia de versionamiento* de la rúbrica, que se evalúan cada
semana.

**Criterios de aceptación**

1. Dado el repositorio `citae-lab` configurado, cuando un integrante intenta empujar directo a
   `main`, entonces GitHub rechaza el push.
2. Dado un pull request abierto, cuando falta la aprobación de otro integrante o el pipeline está en
   rojo, entonces el botón de merge queda bloqueado.
3. Dado un commit con mensaje `feat(catalogo): PF2-12 registrar equipo`, cuando se empuja la rama,
   entonces el commit aparece enlazado en el issue PF2-12 de Jira.
4. Dado un commit cuyo mensaje no cumple Conventional Commits, cuando se intenta empujar, entonces el
   hook lo rechaza e indica el formato esperado.
5. Dado el repositorio recién clonado, cuando se lee `CONTRIBUTING.md`, entonces están documentados
   el formato de rama, el de commit, el flujo de pull request y la política de versionamiento
   semántico.

**Componente** DevOps · **Puntos** 1 · **Prioridad** Crítica · **Depende de** nada ·
**Sostiene** flujo de trabajo y versionamiento

---

### HA-02 · Integración continua con pruebas y cobertura

**Como** equipo, **quiero** que cada pull request corra las pruebas y publique la cobertura,
**para** detectar regresiones antes del merge y tener lista la evidencia que pide la rúbrica cada
semana.

**Criterios de aceptación**

1. Dado un pull request que toca solo `agente/`, cuando corre la integración continua, entonces se
   ejecuta únicamente el pipeline `ci-agente` y no los de los demás componentes.
2. Dado un pull request con una prueba en rojo, cuando termina el pipeline, entonces el merge queda
   bloqueado y el pull request indica qué prueba falló y en qué archivo.
3. Dado un pull request cuyo código nuevo tiene menos del 70% de cobertura, cuando SonarCloud evalúa
   el quality gate, entonces el pull request queda en rojo con el detalle de las líneas sin cubrir.
4. Dado un merge a `main`, cuando termina el pipeline, entonces la gráfica de cobertura de SonarCloud
   se actualiza y queda exportable como evidencia.
5. Dado un pipeline en verde, cuando se consulta su duración total, entonces no supera los 10
   minutos.

**Componente** DevOps · **Puntos** 2 · **Prioridad** Crítica · **Depende de** HA-01 ·
**Sostiene** ejecución de pruebas, code coverage chart

---

### HA-03 · Ambiente local reproducible con un comando

**Como** desarrollador, **quiero** levantar el sistema completo en mi máquina con un solo comando,
**para** trabajar sin depender de la nube ni del laboratorio.

Esta historia es la que permite que tres de los cuatro integrantes avancen sin acceso físico a los
equipos, que es la restricción real del proyecto.

**Criterios de aceptación**

1. Dado un computador con Docker instalado y el repositorio clonado, cuando se ejecuta
   `docker compose up`, entonces quedan corriendo PostgreSQL, Redis, RabbitMQ, MinIO, los servicios
   de la nube y el agente local con su driver simulado.
2. Dado el ambiente levantado por primera vez, cuando termina el arranque, entonces las migraciones
   ya se aplicaron y existe un usuario de prueba por cada rol.
3. Dado el ambiente levantado, cuando se consulta el endpoint de salud de cada componente, entonces
   todos responden `ok` dentro de los 90 segundos siguientes al arranque.
4. Dado que se detiene el ambiente y se vuelve a levantar, cuando arranca de nuevo, entonces los
   datos y los artefactos persisten.
5. Dado un integrante que sigue el `README` desde cero, cuando termina los pasos, entonces llega al
   ambiente funcionando sin necesitar ayuda de otro integrante.

**Componente** DevOps · **Puntos** 1 · **Prioridad** Crítica · **Depende de** HA-01 ·
**Sostiene** ASR-M2 (portabilidad del despliegue)

---

## Historias de habilitación: despliegue y arquitectura

### HA-04 · Despliegue continuo a staging

**Como** equipo, **quiero** que cada merge a `main` despliegue solo a staging, **para** poder mostrar
funcionalidad corriendo en la nube en cada cierre de semana sin trabajo manual.

**Criterios de aceptación**

1. Dado un merge a `main`, cuando termina el pipeline, entonces las imágenes de contenedor quedan
   publicadas en Artifact Registry etiquetadas con el SHA del commit.
2. Dado que las imágenes se publicaron, cuando corre el paso de despliegue, entonces los servicios
   quedan actualizados en el clúster de staging sin intervención manual.
3. Dado un despliegue en curso, cuando se consulta la URL de staging, entonces sigue respondiendo,
   porque la actualización es progresiva y no hay ventana de caída.
4. Dado un despliegue cuyo chequeo de salud no pasa, cuando el pipeline lo detecta, entonces revierte
   a la versión anterior y avisa en el canal del equipo.
5. Dado un tag `v0.1.0` empujado, cuando corre el pipeline de release, entonces la nota de versión se
   genera a partir de los commits y queda publicada en GitHub.

**Componente** Infraestructura · **Puntos** 2 · **Prioridad** Alta · **Depende de** HA-02, HA-03 ·
**Sostiene** parametrización de integración y despliegue continuo

---

### HA-05 · Contrato de driver y carga dinámica de plug-ins

**Como** arquitecto del sistema, **quiero** que el agente cargue drivers de equipo sin conocerlos en
tiempo de compilación, **para** que agregar un instrumento nuevo no obligue a modificar el núcleo.

Esta es la historia de arquitectura del sprint y la que sostiene ASR-M1, el requisito de calidad más
visible del proyecto. Sin ella el demo funcionaría igual, pero el sistema no cumpliría la promesa que
le hacemos al CITAE: que el catálogo de equipos puede crecer sin rehacer el software.

**Criterios de aceptación**

1. Dado el paquete `agente/drivers/`, cuando se agrega un módulo que implementa la interfaz
   `DriverEquipo` y se declara en el punto de entrada, entonces el agente lo descubre al arrancar sin
   ningún cambio en su núcleo.
2. Dado un driver registrado, cuando el motor de ejecución invoca `identificar()`, `configurar()`,
   `medir()`, `capturar()` o `estado_seguro()`, entonces el driver responde según el contrato y el
   motor no conoce ningún detalle del instrumento.
3. Dado un driver que lanza una excepción no controlada, cuando el motor lo invoca, entonces el
   agente aísla la falla, marca el equipo como fuera de servicio, registra el error y sigue
   corriendo.
4. Dado el driver simulado `FUENTE-SIM`, cuando el motor le pide fijar 3,3 V y luego medir corriente,
   entonces devuelve valores coherentes y deterministas, aptos para usarse como aserción en una
   prueba automatizada.
5. Dado que se agrega un driver nuevo, cuando se revisa el `git diff` del pull request, entonces los
   archivos modificados fuera de `agente/drivers/` son cero. Este criterio **es** la medida de ASR-M1
   y se verifica en el propio pull request.
6. Dado un equipo del catálogo cuyo driver no existe en el agente, cuando se intenta ejecutar un paso
   con él, entonces el sistema falla con un mensaje claro antes de emitir comando alguno.

**Componente** Agente local · **Puntos** 2 · **Prioridad** Crítica · **Depende de** HA-03 ·
**Sostiene** ASR-M1

---

## Historias funcionales: acceso y catálogo

### HU-01 · Autenticarse en el sistema

**Como** usuario del laboratorio, **quiero** entrar con mi usuario y contraseña y que el sistema
reconozca mi rol, **para** que solo pueda hacer lo que me corresponde.

Los cuatro roles son Administrador, Ingeniero de pruebas, Jefe de laboratorio y Operador. En un
laboratorio donde un comando equivocado puede dañar un componente satelital, saber quién hizo qué no
es burocracia.

**Criterios de aceptación**

1. Dado un usuario registrado con credenciales válidas, cuando inicia sesión desde la web, entonces
   recibe un token, llega a la pantalla principal y ve únicamente las opciones de su rol.
2. Dado un usuario con credenciales inválidas, cuando lo intenta, entonces el sistema responde con un
   mensaje genérico que no revela si falló el usuario o la contraseña.
3. Dado un usuario que falla cinco intentos seguidos, cuando lo intenta otra vez, entonces el sistema
   aplica una espera creciente antes de aceptar un nuevo intento.
4. Dado un token de acceso expirado, cuando el cliente hace una petición, entonces el sistema
   responde 401 y el cliente lo renueva con el refresh token sin sacar al usuario de la sesión.
5. Dado un usuario con rol Operador, cuando llama directamente el endpoint de alta de equipos,
   entonces el sistema responde 403, y esa acción tampoco aparece en su interfaz.
6. Dado cualquier inicio de sesión, exitoso o fallido, cuando ocurre, entonces queda un registro en la
   bitácora con actor, resultado, origen y marca de tiempo.

**Interfaz.** Pantalla de ingreso con retroalimentación inmediata en los campos: el error se muestra
junto al campo y no en un cuadro aparte, y el botón de entrar permanece deshabilitado hasta que ambos
campos tengan contenido.

**Componente** Identidad, Web · **Puntos** 2 · **Prioridad** Crítica · **Depende de** HA-03 ·
**Sostiene** ASR-S3

---

### HU-02 · Registrar y consultar equipos en el catálogo

**Como** administrador del laboratorio, **quiero** registrar un equipo con su interfaz de
comunicación y sus límites, y verlo en una lista, **para** que los procedimientos de prueba puedan
referirse a él.

Los límites de seguridad del equipo nacen en esta historia. Todo lo que después impida un comando
fuera de envolvente se apoya en lo que se registre aquí.

**Criterios de aceptación**

1. Dado el formulario de alta, cuando registro un equipo con marca, modelo, serial, inventario FAC,
   tipo (fuente, osciloscopio, generador o analizador), interfaz (USB, GPIB o Ethernet-LXI), recurso
   VISA, driver y límites de seguridad, entonces queda creado y aparece en la lista.
2. Dado un serial que ya existe en el catálogo, cuando intento registrarlo de nuevo, entonces el
   sistema lo rechaza señalando el campo duplicado y conserva lo demás que ya escribí.
3. Dado un límite de seguridad vacío, negativo o no numérico, cuando intento guardar, entonces el
   sistema lo rechaza y marca ese campo sin perder el resto del formulario.
4. Dado un catálogo con equipos registrados, cuando un Operador lo consulta, entonces ve marca,
   modelo, tipo, interfaz y estado (libre, reservado o fuera de servicio), y puede filtrar por tipo y
   por interfaz.
5. Dado un equipo de la lista, cuando lo abro, entonces veo su ficha completa, incluidos sus límites
   y el driver asociado.
6. Dado un usuario con rol Operador, cuando entra al catálogo, entonces la acción de registrar equipo
   no está disponible en su interfaz.
7. Dado un alta, una modificación o una baja de equipo, cuando ocurre, entonces queda en la bitácora
   con el actor y el detalle del cambio.

**Interfaz.** Lista con colores de fila alternos y disposición maestro-detalle: la lista a la
izquierda, la ficha del equipo seleccionado a la derecha. Menús desplegables para tipo e interfaz, y
validación con retroalimentación por campo.

**Componente** Catálogo, Web · **Puntos** 2 · **Prioridad** Crítica · **Depende de** HU-01 ·
**Sostiene** ASR-S1, ASR-S3

---

## Historia funcional: autoría de procedimientos

### HU-03 · Crear un procedimiento de prueba

**Como** ingeniero de pruebas, **quiero** armar un procedimiento como una secuencia ordenada de pasos
sobre equipos del catálogo, **para** poder ejecutarlo tantas veces como haga falta sin volver a
escribirlo.

Hoy los procedimientos AIV del CITAE viven en documentos de Word y se ejecutan leyéndolos y operando
los equipos a mano. Esta historia es la que convierte ese documento en algo que la máquina puede
ejecutar. Para el Sprint 1 bastan tres tipos de paso: configurar, esperar y medir. Capturar,
verificar y rampa entran después, y la razón por la que podrán entrar sin tocar el motor es que los
tipos de paso se registran, no se codifican en un condicional.

**Criterios de aceptación**

1. Dado el editor de procedimientos, cuando agrego pasos de tipo configurar, esperar y medir y asigno
   a cada uno un equipo del catálogo, entonces el procedimiento se guarda con los pasos en el orden
   definido.
2. Dado un paso de tipo configurar, cuando indico la magnitud y el valor (por ejemplo, voltaje
   3,3 V), entonces el sistema los guarda con su unidad y los muestra en el resumen.
3. Dado un paso de tipo medir, cuando defino un criterio de aceptación (por ejemplo, corriente entre
   0,8 y 1,2 A), entonces queda asociado al paso y aparece en el resumen.
4. Dado un paso que fija un valor por encima del límite registrado para el equipo seleccionado,
   cuando intento guardar, entonces el sistema lo rechaza indicando qué límite se viola y cuál es el
   valor máximo permitido.
5. Dado un procedimiento ya guardado, cuando lo modifico y vuelvo a guardar, entonces se crea una
   versión nueva y la anterior queda consultable con su fecha y su autor.
6. Dado un procedimiento con pasos, cuando reordeno o elimino un paso, entonces el orden se
   actualiza, la numeración se recalcula y el cambio queda en la bitácora.
7. Dado un procedimiento sin ningún paso, cuando intento marcarlo como listo para ejecutar, entonces
   el sistema lo impide indicando que debe tener al menos un paso.

**Interfaz.** Lista de procedimientos a la izquierda y editor a la derecha. Cada paso se despliega en
un acordeón, de modo que la secuencia completa se ve de un vistazo y solo el paso en edición muestra
sus campos. Menús desplegables para tipo de paso, equipo y magnitud. El criterio de aceptación se
captura con dos campos numéricos y su unidad, nunca como texto libre, porque después tiene que
evaluarse solo.

**Alcance.** La aprobación formal del procedimiento por el Jefe de laboratorio, con flujo
maker-checker, entra en Sprint 2. En Sprint 1 el propio ingeniero lo pasa de Borrador a Listo con una
sola acción.

**Componente** Procedimientos, Web · **Puntos** 3 · **Prioridad** Crítica · **Depende de** HU-02 ·
**Sostiene** ASR-S1, ASR-M3

---

## Historia funcional: ejecución

### HU-04 · Lanzar una ejecución y verla correr contra el equipo simulado

**Como** operador, **quiero** lanzar un procedimiento sobre una unidad bajo prueba y ver su avance
paso a paso, **para** saber que la prueba está corriendo y qué está midiendo.

Es la historia más grande del sprint y la que de verdad pone a prueba la arquitectura: atraviesa la
web, el gateway, el servicio de ejecución, el bus, el agente local, el cargador de plug-ins, el
driver, y todo el camino de vuelta con las mediciones. Si alguna decisión de diseño está equivocada,
se ve aquí.

**Criterios de aceptación**

1. Dado un procedimiento listo y un agente en línea, cuando lanzo la ejecución indicando la unidad
   bajo prueba, entonces se crea en estado En curso y el agente la recibe en menos de 5 segundos.
2. Dado que la ejecución arrancó, cuando el agente ejecuta cada paso contra el driver simulado,
   entonces la web muestra el paso actual y las mediciones ya tomadas, refrescándose al menos cada 2
   segundos sin que el usuario recargue.
3. Dado un paso de tipo medir con criterio de aceptación, cuando la medición cae dentro del rango,
   entonces el paso se marca conforme; cuando cae fuera, se marca no conforme.
4. Dado un paso no conforme en un procedimiento configurado para detenerse ante fallo, cuando ocurre,
   entonces la ejecución termina en estado Fallida y los pasos siguientes no se ejecutan.
5. Dado que no hay ningún agente en línea, cuando intento lanzar una ejecución, entonces el sistema
   la rechaza con un mensaje que lo explica, en lugar de dejarla colgada esperando.
6. Dada una ejecución terminada, cuando la consulto, entonces veo todas sus mediciones con paso,
   equipo, valor, unidad, marca de tiempo y veredicto.
7. Dada cualquier orden enviada a un equipo, cuando se emite, entonces queda en la bitácora
   append-only con actor, equipo, comando, parámetros y marca de tiempo.
8. Dada la bitácora de una ejecución, cuando se corre la verificación de la cadena de hash, entonces
   reporta cero eslabones rotos; y si se altera un registro directamente en la base de datos,
   entonces la verificación señala exactamente ese registro.

**Alcance.** La reserva exclusiva con lease renovable, el streaming por WebSocket y la capacidad de
abortar entran en Sprint 2. Aquí la web consulta por sondeo cada 2 segundos: alcanza para el demo y
no compromete el diseño posterior.

**Riesgo y cómo manejarlo.** Es la historia con más superficie del sprint. Conviene partirla en dos
tareas de Jira desde el primer día, con el contrato de mensajes acordado por escrito antes de
empezar: el camino nube hacia agente (orden, recepción, confirmación) y el camino agente hacia nube
(mediciones, bitácora, estado). Así dos personas trabajan en paralelo y la integración no se descubre
el último día.

**Componente** Ejecución, Resultados, Agente local, Web · **Puntos** 4 · **Prioridad** Crítica ·
**Depende de** HU-03, HA-05 · **Sostiene** ASR-S2, y sienta la base de ASR-D1 y ASR-D2

---

## Historia funcional: móvil

### HU-05 · Consultar las ejecuciones en curso desde el celular

**Como** operador, **quiero** ver desde mi celular qué ejecuciones están corriendo y en qué paso van,
**para** no tener que volver al puesto de trabajo cada vez que quiero saberlo.

En un cuarto limpio ISO 8, con bata y guantes ESD, volver al PC no es trivial. Por eso el celular
sirve para mirar y no para definir: la autoría de procedimientos se queda en el escritorio a
propósito.

**Criterios de aceptación**

1. Dado un operador con credenciales válidas, cuando inicia sesión en la app Android, entonces ve la
   lista de ejecuciones en curso con procedimiento, unidad bajo prueba, paso actual y porcentaje de
   avance.
2. Dada la lista, cuando selecciono una ejecución, entonces veo el estado de cada paso y sus últimas
   mediciones con valor, unidad y veredicto.
3. Dado que el celular pierde conectividad, cuando la recupera, entonces la app vuelve a mostrar el
   estado actual sin que el usuario tenga que cerrar sesión y volver a entrar.
4. Dado que no hay ninguna ejecución en curso, cuando abro la app, entonces veo un estado vacío que
   lo dice con claridad, no una lista en blanco.
5. Dado el dispositivo en modo oscuro, cuando abro la app, entonces la interfaz respeta el tema del
   sistema y los contrastes cumplen WCAG AA.
6. Dado un usuario que no ha iniciado sesión, cuando abre la app, entonces llega a la pantalla de
   ingreso y no puede ver ninguna ejecución.
7. Dado un dispositivo con idioma inglés, cuando abro la app, entonces los textos aparecen en inglés,
   porque las cadenas están externalizadas desde el primer día.

**Interfaz.** Lista de tarjetas, una por ejecución, con barra de avance y paso actual visibles sin
abrir el detalle. Al tocar una tarjeta se navega a la vista de detalle. Ninguna acción de edición en
esta versión.

**Alcance.** Las notificaciones push, las alertas y el botón de abortar entran en Sprint 2.

**Componente** Android · **Puntos** 2 · **Prioridad** Alta · **Depende de** HU-01, HU-04 ·
**Sostiene** condiciones de usabilidad: accesibilidad e internacionalización

---

## Trazabilidad y orden de ejecución

### Qué ASR toca cada historia

| ASR | Historias del Sprint 1 | Qué queda para después |
|---|---|---|
| ASR-M1 · Equipo nuevo = un solo módulo | HA-05 | Medir el esfuerzo real con un instrumento físico (Sprint 2) |
| ASR-M2 · Mismo artefacto en nube y laboratorio | HA-03, HA-04 | Despliegue completo dentro del laboratorio, validado (Sprint 3) |
| ASR-M3 · Tipo de paso nuevo sin tocar el motor | HU-03 | Pasos de capturar, verificar y rampa (Sprints 2 y 3) |
| ASR-D1 · Sobrevivir al corte de enlace | HU-04 (base) | Buffer durable, outbox y resincronización (Sprint 3) |
| ASR-D2 · Sobrevivir al reinicio del agente | HU-04 (base) | Checkpointing, reanudación y estado seguro (Sprint 3) |
| ASR-D3 · Degradación parcial en la nube | ninguna | Circuit breaker hacia notificaciones y reportes (Sprint 3) |
| ASR-S1 · Ningún comando fuera de envolvente | HU-02, HU-03 | El supervisor del agente que valida antes de emitir (Sprint 2) |
| ASR-S2 · Bitácora inalterable | HU-04 | Verificación programada y reporte de trazabilidad (Sprint 3) |
| ASR-S3 · Autorización y aprobación | HU-01, HU-02 | Flujo maker-checker de aprobación (Sprint 2) |

ASR-D3 no lo toca ninguna historia de este sprint, y está bien: la degradación parcial solo tiene
sentido cuando hay servicios que puedan caerse de forma interesante.

### Orden dentro de las dos semanas

**Semana 1.** HA-01 primero, entre todos, porque desbloquea el resto. Luego cuatro frentes en
paralelo: DevOps toma HA-02 y HA-03; el frente de agente toma HA-05; el de backend toma HU-01; el de
web arma el esqueleto del proyecto y la pantalla de ingreso.

Al viernes de la semana 1 tiene que existir: el ambiente montado, un inicio de sesión que funciona, y
el agente cargando un driver simulado. Eso es lo que se muestra en el video de esa entrega.

**Semana 2.** HU-02, luego HU-03, en pareja backend y web. HU-04 partida en dos tareas paralelas.
HA-04 en paralelo por el frente DevOps. HU-05 al final, cuando el API de HU-04 esté estable. El
jueves se congela: se verifican los criterios cruzados y se generan los charts. El viernes, release
`v0.1.0`, retrospectiva y grabación del video.

**El riesgo de esta secuencia** es que HU-02, HU-03 y HU-04 forman una cadena de tres historias
dependientes dentro de una sola semana. La forma de romperla: acordar los contratos de API de las
tres el lunes de la semana 2 y dejar que web y Android trabajen contra respuestas simuladas mientras
el backend las implementa. Si se espera a que cada una termine para empezar la siguiente, la semana
no alcanza.

---

**Proyecto Final II** · Maestría en Ingeniería de Software · Universidad de los Andes · Grupo 13
