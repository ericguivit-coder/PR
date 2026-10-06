# Max Tracking — contexto del proyecto

App de investigación de bachillerato. Devuelve a jóvenes deportistas de competición (unos 20 esquiadores alpinos de 14 a 20 años, algunos menores) sus propios datos de salud y recuperación, analizados por IA. Ahora mismo es un **prototipo navegable** en archivos `.dc.html`; la versión de ordenador/iPad es la que se está diseñando.

El usuario habla **castellano**. Responde en castellano, en lenguaje sencillo y sin jerga técnica.

## Hipótesis y reglas del estudio (obligatorias)

La app sirve a 4 hipótesis:
1. Ver la evolución analizada por IA permite planificar mejor que las puntuaciones sueltas de ahora.
2. Hay una diferencia entre el estado objetivo del deportista y lo que le dice al entrenador, y crece cuanto peor está.
3. Ver los datos como evolución detecta antes el cansancio acumulado.
4. Devolver los datos al deportista aumenta sus ganas de rellenar los cuestionarios.

Reglas:
- Cada deportista se compara **solo consigo mismo**: franja personal de 28 días, con un mínimo de 14 días de datos (`bandFrom`). Sin franja → «Calculando».
  - La franja es la media de esos 28 días ± 1 desviación típica, con un ancho mínimo para los datos que casi no cambian (`FLOOR`: FC 1,5 lpm, HRV 3 ms, sueño 15 min, cómo estás 0,5).
  - Qué es mejor (`DEF.worse`): la FC en reposo y la fatiga, cuanto más bajas; la HRV, el sueño y cómo estás, cuanto más altos.
- **La app no recoge datos propios.** Todo llega de Google Forms: diario, semanal (con OSTRC) y mensual (WEMWBS). Nunca hagas pantallas de formulario, previsualizaciones de preguntas ni bloques de «responde aquí». Los botones «Responder» abren el Google Form (props `formDaily` / `formWeekly` / `formMonthly`) y se desactivan si no hay enlace.
- La IA **sugiere, nunca ordena**. No diagnostica, no sustituye al entrenador y siempre dice qué datos ha usado («Basado en…», visible al pasar el ratón por el título del informe). El aviso «Orientativo, no es un diagnóstico médico…» se queda visible.
- **Nunca** aparecen en la app el **APSQ** ni el **texto libre mensual**. `hrv_semanal` nunca va a la IA.
- **No atribuyas causas** que los datos no permiten afirmar. Por ejemplo, «te notas peor que tus datos» no se etiqueta como estrés o fatiga mental: se dice la diferencia y se sugiere hablarlo con el entrenador. «Cómo estás» es una sola pregunta de 1 a 5.
- Público: jóvenes sin formación técnica. Todo tiene que entenderse sin explicación.
- Idiomas: **catalán por defecto**, más castellano, francés e inglés (Ajustes). Ningún texto sin traducir ni desbordado.
- **Salud mental** (del formulario semanal): la app solo usa el tipo y desde cuándo. Nunca muestra ni envía a la IA síntomas, causas, profesional ni días; la IA solo sabe que hay «un problema privado». En Hoy y Semana sale arriba del todo, destacado, con un texto fijo (nunca de la IA): con quién hablar y los teléfonos 024 y 112 (España). No pone los días en «Precaución».
- **Formularios sin responder:** nada de ese formulario, y nada de formularios antiguos presentado como si fuera de ahora (ni en el texto del día ni en avisos). Se enseña el último respondido con el aviso «Sin responder» + «Responder», y el que falta como hueco en discontinuo.

## La otra app: entrenadores y preparador físico

Una segunda app, para los entrenadores y **sobre todo para el preparador físico**, conectada con esta. Primero fue contexto para pensar ideas (01-10-2026); el 05-10-2026 empezó su diseño como bocetos en un lienzo, y el **06-10-2026 pasó a la versión oficial del prototipo**, en este proyecto y con archivos propios (sección siguiente).

- **Panel del preparador, versión oficial** (06-10-2026; plan en el Drive: «Panel del preparador: plan de la versión oficial (6 oct 2026)», 01 Planificació, id `14IMiTsTga3vPpoUDcp_bN1P1eEyyGGmIYJeu5FE8wL0`). El usuario decidió que viva aquí, en archivos propios y no dentro del motor del deportista.
  - **Archivos:** `Max Tracking - Preparador.dc.html` (la página que se abre) importa `Max Tracking Panel.dc.html` (el motor). Mismo estilo de código que el motor del deportista: React sin JSX, comentarios en catalán, textos en `TX` con `{ ca, es, fr, en }` y `t()`.
  - **Pantallas** (todas hechas el 06-10-2026, a partir de la versión 3 del lienzo; en el menú, Sesiones lleva lo que queda por decidir y Avisos lo que queda por revisar):
    - **Estructura:** barra lateral, barra de arriba (buscador ⌘K, hora de los datos y Todos · Grupo A · Grupo B) y el botón del chat.
      - Desde el 06-10-2026 la barra lateral se pliega, como en la app del deportista: un botón arriba la cierra y queda una columna de iconos, con el nombre al pasar el ratón.
      - Plegada, no lleva logo arriba; un punto de color avisa de lo que queda por decidir (Sesiones) y por revisar (Avisos).
      - Se recuerda (`mt-prep-side`). Sin elegir, está abierta a partir de 1180 px; por debajo de 1000 px, siempre plegada.
    - **Inicio:** estado del equipo, resumen de la IA, cuatro cifras (cada una lleva a su lista), avisos, sesiones de hoy y próximos días.
    - **Deportistas:** la lista entera con buscador, filtros (con aviso, por decidir, lesión o enfermedad, sin datos) y orden por atención o por nombre; con «Todos», separada por grupos.
    - **Ficha:** cuatro pestañas (Resumen, Sesiones, Datos de 28 días y Salud y notas) y, a la derecha, «Sesión de hoy»: la IA sugiere el ajuste y el preparador decide, con una nota, y guarda.
    - **Avisos:** por revisar, revisados y todos, con filtro por tipo. Cada uno dice qué sugirió la IA y qué se decidió (aceptada o cambiada). El historial de la semana es `HIST`. El «Ya estoy bien» del deportista lo confirma el preparador.
    - **Semana:** todo el equipo día a día, con el estado y el ajuste de cada día. A la derecha:
      - la semana hasta hoy;
      - del formulario 38, la escala que más se aleja del normal de cada uno (2 puntos o más), nunca quién no ha respondido.
    - **Sesiones:** el calendario de TrainingPeaks por grupos, solo para mirar, y la sesión elegida a la derecha.
    - **Sesión y adaptaciones:** el Excel, cómo se adapta y, si es de hoy, el ajuste de cada deportista con la sugerencia de la IA; se guardan todos a la vez. Si es pasada, cómo se ajustó; si es futura, que se ajusta ese día; si es de descanso, que no hay nada que ajustar.
    - **Experimento (H1):**
      - 8 casos anónimos (`EXP`, deportistas A–H), con el formato asignado alternando: notas, como ahora, o panel. No lo elige el preparador.
      - Se guardan la decisión, el tiempo y la seguridad de 1 a 5 (`mt-prep-exp`). Al final sale la tabla de resultados.
      - En el formato panel no sale la sugerencia de la IA (sigue por decidir).
    - **Chat «Pregunta sobre el equipo»:**
      - las preguntas sugeridas las responde la app con los datos del grupo elegido, con la fila de 7 días de cada deportista;
      - el texto libre va a la IA (`window.claude`, solo dentro del host) con códigos, nunca nombres; sin IA, responde la sugerida más parecida y lo dice;
      - no responde nada de salud mental, de lo que dijo al entrenador (H2) ni de quién no ha respondido (H4): `chatRisk`, con un texto fijo.
    - Los botones a TrainingPeaks y al Excel salen desactivados en el panel, porque falta decidir cómo se conecta con TrainingPeaks.
  - **Ajustes:**
    - modo oscuro, claro o automático (automático por defecto);
    - **cuatro estilos**, cada uno en claro y en oscuro: Azul marino (el de la versión 3, por defecto), Grafito, Negro (como la app del deportista: botón principal blanco en oscuro y negro en claro) y Pizarra suave. Salen de la página «Modo oscuro · opciones» del lienzo; el usuario pidió poder elegirlos en Ajustes;
    - idioma (catalán por defecto, castellano, francés e inglés; el preparador trabaja en francés);
    - perfil;
    - qué lee y qué no lee la IA.
  - **Datos inventados** (`ATH`, `WEEK`, `seriesOf`):
    - 20 deportistas en los grupos A y B, con código (`MT-01`…). Martí R. es `MT-07`, el mismo código que el deportista de la app; Nora G., la nueva, es `MT-19`.
    - Hoy es el jueves 24 sep, como en la app del deportista.
    - Cada uno tiene 28 días de FC, HRV y sueño, con su franja (media ± 1 desviación típica, con el mismo ancho mínimo).
    - Las sesiones de la semana llegan como de TrainingPeaks.
    - Los resúmenes de la IA son textos hechos con los datos.
  - **Reglas que aplica:** el orden es por atención, nunca por valores; la IA recibe códigos, no nombres; el preparador ve solo la matriz prudente (más abajo), y los días sin datos salen como hueco en discontinuo o ratllado.
  - **Conexión con la app del deportista:**
    - Al guardar el ajuste en la Ficha, se escribe en `localStorage` `mt-prep-aj` (por código: `{ v, note, at, sug }`). La app del deportista lo lee en Hoy y se actualiza sola si el panel está abierto en otra pestaña (evento `storage`).
    - Al guardar, el aviso queda revisado (`mt-prep-rev`).
    - Solo funciona en el mismo navegador; con servidor irá por la base de datos.
  - **Estado persistente del panel:** `mt-prep-style`, `mt-prep-mode`, `mt-prep-lang`, `mt-prep-grp`, `mt-prep-side`, `mt-prep-aj`, `mt-prep-rev`, `mt-prep-exp` y `mt-prep-proto-hidden`. La conversación del chat no se guarda.
  - **Botón del prototipo** (matraz, solo en castellano, tecla E, a la izquierda del chat): abrir la app del deportista, borrar los ajustes y avisos revisados y reiniciar el experimento, para repetir la demostración. En la app del deportista, el matraz tiene «Abrir el panel del preparador».
