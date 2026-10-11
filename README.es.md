<p align="center">
  <img src="assets/banner.jpg" alt="AI Agent Products Reviewed. A public catalog of AI agent products." width="100%">
</p>

<p align="center">
  <a href="README.md">English</a>
  &nbsp;·&nbsp;
  <a href="README.zh-TW.md">繁體中文</a>
  &nbsp;·&nbsp;
  <a href="README.ja.md">日本語</a>
  &nbsp;·&nbsp;
  <a href="README.ko.md">한국어</a>
  &nbsp;·&nbsp;
  <strong>Español</strong>
</p>

<p align="center">
  <a href="LICENSE"><img src="https://img.shields.io/badge/license-MIT-3d3a36" alt="MIT License"></a>
  <a href="CONTRIBUTING.md"><img src="https://img.shields.io/badge/PRs-welcome-3d3a36" alt="PRs welcome"></a>
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/sponsor-GitHub%20Sponsors-ea4aaa?logo=githubsponsors&logoColor=white" alt="GitHub Sponsors"></a>
</p>

<p align="center">
  <a href="#contents">Contenido</a>
  &nbsp;·&nbsp;
  <a href="#contributing">Contribuir</a>
  &nbsp;·&nbsp;
  <a href="CODE_OF_CONDUCT.md">Código de conducta</a>
  &nbsp;·&nbsp;
  <a href="#support">Apoyo</a>
</p>

# AI Agent Products Reviewed

Este es un hub público de productos de agentes de IA. Productos, frameworks,
bancos de trabajo, capas de memoria y runtimes están repartidos entre
repositorios y sitios oficiales. Este catálogo los reúne. Cada producto es
una Cell: se ve qué es, y qué parte cubre en un equipo de orquestación de
agentes de IA.

El catálogo sirve para comparar, no para ordenar. Leemos cada producto con
las mismas preguntas: cómo se organizan los agentes, las tareas, el contexto,
los entornos de ejecución y la supervisión humana, y en qué se convierten
esas ideas dentro de un espacio de trabajo de agentes de IA en 2D o en 3D.

La licencia es [MIT](LICENSE). La forma de trabajar juntos está en el
[código de conducta](CODE_OF_CONDUCT.md). Las notas de insight, el código de
conducta y la guía de contribución están en chino tradicional.

<h2 id="contributing">Contribuir</h2>

**Las contribuciones son bienvenidas.** Este catálogo depende de quienes
añaden productos de agentes de IA que siguen repartidos.

- Propón un producto que todavía no está.
- Escribe una nota de insight y colócala en un asiento del equipo de orquestación.
- Corrige una Cell que ya está: hechos viejos, enlaces rotos o el asiento equivocado.

Empieza por [CONTRIBUTING.md](CONTRIBUTING.md). Un producto nuevo por pull
request. Puedes escribir la nota antes de haber usado el producto; deja el
status en `untried`. Cuando añadas una fila aquí, añade la misma Cell, en el
mismo asiento y con el mismo enlace, a [README.md](README.md),
[README.zh-TW.md](README.zh-TW.md), [README.ja.md](README.ja.md) y
[README.ko.md](README.ko.md).

<h2 id="contents">Contenido</h2>