- **Bocetos** (lienzo «Panel del preparador · bocetos», https://claude.ai/artifact/HZX7TJ7SXXvTSqSwATbtdy). Usan 20 deportistas inventados (grupos A y B), el estilo Sencillo y la fecha de hoy del prototipo (jueves 24 sep).
  - La **primera versión** (lista de los 20, tarjetas por estado, fichas A/B) no le gustó al usuario. Pidió algo más profesional, que informe más que actúe, fácil de navegar con muchos deportistas y sin cargar la pantalla de deportistas. Se borró del lienzo.
  - La **segunda versión** (05-10-2026) se inspira en la estructura de un «centro de control» que pasó el usuario (sin copiarlo, sin etiquetas en mayúsculas ni leyendas). Cada pantalla está en claro y en oscuro. Falta su opinión:
    - **Barra lateral:** Inicio, Deportistas, Avisos y Semana, y un buscador de deportistas. Arriba de cada página, Todos · Grupo A · Grupo B.
    - **Inicio:** estado del equipo (cuántos en cada estado hoy y en los últimos 7 días) y, al lado, el resumen del día de la IA. Cuatro cifras: avisos por revisar, ajustes cambiados, lesión o enfermedad y datos de hoy. Avisos, ajustes de hoy y próximos días. La lista del equipo no sale en el inicio.
    - **Chat «Pregunta sobre el equipo»:** botón redondo en todas las pantallas, como en la app del deportista.
    - **Deportistas:** la lista completa, con buscador y filtros, ordenada por atención o por nombre y separada por grupos.
    - **Ficha:** el aviso, los 7 días con el ajuste de cada día, el resumen de la IA, el ajuste con la sugerencia de la IA y los gráficos de 28 días plegados.
  - La **tercera versión** (06-10-2026) está en la página «Versión 3 · blanco y azul» del lienzo, que se abre por defecto; la segunda queda en su propia página, como pidió el usuario. Le pareció que la segunda «no está mal», pero pidió:
    - revisar en el Drive lo que hay sobre el preparador;
    - que pueda poner **sesiones de entrenamiento adaptadas a cada deportista**;
    - un estilo más afilado, limpio y compacto, en blanco y azul, con modo oscuro;
    - diseñar las secciones que faltan.

    Las pantallas, cada una en claro y en oscuro: Inicio (con «Sesiones de hoy»), chat, **Sesiones** (calendario de la semana por grupos), **Sesión y adaptaciones**, Deportistas, Ficha con pestañas, **Avisos** (pendientes e historial), **Semana** (equipo día a día), **Modo experimento** (H1) y cómo lo ve el deportista en Hoy. Falta su opinión.
- **Sesiones con TrainingPeaks** (06-10-2026; en el lienzo, página de la versión 3: Sesiones, Sesión y adaptaciones, Ficha y «Así lo ve el deportista»):
  - **El preparador planifica en TrainingPeaks.** Cada sesión lleva un enlace a un Excel con las instrucciones y los vídeos. Ese Excel no está en el Drive del usuario. El panel **no crea ni edita sesiones**: las lee de TrainingPeaks, y el calendario de Sesiones es solo para mirar.
  - En el panel solo se decide el **ajuste de cada deportista** sobre esa sesión (el Excel es el mismo para todo el grupo):
    - Normal: como en el Excel;
    - Reducida: qué cambia (una serie menos, intensidad máxima, sin saltos…);
    - Alternativa: qué hace en su lugar, con otro Excel si hace falta;
    - Descanso: hoy no entrena.

    La IA sugiere el ajuste y el preparador decide (H1).
  - **El deportista** ve en Hoy («Propuestas del preparador físico») la sesión del día, su ajuste, lo que cambia hoy y la nota. Lleva dos botones que le llevan a la sesión: «Instrucciones y vídeos» abre el Excel y «Ver en TrainingPeaks» la abre allí. Los ejercicios no se copian en la app. Si falta el enlace, el botón sale desactivado, como los de los formularios.
  - **Por decidir con el preparador:**
    - cómo llegan las sesiones a la app: la conexión directa con TrainingPeaks (hay que confirmar si la dan), el calendario que exporta TrainingPeaks o pegar el enlace a mano;
    - dónde mira el deportista la sesión: si es en TrainingPeaks, el ajuste también tendría que llegar allí;
    - si el Excel es uno por sesión o uno por semana.
  - El **planificador propio** de la versión 3 (crear sesiones con tres versiones y los ejercicios con sus cargas) está aparcado en la página «Planificador propio · aparcado» del lienzo. No lo retomes si el usuario no lo pide.
  - Otras hojas del preparador en el Drive, compartidas con el usuario:
    - «CALCUL RM»: una pestaña por deportista, con su 1RM y los kilos para cada porcentaje;
    - «Explication RPE / RIR / POURCENTAGE»: la tabla de RPE, repeticiones en reserva y porcentajes.

    Sirven para hablar su idioma en los ajustes (por ejemplo, «RPE máximo 7»).
- **Decisiones del 05-10-2026 sobre el panel** (segunda ronda de preguntas):
  - **Avisos: se revisan y salen.** Cada aviso dice qué pasa, desde cuándo y qué ajuste sugiere la IA. Sale de la lista al marcarlo como revisado o al guardar el ajuste en la ficha, y queda en el historial.
  - **Qué es un aviso:** lesión o enfermedad en curso; varios días seguidos en Recuperación (H3); un dato 2 días o más fuera de su franja; la vuelta de una lesión; 2 días o más sin datos de la pulsera; un problema nuevo en el formulario. Nunca: salud mental, quién no responde (H4) ni lo que dijo al entrenador (H2).
  - **Navegación:** la lista completa en su propia página, con buscador; el inicio queda limpio.
  - **IA:** resumen diario del equipo (por grupo), chat sobre el equipo y **sugerir el ajuste**. La IA sugiere y explica por qué; el preparador acepta o elige otro. Se guarda si aceptó la sugerencia o la cambió, así H1 mide también a la IA.
  - Reglas que siguen: el orden es por lo que pide atención, nunca por valores; la IA recibe códigos y la app pone los nombres; lo pendiente de Oriol, en discontinuo.

- **Funciones que conectarán las dos apps** (se irán añadiendo más poco a poco):
  - **Lo que manda el preparador** (decidido el 05-10-2026). Llega desde su app, no de Google Forms.
    - Lo principal es el **ajuste del día**: Normal, Reducido, Descanso o Alternativo, con una nota si quiere. El deportista lo ve en Hoy. Es lo que mide H1, porque deja registrada cada decisión.
    - Desde el 06-10-2026 sale en «Ideas para recuperarte» › «Propuestas del preparador físico» (`prepSesEl`), con la sesión del día de TrainingPeaks. Los ejercicios no se mandan: están en el Excel de la sesión.
  - **Lesiones compartidas:** si el deportista la comparte, el preparador sigue la lesión (el Seguimiento de Hoy, con «¿Cómo va hoy?» y «Ya estoy bien») y guía la vuelta a entrenar.
  - **Avisos al entrenador:** cuando un deportista acumula cansancio (H3) o marca un problema nuevo en el formulario semanal.
- **Privacidad: lo elige el deportista.** Cada deportista decide en Privacidad qué comparte con el entrenador y el preparador. **La salud mental no se comparte nunca.** Falta decidir qué se comparte si el deportista no toca nada. La tabla que hay ahora en Privacidad (`pagePriv`) dice otra cosa (datos diarios y «lo que le dices», sí; formularios e informes de la IA, no). El panel ya existe, así que hay que cambiarla para que diga lo que ve el preparador. Falta que Oriol valide la matriz; pregunta al usuario antes de tocarla.
- **Qué ve el preparador mientras Oriol no valide la matriz** (decidido el 05-10-2026). Se usa el borrador del «Plan de los paneles» (§4), que es lo más prudente:
  - **Sí ve:** los datos físicos, el estado del día, rendimiento, fatiga y descanso, y la participación y el tipo de problema (lesión o enfermedad).
  - **No ve:** lo que el deportista dijo al entrenador, la diferencia entre eso y sus datos, las palabras y el comentario, el bienestar mensual, la salud mental ni quién no ha respondido.
  - En los bocetos, lo pendiente de Oriol sale marcado.
- **En el estudio sirve a H1 y H3:** el entrenador planifica con la evolución analizada por la IA, no con puntuaciones sueltas (H1), y recibe los avisos de cansancio acumulado (H3).
- **Cuidado con H2 y H4.** Si el entrenador ve los datos, el deportista puede cambiar lo que le cuenta (H2). Si ve quién no ha respondido y le insiste, ya no se sabe si el deportista rellena más por la app o por el entrenador (H4). Cuando propongas algo que vea el entrenador, di si puede tocar H2 o H4.
- Mientras no se decida otra cosa, las reglas del estudio de arriba también valen para la otra app: la IA sugiere y no diagnostica, no se atribuyen causas, y nunca salen el APSQ ni el texto libre mensual.
- **Para qué sirve este contexto:** al pensar ideas para la app del deportista, mira si tienen otra mitad en la del preparador (algo que manda, que ve o que le avisa) y dilo al proponerlas.

## Archivos

| Archivo | Qué es |
|---|---|
| `Max Tracking - Ordenador.dc.html` | Página que abre el usuario para ordenador/iPad. Solo importa el motor y pasa las props (`athleteName`, `aiLive`, `formDaily`, `formWeekly`, `formMonthly`, y `sessionExcel` / `sessionTP`: los enlaces de la sesión del día). |
| `Max Tracking - Preparador.dc.html` | Página del **panel del preparador físico** (ordenador/iPad). Solo importa su motor. |
| `Max Tracking Panel.dc.html` | **El motor del panel del preparador** (desde el 06-10-2026; ver «La otra app»). |
| `Max Tracking Escritorio.dc.html` | **El motor de ordenador/iPad (~575 KB).** Casi todo el trabajo se hace aquí. |
| `Max Tracking - Móvil.dc.html` + `Max Tracking.dc.html` | Versión móvil con el diseño y el modelo de datos **antiguos**. Se portará más adelante al motor nuevo; no la toques salvo que lo pida. |
| `support.js` | Runtime de los `.dc.html` (no editar). |
| `ios-frame.jsx` | Marco de iPhone que usa la versión móvil antigua. |
| `uploads/` | Capturas antiguas. |

- Hay repo git y el usuario hace sus commits. **No hagas commit si no te lo pide.**
- **Para verlo:** sirve la carpeta con `python3 -m http.server <puerto> --bind 127.0.0.1` y abre `http://127.0.0.1:<puerto>/Max%20Tracking%20-%20Ordenador.dc.html`. Si el usuario no ve un cambio, casi siempre es la caché: pídele una recarga forzada (Cmd+Shift+R; en Safari, Cmd+Option+R). Los `localStorage` son por dirección, así que en un puerto nuevo sale en catalán y con el estilo por defecto.
- **Comprobar sintaxis:** extrae el `<script>` más largo del motor y pásale `node --check`.
- **Documentos de los formularios, en el Drive del usuario.** Se leen con el conector de Google Drive.
  - «Formulari de rendiment i salut de l'esportista 2026-2027 — Preguntas y variables» (en Mi unidad): las 58 preguntas del semanal, con sus opciones, saltos y variables `entry.NNN`.
  - «Formulari de rendiment i salut de l'esportista 2026-2027 — Cómo lo usa la app» (carpeta «PR - IA i seguiment d'esportistes › 03 Metodologia», versión 2 del 03-10-2026, id `1JnmmVaZMtUI4xUSMi3ZgpB2m5IiPxE4dDffCafGH-_c`; la versión 1 se conserva con «sustituida» en el título). Contiene:
    - qué mide cada pregunta y qué hace la app con ella: dónde sale, si va a la IA o si no se usa nunca;
    - los datos que se recogen y no se usan, como el ciclo menstrual;
    - lo que hay que revisar en el formulario.

    **Si cambias cómo usa la app una pregunta del semanal, díselo al usuario.** Con el conector no se puede editar un Google Doc, así que hay que crear una versión nueva en la misma carpeta.
  - «Formulari de benestar i salut mental 2026-2027 — estructura» (en Mi unidad): el mensual, con SWEMWBS (7 ítems), APSQ (10 ítems, nunca en la app) y el texto libre.
- **Planificación, en «PR - IA i seguiment d'esportistes › 01 Planificació»:**
  - «App del deportista (Max Tracking): qué enseña y cómo funciona · versión 2»: la referencia de la app. Regla del usuario: si quitas o añades un bloque, se cambia primero en el plan y después en el código.
  - «Pantalla Mes: plan de diseño (2 oct 2026)»: sustituye a la sección 8 (Mes) de la versión 2.
  - «App del deportista: en qué punto estamos (análisis, 2 oct 2026)»: el plan comparado con el código, con la lista de pendientes.
  - «Plan de los paneles: preparador físico y especialista»: decisiones D1–D7, qué ve cada rol y fases.
  - «Decisiones de diseño (5 oct 2026): D4, D5, chat y panel del preparador»: D4 a medias, D5, el chat, el diseño congelado y qué manda y qué ve el preparador. Si no coincide con los dos documentos de arriba, manda este.

## Cómo está hecho el motor (`Max Tracking Escritorio.dc.html`)

- Una clase `Component` de React sin JSX: `h = React.createElement`. Los comentarios del código están en **catalán**; escribe los nuevos igual.
- **Textos:** diccionario `TX` con hojas `{ ca, es, fr, en }`. `L()` lo resuelve al idioma activo y `t(clave, ...args)` lee `TX.UI` (las entradas con datos son funciones). **Todo texto visible, en los 4 idiomas.** El panel del matraz (prototipo) va solo en castellano. Cuando dejes de usar una clave, bórrala.
- **Días:** índice `i`, con `day(i)`, `dShort`, `dLong`, `dFull`. `0` = viernes 14 ago 2026; **`41` = hoy (jueves 24 sep 2026)**. Semana 38 = índices 31–37; semana 39 (en curso) = 38–44. El formulario semanal se envía el domingo (`a + 6`) y el mensual el día 31 ago (índice 17); el siguiente mensual es el 30 sep (índice 47). `formState(sendDay, done)` → `future` / `new` (ámbar, día de envío y el siguiente) / `late` (rojo, desde el 2.º día) / `done`.
- **Datos:** `BASE` + `TAILS[escenario]` → `data(sc)` devuelve `{ vals, coach, band, pos, feel, obj, st, nBand, nData }`.
  - Datos diarios: `rhr` (FC en reposo), `hrv`, `son` (sueño), `estat` (cómo estás, 1–5) y `coach` (lo que le dijo al entrenador: `be` / `reg` / `mal` / `res`).
  - `WEEKS`: el formulario semanal real (Google Form 2026-2027; preguntas y variables en el documento de Drive «Formulari de rendiment i salut… Preguntas y variables»). Contiene:
    - 5 escalas de 1 a 10, con `fatiga` invertida;
    - 3 palabras;
    - el comentario (la pregunta 9 explica la nota de Organización);
    - `ostrc` 0–3 + `inici` (participación y «¿Desde qué día?» del primer problema);
    - `pbs`: hasta 2 problemas, el peor primero. Cada uno con `t` = `les` (lesión, `z` = 1–2 de las 17 zonas), `mal` (enfermedad, `sis` = sistema) o `men` (salud mental, solo el tipo); `nou` (`nova` / `rec` / `pit` / `cro`); y `mod` / `perf` / `sym`, que solo van a la IA. El segundo lleva su `ost` y su `inici`. Diccionario `PB`; helpers `pbList`, `pbWhat`, `pbInfo`, `pbSame`, `pbEnd`, `pbPrev`;
    - `hrvSet`.

    Se sobrescribe por escenario con `WEEK_SC`, y el matraz cambia el problema de la semana 38 con `PB_SIM`. `MONTHS` = WEMWBS, 7 ítems.
  - **Problemas de salud, un solo modelo para Hoy, Semana y la IA** (`healthOne` / `healthList` / `physList` / `menOf`). El final es mixto:
    - una **enfermedad** termina cuando los datos vuelven a tu normal (`episode`, 2+ días);
    - una **lesión**, la **salud mental** o una enfermedad que no sacó los datos de tu normal terminan cuando un formulario posterior ya no la marca;
    - `next` = el próximo formulario, que es cuando se revisa.