- [Contribuir](#contributing)
- [Objetivos de investigación](#research-goals)
- [Estructura del repositorio](#repository-layout)
- [Modelo de Cell](#cell-model)
- [Proceso de revisión](#review-process)
- [Lugar en un equipo de orquestación](#orchestration-team)
- [Apoyar este catálogo](#support)

Los proyectos que quieras ejecutar tú se clonan fuera de este repositorio.
Aquí quedan el enlace a la fuente, el análisis del producto y de sus
funciones, lo que de verdad se probó, y lo que sugiere para un espacio de
trabajo en 2D o en 3D. Un producto sin repositorio público se registra desde
su página oficial y la información de versión que haya.

<h2 id="research-goals">Objetivos de investigación</h2>

Cada revisión de una Cell debe ayudar a responder:

- ¿Qué nuevo espacio de trabajo de agentes, o qué modelo de interacción, representa?
- ¿Cómo presenta agentes, tareas, ramas, sandboxes, artefactos y el avance?
- ¿Cómo delega, compara, interviene, verifica y recupera el control una persona?
- ¿Qué capacidades vienen del modelo, y cuáles del runtime del agente o de la orquestación?
- ¿En qué se convierte en un espacio 2D (un lugar de trabajo plano o en píxeles)?
- ¿En qué se convierte en un espacio 3D (una oficina en la que se puede entrar, no un objeto 3D)?
- ¿Qué patrones vale la pena adoptar, rediseñar o evitar a propósito?

Ángulos principales:

1. Espacio de trabajo y organización espacial
2. Orquestación multiagente
3. Contexto, memoria y traspaso
4. Runtime, sandbox y permisos
5. Estado, avance y observabilidad
6. Control con una persona en el bucle
7. Artefactos, procedencia y revisión
8. Colaboración y extensibilidad

<h2 id="repository-layout">Estructura del repositorio</h2>

- [`README.md`](README.md): este catálogo en inglés. Es la página de inicio del repositorio.
- [`README.zh-TW.md`](README.zh-TW.md): el catálogo en chino tradicional.
- [`README.ja.md`](README.ja.md): el catálogo en japonés.
- [`README.ko.md`](README.ko.md): el catálogo en coreano.
- [`README.es.md`](README.es.md): este catálogo en español.
- [`CONTRIBUTING.md`](CONTRIBUTING.md): cómo proponer, añadir o corregir una Cell. El texto está en chino tradicional.
- [`cells.yaml`](cells.yaml): metadata estructurada de cada Cell.
- [`reviews/README.md`](reviews/README.md): reglas comunes de revisión y el estándar de evidencia.
- [`reviews/`](reviews/): una nota de insight por Cell. Léelas en el navegador con el enlace renderizado de GitHub. Véase [`reviews/README.md`](reviews/README.md).
- `/workspace-labs/<cell-name>`: una ruta local sugerida para experimentar. Está fuera de este repositorio y Git no la sigue.

<h2 id="cell-model">Modelo de Cell</h2>

Una **Cell** es una unidad de investigación en las tablas. Una Cell no guarda
el árbol de código upstream. Enlaza a la fuente y conserva lo que aprendimos
del producto.

- El ID de una Cell con repositorio es la URL canónica del repositorio de GitHub, por ejemplo `https://github.com/mattpocock/sandcastle`.
- Un producto sin repositorio público usa su URL canónica oficial, por ejemplo `https://www.conductor.build/`.
- De las URL de GitHub se quitan `.git`, la query, el fragmento y la `/` final.
- Si un repositorio cambia de nombre o se transfiere, la URL canónica nueva pasa a ser el ID y la URL vieja va en `aliases`.

<h2 id="review-process">Proceso de revisión</h2>

1. Añade el producto como Cell en `cells.yaml` con status `untried`. Sigue [`CONTRIBUTING.md`](CONTRIBUTING.md). Si la fuente es un repositorio de GitHub o una URL oficial, también puedes llamar a [`/create-cell-pr`](.cursor/skills/create-cell-pr/SKILL.md) y dejar que un subagente escriba la nota y abra su propio PR.
2. Si la fuente es pública, clónala en un área de experimento aparte y registra el SHA completo del commit que de verdad probaste. Si no, registra la versión del producto.
3. Sigue [`reviews/README.md`](reviews/README.md) y parte de [`reviews/_template.md`](reviews/_template.md).
4. Haz una comprobación directa, como recomienda upstream, solo cuando la documentación oficial, el código o una demo no pueden responder una pregunta clave. Docker no es obligatorio.
5. Actualiza el estado tried/untried y la lectura inicial, y coloca la Cell en un asiento de abajo. Un cambio claro por commit atómico.

No metas proyectos candidatos dentro de este repositorio. Si un experimento
necesita cambios de código, haz fork de ese producto. El fork guarda los
cambios de código. La revisión se queda aquí.

<h2 id="orchestration-team">Lugar en un equipo de orquestación</h2>

Estas tablas clasifican qué es un producto y qué parte cubre en un equipo de
orquestación de agentes de IA. Cada Cell tiene un asiento principal. Las
capacidades que también tocan un asiento vecino van en Contribución. Si
alguien lo ha probado queda en la nota y en [`cells.yaml`](cells.yaml), en
`evaluation.status`.

El nombre de la Cell enlaza a la nota de insight renderizada en GitHub. El
ID de la Cell está al principio de la nota y en [`cells.yaml`](cells.yaml).

| Asiento | Qué hace en el equipo |
| --- | --- |
| Orquestación | Parte el trabajo, lo asigna al agente adecuado y trae el resultado de vuelta. |
| Gobernanza | Cubre objetivos, plantilla, presupuesto, permisos y si el trabajo puede empezar cuando nadie está mirando. |
| Banco de trabajo | Permite a una persona ver, comparar y entrar en varios agentes a la vez. |
| Presencia | Usa el espacio para mostrar quién está ocupado, quién espera y quién terminó. |
| Trabajador | Quien lee, escribe, opera una interfaz, habla o responde, y el runtime que los arma. |
| Memoria | Deja el contexto anterior disponible para el siguiente turno y el siguiente agente. |
| Método | Dice cómo debe hacerse este trabajo: skills, procedimiento y cómo se ve lo terminado. |
| Límite | Decide dónde se ejecuta y qué archivos, red, terminal y navegador puede tocar. |
| Evaluación | Guarda puntuaciones, trazas e intentos comparables para juzgar cómo fue esta ejecución. |
| Superficie de entrega | La capa de salida que el equipo edita junta: pantallas, documentos, hojas y diapositivas. |

### Orquestación

Parte el trabajo, lo asigna al agente adecuado y trae el resultado de vuelta.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Sandcastle](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/sandcastle.md) | Biblioteca de orquestación | Ejecuta un coding agent en un entorno aislado desde código y, al terminar, fusiona según la estrategia de ramas. |
| [Octop Harness](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-harness-cell-7618/reviews/octop-harness.md) | Runtime de despliegue | Registra varios agentes aislados entre sí dentro de un solo proceso. |
| [OpenRig](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openrig-cell-7aa3/reviews/openrig.md) | Harness de equipo | Describe asientos y miembros en YAML, los arranca de una vez y les asigna trabajo. |
| [Deep Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-deepagents-cell-9e0b/reviews/deepagents.md) | Harness para tareas largas | Empaqueta subagentes, un sistema de archivos virtual, memoria y aprobación humana en un runtime de tareas largas. |
| [CrewAI](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-crewai-cell-90bd/reviews/crewai.md) | Framework de orquestación | Arma un equipo con roles y tareas, y por fuera un flujo de eventos controla las ramas. |
| [OmO](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-openagent-cell-90bd/reviews/oh-my-openagent.md) | Capa de orquestación | La sesión principal parte y asigna el trabajo; trabajadores temporales editan archivos y devuelven evidencia. |
| [Oh My OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-oh-my-opencode-cell-103e/reviews/oh-my-opencode.md) | Plugin de varios roles | Convierte un desarrollo en entrevista, plan, asignación, implementación e investigación. |
| [AgentScope](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentscope-cell-fb52/reviews/agentscope.md) | Framework de orquestación | Arma agentes en código y deja que se pasen el trabajo. |
| [Gas Town](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gastown.md) | Orquestación por CLI | Programa varios coding agents a la vez y guarda el estado del trabajo en un libro recuperable. |
| [Routa](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/routa.md) | Mesa de coordinación de entrega | Parte un chat largo en tareas, un tablero, notas y contratos de especialistas. |
| [Agency Swarm](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agency-swarm-cell-ad04/reviews/agency-swarm.md) | Framework de orquestación | Usa roles y un grafo de comunicación en un solo sentido para decidir quién asigna trabajo y quién se queda con la conversación. |
| [Agent Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-squad-cell-ad04/reviews/agent-squad.md) | Enrutador de conversación | Entrega cada frase al agente especialista y conserva el chat. |
| [Amux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-amux-cell-80a8/reviews/amux.md) | Plano de control autoalojado | Da a los coding agents que ya existen un tablero compartido, un canal de mensajes y un horario. |
| [AutoAgent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autoagent-cell-ca43/reviews/autoagent.md) | Framework de orquestación | Arma especialistas y flujos en lenguaje natural, y un agente de triaje asigna el trabajo. |
| [AutoGen](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-autogen-cell-9381/reviews/autogen.md) | Framework de orquestación | Arma en código un grupo de agentes que trabajan solos y también con una persona. |
| [BeeAI Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-beeai-framework-cell-ad04/reviews/beeai-framework.md) | Framework de orquestación | Escribe agentes y flujos que se pasan el trabajo, en Python o TypeScript. |
| [Bernstein](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-bernstein-cell-80a8/reviews/bernstein.md) | Orquestación programada | Reparte un objetivo entre agentes CLI y un planificador decide quién toma el trabajo, cuándo reintentar y cuándo fusionar. |
| [LangGraph](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langgraph-cell-9381/reviews/langgraph.md) | Runtime de grafos | Orquesta flujos largos y reanudables con estado compartido, nodos y aristas. |
| [MetaGPT](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-metagpt-cell-9381/reviews/metagpt.md) | Framework de orquestación | Organiza roles en una compañía de software que entrega diseño y código según un SOP. |
| [Microsoft Agent Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-microsoft-agent-framework-cell-9381/reviews/microsoft-agent-framework.md) | Framework de orquestación | Escribe un agente que llama herramientas, o encadena varios agentes en un workflow. |
| [MS-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ms-agent-cell-ad04/reviews/ms-agent.md) | Harness para tareas largas | Se encarga de la planificación, los permisos, los subagentes y la memoria de proyecto que puede retomarse al día siguiente. |
| [OpenAI Agents SDK](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openai-agents-python-cell-9381/reviews/openai-agents-python.md) | SDK de agentes | Compone flujos multiagente con agentes, handoffs y guardrails. |
| [PocketFlow](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-cell-ca43/reviews/pocketflow.md) | Framework de grafos | Escribe una aplicación como nodos, acciones y un almacén compartido. |
| [Pragma](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pragma-cell-80a8/reviews/pragma.md) | Plataforma de Agent Team | Empaqueta especialistas, flujos, herramientas, memoria y puntos en los que una persona tiene que asentir, en un equipo que puedes llevarte. |
| [Youtu-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-youtu-agent-cell-ad04/reviews/youtu-agent.md) | Framework de orquestación | Arma y ejecuta agentes desde YAML, y puede evaluar y mejorar esa misma configuración. |

### Gobernanza

Cubre objetivos, plantilla, presupuesto, permisos y si el trabajo puede empezar cuando nadie está mirando.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Paperclip](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-paperclip-cell-7aa3/reviews/paperclip.md) | Plano de control de la organización | Despierta agentes externos como empleados, con objetivos, un organigrama, un presupuesto y un heartbeat. |
| [Agenta](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agenta-cell-ad04/reviews/agenta.md) | Espacio de equipo | Permite a un equipo armar colegas que empiezan solos, y ajustar instrucciones, habilidades y permisos. |

### Banco de trabajo

Permite a una persona ver, comparar y entrar en varios agentes a la vez.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Conductor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/conductor.md) | ADE de escritorio | Pone worktrees, vistas previas y fusiones de varios coding agents en una sola consola. |
| [Maestro](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/maestro.md) | ADE de escritorio | Avanza varios proyectos y una cola de tareas desde una consola pensada primero para el teclado. |
| [Orca](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/orca.md) | ADE de escritorio | Da a cada agente CLI su propio worktree y muestra chat, terminal y diff en una sola app. |
| [cmux](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cmux.md) | Espacio de terminal | Organiza muchas sesiones CLI con pestañas, divisiones y un aviso cuando un agente te necesita. |
| [Emdash](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/emdash.md) | ADE de escritorio | Ejecuta agentes ya existentes por tarea y muestra el diff, la CI y el PR en la misma app. |
| [Paseo](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/paseo.md) | Plano de control autoalojado | Un daemon ejecuta CLIs ya existentes en local; escritorio, teléfono y web vuelven a esa máquina. |
| [Nimbalyst](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/nimbalyst.md) | Banco de trabajo visual | Personas y agentes editan los mismos archivos, y las sesiones paralelas quedan aisladas en worktrees. |
| [Odysseus](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-odysseus-cell-7aa3/reviews/odysseus.md) | Espacio personal autoalojado | Reúne chat, investigación, documentos, correo y tareas en una sola interfaz. |
| [T3 Code](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-t3code-cell-7aa3/reviews/t3code.md) | Superficie de control del harness | Se conecta a CLIs que ya iniciaron sesión en local y usa una sola UI para abrir hilos, ver diffs y aprobar permisos. |
| [OpenChamber](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openchamber-cell-0717/reviews/openchamber.md) | Banco de trabajo de OpenCode | Supervisa las mismas sesiones de OpenCode desde escritorio, navegador, VS Code y teléfono. |
| [Ekko Studio](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ekko-studio-cell-fb52/reviews/ekko-studio.md) | Banco de trabajo y flujo de nodos | Alterna entre un chat individual, una sala de grupo y un grafo de nodos ejecutable. |
| [Codeg](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-codeg-cell-fb52/reviews/codeg.md) | ADE multiagente | Usa ACP para reunir varios CLI en un mismo chat, diff y aviso de permisos. |
| [Agentrove](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentrove-cell-c2d2/reviews/agentrove.md) | Espacio de código autoalojado | Une un workspace a un sandbox y arranca los agentes instalados mediante ACP. |
| [cc-haha](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cc-haha-cell-c2d2/reviews/cc-haha.md) | Banco de trabajo local de escritorio | Edita un proyecto en lenguaje llano y ve el diff; el teléfono y los chats vuelven a este equipo. |
| [iPolloWork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/ipollowork.md) | Banco de trabajo multi-motor | Reúne motores como OpenCode y Codex en un solo flujo de tareas, progreso y archivos. |
| [Golutra](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/golutra.md) | Sala de chat de terminal | Convierte CLIs locales en miembros de un canal y devuelve su salida a una sola conversación. |
| [Claude Code Bridge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/claude-codex-bridge.md) | Banco de trabajo CLI | Muestra varios CLI a la vez y les deja pasarse el trabajo por mensaje. |
| [AgentSpace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agentspace-cell-80a8/reviews/agentspace.md) | Espacio web de colaboración | Da a las personas y a empleados digitales con un puesto un mismo hogar para mensajes, documentos y aprobaciones. |
| [Buzz](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-buzz-cell-80a8/reviews/buzz.md) | Espacio de colaboración | Mete a personas y agentes en los mismos canales, hilos, lienzos y flujos de trabajo. |
| [Claude Squad](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-claude-squad-cell-80a8/reviews/claude-squad.md) | Supervisor de terminal | Da a cada sesión su propio worktree y tmux para que no compartan un directorio. |
| [Free4chat](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-free4chat-cell-fb52/reviews/free4chat.md) | Sala temporal de colaboración | Usa un enlace para traer a personas del navegador y agentes locales a un tramo corto de trabajo compartido. |
| [Hermes Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hermes-workspace-cell-ad04/reviews/hermes-workspace.md) | Puesto de mando web | Usa un navegador para ver chats, terminales, memoria, habilidades y varios trabajadores de Hermes. |
| [Kun](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-kun-cell-ad04/reviews/kun.md) | Banco de trabajo local | Convierte un objetivo en una entrega que se puede comprobar, entre Code, Design, Work y Rooms. |
| [Meldwork](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-meldwork-cell-80a8/reviews/meldwork.md) | ADE de escritorio | Pone CLIs ya instalados en un mismo caso: una persona, varias respuestas, o una discusión antes de adoptar un resultado. |
| [Mycelium](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mycelium-cell-ca43/reviews/mycelium.md) | Sala compartida | Comparte un chat, un tablero y una memoria en Markdown entre una persona y los coding agents que ya usa. |
| [OpenHands](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openhands-cell-80a8/reviews/openhands.md) | Consola de desarrollo autoalojada | Dibuja chat, terminal, navegador, archivos y automatización; las acciones corren en un sandbox al lado. |

### Presencia

Usa el espacio para mostrar quién está ocupado, quién espera y quién terminó.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Agent Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/agent-office.md) | Oficina 3D | Da a cada repositorio un piso; te acercas, lees la terminal del trabajador y escribes con esa persona. |
| [Open Office](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openoffice-cell-c2d2/reviews/openoffice.md) | Equipo pixel 2D | Miembros con nombre, en un mismo piso, planifican, escriben código, revisan y previsualizan. |
| [Pixel Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pixel-agents.md) | Oficina pixel 2D | Un agente en marcha se vuelve una figura en el piso, con una burbuja cuando se atasca. |

### Trabajador

Quien lee, escribe, opera una interfaz, habla o responde, y el runtime que los arma.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Pi](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/pi.md) | Runtime de agente de código | Un coding agent mínimo e integrable, que corre como CLI o dentro de otro producto. |
| [Octop](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-cell-7aa3/reviews/octop.md) | Plataforma de asistente autoalojada | Varias personas mantienen cada una a sus expertos y hablan con ellos en la web, el escritorio y varios chats. |
| [OpenClaw](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openclaw-cell-7aa3/reviews/openclaw.md) | Runtime de asistente | Un Gateway residente que actúa desde los chats que ya usas: shell, horarios y acciones del dispositivo. |
| [OpenCode](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-opencode-cell-9e0b/reviews/opencode.md) | Agente de código | Lee, escribe y ejecuta comandos en un proyecto, y puede llamar a especialistas. |
| [Agent-S](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-agent-s-cell-9381/reviews/agent-s.md) | Agente de uso de escritorio | Mira la pantalla y termina el trabajo en aplicaciones normales con ratón y teclado. |
| [Atomic Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-atomic-agents-cell-ca43/reviews/atomic-agents.md) | Biblioteca de piezas | Parte un flujo en piezas con esquema y las conecta solo después de validar entradas y salidas. |
| [HelloAgents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-helloagents-cell-ca43/reviews/helloagents.md) | Biblioteca de componentes | Usa un registro de herramientas para una vuelta: pedir una herramienta, ejecutarla y volver al modelo. |
| [LangChain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-langchain-cell-ca43/reviews/langchain.md) | Framework de agentes | Arma un bucle que llama herramientas a partir de un modelo, herramientas y un prompt. |
| [LiveKit Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-livekit-agents-cell-9381/reviews/livekit-agents.md) | Runtime de voz | Mete un programa en una sala en tiempo real como participante que oye, habla y ve. |
| [Open-AutoGLM](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-open-autoglm-cell-ca43/reviews/open-autoglm.md) | Agente de uso del teléfono | Manda un recado en una frase y lo termina en una app del teléfono conectado. |
| [Qwen-Agent](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-qwen-agent-cell-9381/reviews/qwen-agent.md) | Framework de agentes | Compone un modelo, herramientas y documentos en un Assistant que transmite la respuesta. |
| [TEN Framework](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ten-framework-cell-9381/reviews/ten-framework.md) | Runtime de voz | Arma una conversación de voz en tiempo real con un grafo de extensiones intercambiables. |

### Memoria

Deja el contexto anterior disponible para el siguiente turno y el siguiente agente.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [gbrain](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gbrain.md) | Memoria a largo plazo | Guarda decisiones, relaciones y trabajo hecho como conocimiento que el siguiente turno puede consultar. |
| [llmwiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-compiler-cell-7aa3/reviews/llm-wiki-compiler.md) | Compilador de conocimiento | Compila documentos y sesiones en una wiki con fuentes, que luego consultan personas y agentes. |
| [Hindsight](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hindsight-cell-7aa3/reviews/hindsight.md) | Memoria que aprende | Convierte información nueva en hechos, experiencia y modelos mentales, y luego hace recall y reflect. |
| [ai-memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ai-memory-cell-7aa3/reviews/ai-memory.md) | Memoria entre harnesses | Reúne trazas de varios CLI de código en una sola wiki versionada con git. |
| [Octop Memory](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-memory-cell-740f/reviews/octop-memory.md) | Runtime de memoria portable | Extrae hechos, recupera un contexto que cabe en el prompt y puede mover esa memoria a otro anfitrión. |
| [LLM Wiki](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-llm-wiki-cell-17ae/reviews/llm-wiki.md) | Especificación de una idea | Se lo pasas a tu propio agente y juntos hacen crecer una base de conocimiento de un tema. |
| [MCP Memory Service](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mcp-memory-service-cell-fb52/reviews/mcp-memory-service.md) | Servicio de memoria autoalojado | Deja decisiones, observaciones y errores en un armario que la siguiente sesión y otros agentes pueden abrir. |
| [Memori](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memori-cell-fb52/reviews/memori.md) | Capa de memoria SQL | Anota quién estuvo en este turno y qué tramo de trabajo era, y mete los hechos relacionados en el siguiente contexto. |
| [Memory OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memory-os-cell-fb52/reviews/memory-os.md) | Capa de memoria de Hermes | Une archivos, chats, hechos y una wiki a Hermes, e inserta historia relevante antes de llamar al modelo. |
| [memsearch](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memsearch-cell-fb52/reviews/memsearch.md) | Memoria de proyecto | Escribe el turno en Markdown y, cuando hace falta una decisión vieja, busca solo unos pocos pasajes. |
| [Cashew](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cashew-cell-ca43/reviews/cashew.md) | Grafo de pensamiento personal | Guarda ideas y las relaciones entre ellas en un solo SQLite, para un agente que ya está en marcha. |
| [Cognee](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-cognee-cell-fb52/reviews/cognee.md) | Memoria de grafo de conocimiento | Convierte documentos, código y chats en un grafo que se puede buscar, y responde con un pasaje relevante. |
| [Daem0nMCP](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-daem0n-mcp-cell-ca43/reviews/daem0n-mcp.md) | Demonio de memoria persistente | Trae decisiones y fallos de sesiones anteriores, y se detiene antes de cambiar algo. |
| [Memlayer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memlayer-cell-ca43/reviews/memlayer.md) | Biblioteca de memoria | Se sitúa entre el modelo y el almacenamiento, y decide si esta frase se anota o se busca. |
| [Memora](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-memora-cell-fb52/reviews/memora.md) | Almacén de memoria MCP | Mete hechos, pendientes, preguntas y documentos en un mismo almacén y, al empezar, saca por tema lo que sigue vigente. |
| [MemPalace Evolve](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-mempalace-evolve-cell-ca43/reviews/mempalace-evolve.md) | Memoria local a largo plazo | Pone hechos en un directorio y los vuelve a encontrar en una conversación posterior. |
| [PocketFlow Codebase Knowledge](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-codebase-tutorial-cell-ca43/reviews/pocketflow-tutorial-codebase-knowledge.md) | Flujo para generar tutoriales | Compila un código en un tutorial de Markdown que se puede volver a leer. |
| [Youtube Made Simple](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-pocketflow-youtube-tutorial-cell-ca43/reviews/pocketflow-tutorial-youtube-made-simple.md) | Flujo para generar tutoriales | Reduce un video largo a una página en lenguaje llano. |

### Método

Dice cómo debe hacerse este trabajo: skills, procedimiento y cómo se ve lo terminado.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [gstack](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/gstack.md) | Paquete de skills | Usa roles de producto, ingeniería, diseño, QA y lanzamiento para decir cómo mirar un problema y cómo entregarlo. |
| [mattpocock skills](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/mattpocock-skills.md) | Habilidades de ingeniería | Alinea un coding agent con los requisitos y arma retroalimentación con pruebas y revisión. |
| [ECC](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-ecc-cell-90bd/reviews/ecc.md) | Procedimiento de ingeniería | Deja plan, test, implement, review y verify dentro del harness que ya usas. |
| [LifeOS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-lifeos-cell-90bd/reviews/lifeos.md) | Harness personal | Recuerda quién eres, qué te importa y cómo se ve el trabajo terminado. |
| [Hello-Agents](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-hello-agents-cell-ca43/reviews/hello-agents.md) | Tutorial | Explica, en un libro y el código de cada capítulo, cómo armar agentes desde los principios hasta aplicaciones multiagente. |

### Límite

Decide dónde se ejecuta y qué archivos, red, terminal y navegador puede tocar.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [OpenShell](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-openshell-cell-7aa3/reviews/openshell.md) | Sandbox por políticas | Usa políticas para limitar los archivos, procesos, red y credenciales que un agente puede tocar. |
| [Herdr](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-herdr-cell-7aa3/reviews/herdr.md) | Runtime de terminal | Mantiene vivos el PTY y la disposición de un agente ya existente para que una capa superior lea el estado y vuelva a conectarse. |
| [Octop Browser](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-octop-browser-cell-6a61/reviews/octop-browser.md) | Runtime de navegador | Da a un agente un Chromium real, opera la página con identificadores cortos y deja los inicios de sesión en la máquina. |
| [Cloudflare OS](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/main/reviews/cloudflare-os.md) | Banco de trabajo de permisos | Un workspace no toca cuentas externas hasta que una persona presenta el recurso. |

### Evaluación

Guarda puntuaciones, trazas e intentos comparables para juzgar cómo fue esta ejecución.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [AxisAgentic](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-axisagentic-cell-ca43/reviews/axisagentic.md) | Runtime de largo horizonte | Ejecuta tareas largas que usan herramientas y escribe cada ejecución como una traza que se puede reproducir. |
| [Harbor](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-harbor-cell-ad04/reviews/harbor.md) | Harness de evaluación | Conserva la puntuación y la traza de cada intento para compararlas, volver a calificarlas y optimizar. |

### Superficie de entrega

La capa de salida que el equipo edita junta: pantallas, documentos, hojas y diapositivas.

| Cell | Naturaleza | Contribución |
| --- | --- | --- |
| [Onlook](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-onlook-cell-90bd/reviews/onlook.md) | Lienzo de interfaz | Edita una interfaz React en la pantalla que está corriendo y escribe el cambio de vuelta al código. |
| [Univer](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-cell-80a8/reviews/univer.md) | Runtime de Office | Deja que personas y agentes operen el mismo modelo de hojas, documentos y diapositivas. |
| [Univer Workspace](https://github.com/Giorno-Giovanna-Dio/AI-Agent-Products-Reviewed/blob/cursor/add-univer-workspace-cell-80a8/reviews/univer-workspace.md) | Espacio de documentos | Personas y agentes editan hojas y documentos juntos; una persona decide si se fusiona de vuelta a la versión que todos están viendo. |

El estado de uso directo tiene dos valores:

- `untried`: está en el catálogo y todavía no se ha usado de verdad.
- `tried`: alguien lo ha usado.

<h2 id="support">Apoyar este catálogo</h2>

Este catálogo es público bajo la licencia MIT. Hay dos formas de sostenerlo:

- Añade un producto que no está, o corrige una Cell que sí está. Véase [CONTRIBUTING.md](CONTRIBUTING.md).
- Financia el mantenimiento con [GitHub Sponsors](https://github.com/sponsors/Giorno-Giovanna-Dio). El botón Sponsor de la página del repositorio lee [`.github/FUNDING.yml`](.github/FUNDING.yml).

<p align="center">
  <a href="https://github.com/sponsors/Giorno-Giovanna-Dio"><img src="https://img.shields.io/badge/Sponsor-%E2%9D%A4-ea4aaa?style=for-the-badge&logo=githubsponsors&logoColor=white" alt="Sponsor on GitHub"></a>
</p>