- **Escenarios** (`SCN`, matraz): `normal` Día normal · `descans` Fatiga · `salut` Problema de salud · `disc` Discrepancia · `gran` Gran diferencia · `estres` Te notas peor · `buit` Sin datos · `nou` Deportista nuevo. Cambiar de escenario tiene que enseñar diseños distintos en todas las páginas.
- **Escala de color:**
  - `qOf` (distancia a la franja), `hOf` / `lvlOf` (`ok` / `mid` / `bad`) y `status()`.
  - `tone(h)` para texto, líneas, puntos y barras; `toneF(h)` para rellenos grandes (anillos, celdas, bloques de estado); `sTone(v, min, max, inv)` para los cuestionarios (curva `SQ`).
  - `dayState(sc, i)` (`fons` Óptimo / `mod` Moderado / `rec` Recuperación / `prec` Precaución) y `dayTone`.
- **Estilos:** `THEMES` (`gpt` por defecto, que en Ajustes se llama «Senzill / Sencillo / Simple / Simple» desde el 03-10-2026, nunca «ChatGPT»; más `hibrid`, `editorial` y `grafit`) × modo oscuro/claro/automático (**automático por defecto**, sigue el sistema), todo con tokens `C.*` y `G.*`. Nunca pongas colores fijos (`#000`, etc.).
  - `C.well` para paneles de gráfico dentro de tarjetas.
  - `alertC(col, a)` para cualquier aviso (en claro, más marcado).
  - `G.gpt` cambia la estructura: con Sencillo, `sideEl` + `topEl` + `reportChat`; con el resto, `railIcons` + `reportBox`. La barra lateral plegada no lleva el logo «MT» (redondo, parecía un segundo perfil): arriba solo el botón de abrir y abajo el perfil.
  - `railIcons` (Híbrid, Editorial, Grafit; desde el 05-10-2026): barra de 60 px **solo con iconos** (el nombre, al pasar el ratón), sin el logo «MT». El botón de modo claro/oscuro lleva el icono `contrast`, porque el sol se confundía con Hoy.
- **IA:** `fetchAI` → `window.claude.complete`, con respuesta en JSON y caché en `localStorage` (`mt-desk-ai-v14`; súbele la versión si cambian los datos o los prompts).
  - Prompts: `promptDaily`, `promptWeek` (tipo `week2` en Semana), `promptMonth2`, `promptTrend`.
  - **Nada personal** (desde el 03-10-2026): ningún informe envía el nombre, la edad ni el sexo (plantillas `head`, `wMain`, `tMain` y `m2Main`).
  - El informe de la semana solo pide el titular y las claves (`jsonW`), comparando con su normal, nunca con la semana anterior (no la recibe). Para `week2` el lector ya no exige «accions».
  - Textos de reserva generados con datos: `fbDaily`, `fbWeek`. Nunca se pide un informe de un formulario sin responder (`ensureAI`).
- **Piezas útiles:**
  - `box({ title, right, id, st }, …)` / `secHead`: título fuera de la tarjeta.
  - `missChip`: dato que falta, en discontinuo.
  - `flashTo(id)`: baja al bloque y lo ilumina en ámbar.
  - `goWeek(wi, target)`: -1 = el último formulario respondido; destinos `form`, `q-<escala>`, `ost`, `vs`.
  - `goMetric(k)` / `goDays()`: van a Evolución › Día a día.
  - `lastWi()` / `mLast()`: último formulario semanal / mensual respondido (con «Simular cuestionarios sin responder», el anterior).
  - `stack()`: cada bloque queda por encima del siguiente (zIndex), así los desplegables no quedan tapados.
  - `formBtn(url, …)`, `stateMark`, `markWord`.
- **Tàctil (iPad, desde el 04-10-2026).** Con ratón no cambia nada.
  - Lo que con ratón sale al pasar por encima, con el dedo sale al tocar. Lo resuelven `tapDown` / `tapUp` / `tapClick` y `tapTipEl`, a nivel de documento:
    - el `title` de un elemento sale como globo;
    - «Basado en…» (`.mt-bs[data-open]`) se abre al tocar.
  - Un `title` en un botón es su nombre y no sale como globo.
  - Si un elemento con `title` también hace algo (`data-act`, como los días de «Esta semana»), el primer toque enseña el detalle y el segundo hace la acción. Tocar fuera lo cierra.
  - Gráficos: `...this.ptr(pick, clear)` sustituye a `onPointerMove` / `onPointerLeave` (el detalle se queda al levantar el dedo), y `this.useTapAway(ref, clear)` lo cierra al tocar fuera. Úsalos en los gráficos nuevos.
  - Los `:hover` del CSS van dentro de `@media(hover:hover)`, porque en un iPad táctil se quedan enganchados.
- **Estado persistente (`localStorage`):** `mt-lang`, `mt-desk-theme-v4`, `mt-desk-mode-v2` (claves renovadas el 03-10-2026 para que todos empiecen en Sencillo + Automático), `mt-desk-side`, `mt-desk-done`, `mt-desk-trk` (respuestas de «¿Cómo va hoy?» y «Ya estoy bien», una por problema), `mt-desk-ai-v14`, `mt-proto-hidden`, `mt-desk-chatw` (ancho de la vista general del chat; la conversación nunca se guarda). Lee además `mt-prep-aj`, el ajuste que guarda el panel del preparador (no lo escribe nunca).
- **Matraz** (`protoEl`, tecla E). Es temporal; se quitará cuando acabe la fase de prototipo. Contiene:
  - el escenario;
  - «Salud en el formulario (semana 38)» (`pbSim`: Escenario / Lesión / Mental / Dos);
  - «Bienestar en el formulario (mes)» (`wemSim`: Escenario / Bajada / Zona baja);
  - «Simular cuestionarios sin responder» (`simOverdue`);
  - abrir la versión móvil y ocultar el botón.

## Páginas (estado a 30-09-2026)

**Hoy** (`pageAvui`), de arriba abajo:
1. Salud mental (`wellBlockEl`): si el último formulario semanal la marca en curso, sale el bloque `menBlockEl`, el mismo de Semana. Si no, y el último formulario mensual está en zona baja (≤ 19), el bloque de ayuda del Mes (`wemSupHoy`). Nunca los dos.
2. Informe del día:
   - A la izquierda, el veredicto con la palabra del estado y 4 anillos (`RingsV`). Cada anillo se llena según dónde cae el dato respecto a tu normal (más lleno = mejor) y lleva debajo un texto en dos líneas: la dirección en blanco y el nivel con el color del anillo.
   - A la derecha, el titular y el párrafo de la IA, cortado a unas 4 líneas con «Seguir leyendo» (`ClampBox`).
   - Debajo, «Tus cuestionarios» (`formsSummaryEl`): dos tarjetas negras alineadas (subgrid) con las notas en barras de color, sin ▲▼. Al pie, «Próximo…» o el aviso «Sin responder · Semana 38» con «Responder».
3. Seguimiento (`trackingEl`), si el último formulario semanal marca un problema físico, o fatiga ≥ 7 o salud ≤ 4. La salud mental no abre seguimiento.
   - **Con dos problemas**, arriba se elige cuál (`trkSel`) y todo lo de debajo es solo de ese:
     - nombre, «Ha vuelto…» y estado (lo mismo que en Semana);
     - «Revisión: domingo 27 · formulario semanal»;
     - sus señales desde que empezó y su mensaje (una lesión nunca «se cura» por los datos);
     - «¿Cómo va hoy?» y «Ya estoy bien»;
     - la curva, solo de este problema. La línea discontinua de la vez anterior («Anterior (S37)») se quitó el 05-10-2026 porque no aportaba nada.
   - Si todos han terminado, sale una línea «Terminó en N días», solo si terminó en los últimos 7 días.
   - «Precaución» solo mientras hay un problema físico en curso.
4. «Ideas para recuperarte» (`recoveryPlanEl`): a la izquierda, los hábitos que propone la IA. A la derecha, desde el 06-10-2026, «Propuestas del preparador físico» (`prepSesEl`), que sustituye al hueco «Próximamente»:
   - la sesión del día tal como llega de TrainingPeaks (`TX.PREP.ses`, por grupo: MT-07 es del A y MT-19 del B);
   - el ajuste que guardó el preparador en su panel (`mt-prep-aj`), con «Ajustada según cómo llegas», «Lo que cambia hoy» (`TX.PREP.chg`, en segunda persona; el panel enseña las mismas frases) y la nota;
   - dos botones que llevan a la sesión: «Instrucciones y vídeos» (prop `sessionExcel`; con Descanso no sale) y «Ver en TrainingPeaks» (prop `sessionTP`). Sin enlace salen desactivados, como los de los formularios, y se apilan si no caben.

   Sin ajuste guardado, sale la sesión tal cual. Arriba a la derecha va la hora del ajuste.
5. Franja «Tú vs tu cuerpo» (`feelStripEl`, id `mt-feel`). Para ir a Evolución está el enlace «Verlo en Evolución ›». Con el dedo, el gráfico sirve para mirar: un toque enseña el día y no cambia de página. Con ratón, el clic en el gráfico sigue llevando a Evolución.
6. «Esta semana» (`weekNowEl`): nombres de los datos a la izquierda y 7 tarjetas de día negras con el borde del color de su estado, la palabra del estado, 4 barras que se llenan según el estado y la palabra del entrenador. Los días que faltan van en discontinuo. Clic en un día → Evolución › Día a día; «Ver semana» → Semana.
7. Notificación flotante del entrenador (`coachToastEl`), solo si lo que dijo al entrenador no cuadra con el estado de sus datos. Entra deslizándose por la derecha, sigue al hacer scroll y tiene ✕. Al tocarla baja a `mt-feel`.

**Semana** (`pageSetmana`, oficial desde el 30-09-2026; la antigua con anillos, «Salud y participación», «Los 7 días» y el mapa de semanas se borró).
- Centrada en el feedback del formulario semanal. Se abre en el **último formulario respondido**. La semana en curso solo sale bajo el título: «Próximo formulario…», o el aviso con «Responder».
- Arriba a la derecha, `weekPickEl`: flechas ‹ › y un desplegable con todas las semanas de la temporada (fechas y 3 palabras). La que está sin responder sale en discontinuo, con «Responder».
- **Estructura desde el 05-10-2026** (lienzo «Semana y Mes · boceto de estructura», https://claude.ai/artifact/FHtT2Qv97ncDDcVZQnyoh9; antes, versión de prueba en el matraz). Los datos, los textos y los bloques no cambiaron: solo cómo se colocan. Sin avisos, la página se acorta. De arriba abajo:
  1. Salud mental (`menBlockEl`, solo si hay). Es la versión **compacta** (elegida el 01-10-2026): tarjeta ámbar con desde cuándo, una frase, con quién hablar en tres botones (el detalle al pasar el ratón) y los teléfonos 024 y 112 en una línea. Para verla: matraz › «Salud en el formulario» › Mental.
  2. El titular de la IA («Basado en…» al pasar el ratón).
  3. Bloque principal (`wtTopEl`), en una tarjeta partida en dos:
     - a la izquierda, «Tu semana»: las 3 palabras en grande (clic → su fila) y la barra «Estado de los días · Óptimo 6 de 7 días» (la misma cifra que la pareja Salud);
     - a la derecha, «Lo más importante» (`wtMain`): el problema de salud en curso o la primera clave que hay que vigilar (`wkKeys`). Si no hay nada, «Nada que vigilar esta semana»;
     - debajo, «Orientativo…».
  4. Cinco cifras (`wtTilesEl`): tus notas frente a tu normal (`normOf`, `vsNorm`). Solo se tiñe de ámbar la que va peor. Clic → su fila. En una fila si caben (ancho ≥ 800 px); si no (iPad), 3 arriba y 2 abajo. «Menos que tu normal» pasa a dos líneas antes que salirse.
  5. Dos columnas (`wtColsEl`):
     - a la izquierda, «Para tener en cuenta» (o «Lo que ha ido bien», si nada va mal) con el resto de las claves. Cada fila lleva › en un círculo (`goCue`, lo mismo en «Lo más importante»): se mueve dos veces hacia la derecha cuando la lista aparece en pantalla y, con ratón, al pasar por la fila;
     - a la derecha, «Semana a semana» en puntos (`stepEl`, con el color de `weekEval`; clic → esa semana; la que falta y la próxima, en discontinuo). Con una lesión en curso, debajo, «Revisión · domingo 27».
  6. «El detalle», plegado (`wtFoldEl`):
     - cada fila lleva «Ver» y una flecha en un círculo (`foldCue`), y se abre deslizándose (`foldBody`);
     - la flecha baja y sube dos veces cuando la lista aparece en pantalla (`seenRef` pone `data-seen`), no al cargar la página;
     - con ratón, al pasar por la fila, el círculo se ilumina y baja un poco. Lo mismo en «Ver las 7» de Mes;
     - dentro van los bloques de siempre, sin su título ni su tarjeta (`bare`);
     - `flashTo` abre la sección antes de ir a ella (`dSec`, `dOpen`), así los enlaces de Hoy, de las cifras y de las claves siguen funcionando.

     Las secciones del detalle:
  - «Salud», solo lesión o enfermedad (`weekHealthF` → `healthDays`):
     - nombre + «Nueva / Ha vuelto / Ha empeorado / Crónica» + estado;
     - «2.ª vez esta temporada · la anterior duró 5 días» con enlace;
     - una **fila continua** de fichas por día. Con dos problemas comparten fechas; los días sin registro van en discontinuo;
     - la ficha «Revisión · Formulario» del próximo formulario (en rojo si está sin responder);
     - «Ir al seguimiento» → Hoy, con ese problema elegido.
  - «Tus respuestas» (solo esta semana): «más / menos / como tu normal», con la media de las 4 anteriores (`normOf`) y una marca blanca en la barra. Al final, «Participación» con el texto largo (`OST`: «Completa, con problemas»…).
  - «Lo que dijiste vs tus datos» (`wkVsEl`), diseño **«Cara a cara»** (elegido el 03-10-2026 entre las dos barras de antes, una escala y la diferencia: las barras no dejaban entender la comparación). Una tarjeta negra por pareja: las dos partes con las mismas palabras (Bien / Regular / Mal, los niveles del veredicto, `vsLvl`), una al lado de la otra con = / ≠ en medio (≈ si coinciden a un nivel de distancia), y la cifra debajo de cada palabra; los días sin registro se avisan. El entrenador va día a día (`vsDaysEl` / `coachDays`): lo que le dijiste arriba y el estado de tus datos abajo, con los días que no cuadran enmarcados en ámbar y los que no se comparan (Precaución, «No le he dicho nada») atenuados. **Sin frase debajo** (la del entrenador se quitó: «sobrecarga y no aporta»). Las parejas: fatiga ↔ FC y HRV, descanso ↔ sueño, salud ↔ estado de los días, entrenador. Desde el 03-10-2026 (análisis de Semana frente al plan v2):
     - el estado de los días cuenta con `dayState`: un día en «Precaución» no es «Óptimo», como en Hoy;
     - si ese formulario marca un problema de salud (lesión, enfermedad o salud mental), la pareja Salud no da veredicto: «Marcaste un problema de salud» (`vsPb`), y no cuenta como diferencia en la clave «Tú vs tus datos»;
     - entrenador: «No cuadra» a partir de 2 días, igual en la clave y en la tarjeta; con 1 día, «Cuadra» (el día sigue enmarcado).
  - El titular de reserva (`fbWeek`) solo cuenta lesiones y enfermedades (`physList`): con salud mental no dice «con un problema de salud en curso».
  - «Tus respuestas · semana a semana» (`WeekAnswersF` con `mode: 'panel'`): solo para mirar, no cambia la página. La semana sin responder es una columna en discontinuo.

**Evolución** (`pageEvol`). Repasada con el criterio de Semana el 04-10-2026. Desde el 05-10-2026 tiene la misma estructura que Semana y Mes: el usuario la pidió directamente en producción, sin pasar por el matraz (lienzo, fila «Evolución»). Los datos, la IA y los gráficos no cambiaron. De arriba abajo:
1. **Cabecera:** el periodo («28 ago – 24 sep») y 7 días · 28 días · 6 semanas.
2. **Lectura de tendencias** (`evTopEl`): «Lectura de tendencias · Análisis redactado por IA», el titular («Basado en…») y el resumen.
3. **Bloque principal**, una tarjeta partida en dos:
   - a la izquierda, «Tus días»: una casilla por día, del color de su estado. Sin registro, en discontinuo; con datos pero sin franja (deportista nuevo), gris liso. Debajo, la barra «Estado de los días · Óptimo N de N»;
   - a la derecha, «Lo más importante» (`evMain`), con el número de su hallazgo y «Los próximos días». El orden es: un dato con días seguidos fuera de tu normal (`m: 'run'`), los días sin registro, la sensación y los otros cambios. Sin nada que vigilar, «Nada que vigilar estas semanas».
4. **Cuatro cifras** (`evTilesEl`): la media de los últimos 7 días de cada dato frente a las 3 semanas anteriores (sube, baja o como antes, con la misma regla que `fbTrend`). Cada una lleva un minigráfico del periodo con tu franja (`sparkEl` → `Spark`), en curva redondeada y al ancho real de la tarjeta (05-10-2026): con 7 días, la curva del gráfico grande; con 28 días o 6 semanas, `basisPath`, que redondea las puntas. Solo se tiñe de ámbar la que va peor. Clic → su fila (`goMetric`).
5. **Dos columnas** (`evColsEl`):
   - «Para tener en cuenta» (o «Lo que ha ido bien»), con el resto de hallazgos, su número y la flecha › animada (`goCue`). Clic → fija el número en los gráficos y baja (`evGo`);
   - «Tú vs tus datos» (`trendLeftEl(sc, R, true)`), con «Ver el gráfico».
6. **Los gráficos, abiertos:** «Día a día» y «Cómo te sientes vs qué dicen los datos».
- **Deportista nuevo** (sin 14 días de datos, sin franja ni tendencias): solo la cabecera y el aviso en discontinuo «Tu evolución estará lista el 3 de octubre» (`emptyEvoF`), como Mes. La fecha es hoy más los días que faltan, en el idioma activo (`Intl.DateTimeFormat`).
- **Las dos columnas** (aquí, en Semana y en Mes) tienen la misma altura (`alignItems: 'stretch'`). La tarjeta más baja se estira y sus filas se reparten el espacio, así no queda un hueco debajo. Los puntos de «Semana a semana» y «Mes a mes» quedan centrados.
- La reserva sin IA es `fbTrend`: se hace con los datos (sensación vs datos; FC, HRV y sueño de esta semana frente a las 3 anteriores; días sin registro). `FB_TREND` solo queda para «Deportista nuevo».
- «Día a día» (`LineMulti`, un panel por dato con su color): en la HRV hay **una sola línea**, la media de 7 días, con la etiqueta directa «Media de 7 días». Desde el 05-10-2026 no lleva puntos por día: quedaban sueltos y el usuario tampoco quiso una segunda línea fina. El valor de cada día sale al pasar el ratón. El punto de «Hoy» y el del ratón van sobre la línea (`onLine`), y la etiqueta del final dice el valor de hoy (con Fatiga, 49 aunque la media esté cerca de 61). El estado de cada dato va con su color y la diferencia con ayer en blanco.
  - Con más de 14 días (28 días, 6 semanas), el eje pone el mes en cada etiqueta («31 ago», «7 set»), también en «Cómo te sientes…». Una etiqueta que pisaría a otra no sale (`lineAxisX`).
  - Los paneles crecen con el ancho, de 96 a 140 px.
- «Cómo te sientes vs qué dicen los datos» (`LineSents`, dos líneas con la diferencia en ámbar): sin leyenda.
- Días sin registro en los dos gráficos: franja rayada suave y línea de puntos entre los dos lados (`gapZones` / `gapDecor`). Las otras variantes de gráficos y la página «Opciones de gráficos» se borraron el 04-10-2026. Mejor / Tu normal / Peor a la izquierda y «Tú» / «Tu cuerpo» al final de las líneas, como en Hoy.
- No hay puntos que laten en ninguna página: `pingEl` no dibuja nada.

**Privacidad**, **Ajustes**: existen. Ya no nombran el APSQ (04-10-2026).

**Mes** (`pageMes`, oficial desde el 02-10-2026; el Mes antiguo, con el APSQ, el texto libre y el informe en tarjetas, se borró). Hecho con el plan del Drive «Pantalla Mes: plan de diseño (2 oct 2026)», que sustituye a la sección 8 del plan v2. Maquetas: https://claude.ai/artifact/JvG3jDXCxxJwRjd5a6tWQk. Se abre en el último mes respondido: el que falta sale en discontinuo, con el aviso «Sin responder» (`monthPendEl`).
- **Solo habla del formulario mensual:** las 7 frases de bienestar (escala corta de Warwick-Edinburgh, 1–5, total 7–35). El APSQ y el texto libre nunca llegan a la app ni a la IA. «Tú vs tu cuerpo» y lo del entrenador se quitaron.
- Pregunta por las **dos últimas semanas**: el periodo sale en el selector («Agosto · 18–31 ago», `mPer`) y se cuenta desde el día en que se responde (`mDate`: `M.resp = [mes, día]` con datos reales; en el prototipo, el día en que llega).
- Tu normal = media de tus meses anteriores (hasta 3; hacen falta 2, `mNorm`). Cambio claro = 3 puntos o más (`mCmp`). En cada frase, «más / menos que tu normal» a partir de ¾ de punto.
- **Sin rojo:** el bienestar va de ámbar a verde (`wTone`, `WSEG`, `segs(texto, true)`) en Mes, en la tarjeta del mes de Hoy y en el chat. «Sin responder» sigue en rojo (es el estado del formulario).
- Avisos con texto fijo, nunca de la IA, solo con el último formulario respondido (`mLvl`):
  - **bajada clara** (3+ puntos por debajo de tu normal): debajo del texto de arriba, con quién hablar, sin teléfonos;
  - **zona baja** (total ≤ 19): arriba del todo, el bloque de ayuda con el 024 y el 112 (`wemSupEl`), **también en Hoy** (`wemSupHoy`). Nunca a la vez que el de salud mental.
- De arriba abajo (estructura desde el 05-10-2026, la misma idea que Semana):
  - selector de mes (`monthPickEl`) y ayuda;
  - el titular («Basado en…» al pasar el ratón), un texto corto de la IA y, con una bajada clara, con quién hablar (`mesHeroF(…, 'read')`);
  - bloque principal, una tarjeta partida en dos:
    - tu bienestar: número y barra con la marca de tu normal (`mesHeroF(…, 'well')`);
    - la idea principal (`mesIdeasEl(R, 'feat')`), con «Propuestas por la IA» si viene de la IA;
  - cuatro cifras (`mtTilesEl`): bienestar, frente a tu normal, lo más alto y lo más bajo. Solo se tiñe de ámbar lo que baja claramente;
  - dos columnas (`mtColsEl`):
    - Tus respuestas: filas compactas con barra corta y la etiqueta junto al nombre, solo si cambia (`mesAnswersEl`). Si ninguna frase cambia, una línea «Las 7 frases, como tu normal» con «Ver las 7», que se despliega;
    - Mes a mes en puntos (`stepEl`): el total de cada mes; clic → ese mes; el que falta y el próximo, en discontinuo;
  - Más ideas (`mesIdeasEl(R, 'rest')`).
- Las ideas son **recomendaciones, no tareas** (sin casillas ni «0 de 3 hechas»). Si todo va bien, «Seguir con lo que te ha funcionado» sale con un círculo verde con la marca, que no se puede marcar. Iconos con sentido para cada frase: amanecer (optimismo), mano que ayuda (sentirte útil), ondas (calma), montaña (afrontar problemas), bombilla (claridad), personas (relaciones), brújula (autonomía) y bocadillo (hablarlo). Ya no usan los colores de la FC, la HRV ni el sueño. Estilo negro, elegido el 03-10-2026: cuadrado negro con borde fino y el icono en blanco (en modo claro, cuadrado claro e icono oscuro).
- Cambia con los escenarios (`MONTH_SC`: normal 28, estres 21 = bajada, gran 18 = zona baja…).
- IA: `promptMonth2` (tipo `month2`), con estas reglas:
  - recibe las frases sin nombre ni sexo (neutras, `WEM_N`), tu normal y los meses anteriores (`m2Prev`);
  - solo para el tono, sabe si hay un problema de salud en curso, sin detalle (`m2Health`);
  - pide un `texto` de 2–3 frases (`m2Texto`) e ideas;
  - dice «coincide con», nunca «porque»; sin palabras clínicas ni teléfonos.
  - La reserva hecha con datos es `fbMonth2` (titular, texto `m2Tx` e ideas).
- Chat: en Mes, la sugerida «¿Qué ha cambiado en mi bienestar?» (`q_wem_ch`) sustituye a «¿Mi cuerpo dice lo mismo que yo?».

**Chat «Pregunta a tus datos»** (oficial desde el 02-10-2026; hecho el 30-09-2026; ya no hay interruptor en el matraz). Lo que eligió el usuario: preguntas sugeridas + texto libre; disponible en todas las páginas; respuestas con texto, tarjetas o gráficos; comparar periodos, datos entre sí, formularios en el tiempo y antes/después de un problema.
- **Botón flotante** (01-10-2026, «como el del matraz»): redondo, abajo a la derecha, en todas las páginas (`chatFabEl`). El matraz queda a su izquierda. Ya no hay botón «Pregunta» arriba ni en la barra lateral.
- Siempre se abre **pequeño**: una ventana flotante de 420 px encima del botón, por encima de la página. Se queda abierta mientras miras la página.
- Con ⤢ pasa a la **vista general**: el chat a la derecha, de arriba abajo, y la página se estrecha (`chatDock` / `chatPush`).
  - La línea de su borde izquierdo se arrastra: hacia la izquierda se hace más grande, hacia la derecha más pequeña.
  - El ancho va de 380 px hasta lo que deja 640 px a la página. **Por defecto se abre con el más pequeño (380 px)**, como pidió el usuario el 01-10-2026; el doble clic vuelve a 380. Si lo cambias, se guarda (`mt-desk-chatw`).
  - La cabecera solo lleva el título, ampliar/reducir y ✕. Se quitó la etiqueta de la página («Hoy», «Semana»…) porque a 380 px cortaba el título en catalán y francés.
  - Si no cabe (iPad), va por encima de la página y puede taparla entera.
  - Con ⤡ vuelve a pequeño. Esc o ✕ lo cierran, y la próxima vez se abre pequeño.
- Los gráficos usan el ancho real de la columna (`chatBlockEl(…, cw)`): en la vista general se alargan sin agrandar las letras, y las tarjetas se ponen en una fila. La conversación mide como mucho 720 px de ancho.
- Sin burbujas: cada respuesta lleva la pregunta como título («Basado en…» al pasar el ratón), el texto con `[[ ]]` y las piezas. Debajo de la última, 2–3 preguntas para seguir (primero las de la página si has cambiado de página).
- Las **sugeridas** (`chatSuggest`, según la página; la del problema de salud solo si hay uno físico) las responde la app con los datos, sin IA (`chatRecipe`).
- El **texto libre** va a la IA (`chatPrompt`, textos `chRules` / `chCat` / `chCtx`); sin IA, responde la sugerida más parecida y lo dice («Respuesta automática a…», `chatMatch`). Autolesión o suicidio → respuesta fija con a quién acudir y 112 / 024, nunca a la IA (`chatRisk`).
- La IA no dibuja: elige piezas de un catálogo y la app las rellena con los datos reales (`chatValid`, `chatBlockEl`): `tarjetes` (valores de un periodo), `linia` (1 dato con su franja; 2 datos = filas de casillas día a día, con los días que cuentan enmarcados), `comparar` (barras de dos periodos: `7d`, `setmana-38`, `normal`, `problema` / `abans`…), `setmanes` (una nota del formulario o el WEMWBS semana a semana; las sin responder en discontinuo).
- A la IA nunca van el nombre, el APSQ, los textos libres, la HRV semanal ni el detalle de salud mental (solo «problema privado»). La conversación vive en el estado y se borra al cambiar de escenario.

## Preferencias de diseño del usuario (respétalas siempre)

- **Nada de gris para información.** El texto importante va en blanco o con el color de su estado; el gris queda solo para datos que faltan. No pongas subtítulos, pies de gráfico ni frases explicativas: «hacen ruido».
- **Sin leyendas.** El significado va en el propio elemento: etiquetas directas, unidades, color, hover o el texto de la IA.
- **Sin estética «AI slop»:** nada de etiquetas en mayúsculas espaciadas, brillos, halos ni puntos que laten. Nada que parezca un chat: se quitaron las burbujas de pregunta («no estamos en ChatGPT»).
- **Títulos fuera de las tarjetas**; las tarjetas solo llevan contenido. Tarjetas negras con borde fino, el estilo del resto de la app.
- **Color = cómo va el dato:** un solo color por valor, con todos los matices entre rojo, amarillo y verde (no solo tres colores, ni un degradado dentro de la barra). Cuando sirva, las barras y los anillos se **llenan** según lo bien que va el dato (más lleno = mejor), además del color. Los colores de identidad (rosa FC, azul HRV, cian sueño, blanco o negro cómo estás, violeta datos) nunca son verde, ámbar ni rojo.
- **Datos que faltan:** siempre visibles como hueco en discontinuo (`missChip`, anillos o celdas discontinuas), nunca como espacio en blanco.
- **Coherencia:** palabra, color y relleno cuentan lo mismo. Los bloques que van juntos se alinean («cuadrados uno con el otro»). Un texto largo no puede empujar las tarjetas hacia abajo.
- **Animaciones suaves** (fundidos, deslizamientos); nada de «pop», ondas ni brillos.
- **Poca información, de calidad:** quita lo que no aporta. No repitas en una página lo que ya sale en otra: por ejemplo, el plan de recuperación no va en Semana, y en Semana no se compara con la semana anterior.
- **Los avisos tienen que destacar**, pero sin molestar.
- **Todo funciona en modo oscuro y en modo claro.** Comprueba los dos.

## Forma de trabajar con este usuario

- **Si no estás seguro al 95 % de lo que quiere, pregunta antes**, con las dudas bien explicadas y opciones. Suele querer **varias versiones** para elegir: móntalas en la app, seleccionables desde el matraz (con etiqueta en castellano). Cuando elija una, **borra las demás y el selector**.
- Los rediseños grandes van primero como versión de prueba en el matraz, y la oficial se mantiene igual hasta que lo apruebe.
- Tras cada cambio, **compruébalo en un navegador real** con capturas: los estados (escenarios, simulaciones), el modo oscuro y el claro, y una pantalla estrecha si afecta a la maqueta. Si hay otra sesión de Claude activa, **no uses el navegador compartido de gstack** (lo usan las dos). Lanza un Chrome headless propio (`--headless=new --remote-debugging-port`) y contrólalo por CDP con un script de Node (Node 25 tiene `WebSocket` nativo).
- **Sesiones en paralelo:** a veces hay otra sesión editando el mismo archivo. Haz ediciones pequeñas y puntuales, vuelve a leer el archivo antes de editar y no reescribas bloques grandes que no son tuyos.
- Al terminar, explica en castellano y de forma breve qué ha cambiado y cómo verlo: escenario, matraz, recarga forzada.

## Pendiente

- **Panel del preparador: plan para que sea más claro** (pedido el 06-10-2026; solo plan, la versión oficial no se toca hasta que se apruebe).
  - El plan está en el Drive: «Panel del preparador: plan para que sea más claro (6 oct 2026)», 01 Planificació, id `1zWUfQzKI0YjkHh9CG07dqokGleUqd-LAf-Q04Qqhiu0`.
  - Los bocetos están en el lienzo, página «Inicio más claro · propuesta», que se abre por defecto. Son dos:
    - A · Por sesión, la recomendada: una tarjeta por sesión de hoy;
    - B · Una sola lista.
  - La idea de los dos: Inicio responde «¿a quién ajusto la sesión de hoy y cómo?».
    - Los avisos pasan a ser el motivo del ajuste.
    - Cada dato sale una sola vez.
    - Los motivos llevan números (FC +4 lpm, HRV −8 ms…).
    - Se decide en la misma fila: IA: Reducida · Aceptar · Otra.
  - Por decidir con el usuario:
    - A o B;
    - aceptar con un clic o con confirmación (H1: que no se acepte sin mirar);
    - si se quita «Sesión y adaptaciones» para hoy;
    - si Avisos pasa a «Historial»;
    - unificar las palabras en «ajuste».
- **Panel del preparador (versión oficial):**
  - en el Experimento, si el formato panel enseña la sugerencia de la IA (ahora no);
  - qué estilo va por defecto (ahora, Azul marino);
  - la tabla de Privacidad de la app del deportista (ver «La otra app»);
  - para la demostración, poner los enlaces de la sesión en las props `sessionExcel` y `sessionTP` de «Max Tracking - Ordenador».
- **Decisiones del 04 y el 05-10-2026.** Están escritas en el Drive, en «Decisiones de diseño (5 oct 2026): D4, D5, chat y panel del preparador» (01 Planificació, id `1Zzn6mWL-Tn0c0b2jv9QtOwMF0jRmYb9I7M5BPVWy-HE`).
  - **D4, a medias:** el registro diario será automático. FC, HRV y sueño llegan de la pulsera o el reloj. El 4 oct se concretó que «Cómo estás» lo calcula la IA y que lo que dijo al entrenador sale del formulario. Quedan dos problemas:
    - «Cómo estás» es la parte «Tú» de «Tú vs tu cuerpo» (Hoy) y de «Cómo te sientes vs qué dicen los datos» (Evolución): calculado con los mismos datos, compararía datos con datos;
    - el formulario semanal no pregunta qué le dijo al entrenador.

    Opciones: un Google Form diario muy corto, o sacarlo del formulario semanal. Se cierra después de la llamada con Oriol.
  - **D5:** la diferencia se enseña al deportista. Lo importante es lo que pone en el formulario sobre cómo se siente frente a lo que dicen los datos de la pulsera. Falta justificarlo en la metodología: se mide si la diferencia baja con el tiempo.
  - **Chat:** entra en el estudio, como fuente rápida y fiable. Su uso se medirá con las llamadas a la API de la IA cuando haya servidor.
  - **Diseño de la app del deportista congelado (05-10-2026)**, menos lo que depende de D4. A partir de ahora, solo errores.
- iPad: probarlo en un iPad de verdad.
- Chat: la IA real solo existe dentro del host (`window.claude`); en local responden las sugeridas.
- Aviso automático al especialista (idea del usuario, 02-10-2026): por decidir quién lo recibe, qué lo dispara y si el deportista lo ve en la app. Iría fuera de la app (en la hoja de respuestas de los Google Forms), porque la app no recibe el APSQ ni el texto libre.
- Privacidad: la fila «Informes de la IA → Entrenador».
- Semana, por decidir con el usuario:
  - las 3 palabras son texto libre: si alguien escribe algo de salud mental, sale en grande y llega a la IA;
  - las fichas de una lesión dicen «Bien» (son los datos del cuerpo, no la lesión).
- **Formulario semanal** (detalles en el documento «Cómo lo usa la app»):
  - Confirmar si la opción «Cap» de las zonas de lesión quiere decir «cabeza». La app no tiene esa zona.
  - En salud mental, el «desde cuándo» debería salir de P29, que es obligatoria. Ahora la app usa P15, que es opcional.
  - El ciclo menstrual no se puede usar todavía: el formulario pregunta cuánto duró la regla, no cuándo empezó.
- **Protocolo para las respuestas de riesgo de salud mental** (autolesión o pensamientos suicidas en el formulario semanal): la app no avisa a nadie. Hay que decidir quién las revisa y qué se hace.
- H3: aviso temprano de cansancio acumulado (quizá por correo).
- Portar la versión móvil al motor nuevo.
- Quitar el matraz cuando acabe la fase de prototipo.
