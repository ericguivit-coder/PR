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
- **La app no recoge datos propios.** Todo llega de Google Forms: diario, semanal (con OSTRC) y mensual (WEMWBS). Nunca hagas pantallas de formulario, previsualizaciones de preguntas ni bloques de «responde aquí». Los botones «Responder» abren el Google Form (props `formDaily` / `formWeekly` / `formMonthly`) y se desactivan si no hay enlace.
- La IA **sugiere, nunca ordena**. No diagnostica, no sustituye al entrenador y siempre dice qué datos ha usado («Basado en…», visible al pasar el ratón por el título del informe). El aviso «Orientativo, no es un diagnóstico médico…» se queda visible.
- **Nunca** aparecen en la app el **APSQ** ni el **texto libre mensual**. `hrv_semanal` nunca va a la IA.
- **No atribuyas causas** que los datos no permiten afirmar. Por ejemplo, «te notas peor que tus datos» no se etiqueta como estrés o fatiga mental: se dice la diferencia y se sugiere hablarlo con el entrenador. «Cómo estás» es una sola pregunta de 1 a 5.
- Público: jóvenes sin formación técnica. Todo tiene que entenderse sin explicación.
- Idiomas: **catalán por defecto**, más castellano, francés e inglés (Ajustes). Ningún texto sin traducir ni desbordado.
- **Salud mental** (del formulario semanal): la app solo usa el tipo y desde cuándo. Nunca muestra ni envía a la IA síntomas, causas, profesional ni días; la IA solo sabe que hay «un problema privado». En Hoy y Semana sale arriba del todo, destacado, con un texto fijo (nunca de la IA): con quién hablar y los teléfonos 024 y 112 (España). No pone los días en «Precaución».
- **Formularios sin responder:** nada de ese formulario, y nada de formularios antiguos presentado como si fuera de ahora (ni en el texto del día ni en avisos). Se enseña el último respondido con el aviso «Sin responder» + «Responder», y el que falta como hueco en discontinuo.

## La otra app: entrenadores y preparador físico

Habrá una segunda app, para los entrenadores y **sobre todo para el preparador físico**, conectada con esta. Todavía no existe: de momento es contexto para pensar mejores ideas (añadido el 01-10-2026). No empieces nada de ella si el usuario no lo pide.

- **Funciones que conectarán las dos apps** (se irán añadiendo más poco a poco):
  - **Propuestas del preparador:** manda ejercicios o rutinas, y salen en «Ideas para recuperarte» (`recoveryPlanEl`), en el hueco «Propuestas del preparador físico» que ahora pone «Próximamente». Llegan desde su app, no de Google Forms.
  - **Lesiones compartidas:** si el deportista la comparte, el preparador sigue la lesión (el Seguimiento de Hoy, con «¿Cómo va hoy?» y «Ya estoy bien») y guía la vuelta a entrenar.
  - **Avisos al entrenador:** cuando un deportista acumula cansancio (H3) o marca un problema nuevo en el formulario semanal.
- **Privacidad: lo elige el deportista.** Cada deportista decide en Privacidad qué comparte con el entrenador y el preparador. **La salud mental no se comparte nunca.** Falta decidir qué se comparte si el deportista no toca nada. La tabla que hay ahora en Privacidad (`pagePriv`) dice otra cosa (datos diarios y «lo que le dices», sí; formularios e informes de la IA, no): habrá que cambiarla cuando exista la otra app.
- **En el estudio sirve a H1 y H3:** el entrenador planifica con la evolución analizada por la IA, no con puntuaciones sueltas (H1), y recibe los avisos de cansancio acumulado (H3).
- **Cuidado con H2 y H4.** Si el entrenador ve los datos, el deportista puede cambiar lo que le cuenta (H2). Si ve quién no ha respondido y le insiste, ya no se sabe si el deportista rellena más por la app o por el entrenador (H4). Cuando propongas algo que vea el entrenador, di si puede tocar H2 o H4.
- Mientras no se decida otra cosa, las reglas del estudio de arriba también valen para la otra app: la IA sugiere y no diagnostica, no se atribuyen causas, y nunca salen el APSQ ni el texto libre mensual.
- **Para qué sirve este contexto:** al pensar ideas para la app del deportista, mira si tienen otra mitad en la del preparador (algo que manda, que ve o que le avisa) y dilo al proponerlas.

## Archivos

| Archivo | Qué es |
|---|---|
| `Max Tracking - Ordenador.dc.html` | Página que abre el usuario para ordenador/iPad. Solo importa el motor y pasa las props (`athleteName`, `aiLive`, `formDaily`, `formWeekly`, `formMonthly`). |
| `Max Tracking Escritorio.dc.html` | **El motor de ordenador/iPad (~670 KB).** Casi todo el trabajo se hace aquí. |
| `Max Tracking - Móvil.dc.html` + `Max Tracking.dc.html` | Versión móvil con el diseño y el modelo de datos **antiguos**. Se portará más adelante al motor nuevo; no la toques salvo que lo pida. |
| `support.js` | Runtime de los `.dc.html` (no editar). |
| `Max Tracking - Avui.dc.html`, `ios-frame.jsx`, `uploads/` | Restos o recursos antiguos. |

- Hay repo git y el usuario hace sus commits. **No hagas commit si no te lo pide.**
- **Para verlo:** sirve la carpeta con `python3 -m http.server <puerto> --bind 127.0.0.1` y abre `http://127.0.0.1:<puerto>/Max%20Tracking%20-%20Ordenador.dc.html`. Si el usuario no ve un cambio, casi siempre es la caché: pídele una recarga forzada (Cmd+Shift+R; en Safari, Cmd+Option+R). Los `localStorage` son por dirección, así que en un puerto nuevo sale en catalán y con el estilo por defecto.
- **Comprobar sintaxis:** extrae el `<script>` más largo del motor y pásale `node --check`.
- **Documentos de los formularios, en el Drive del usuario.** Se leen con el conector de Google Drive.
  - «Formulari de rendiment i salut de l'esportista 2026-2027 — Preguntas y variables» (en Mi unidad): las 58 preguntas del semanal, con sus opciones, saltos y variables `entry.NNN`.
  - «Formulari de rendiment i salut de l'esportista 2026-2027 — Cómo lo usa la app» (carpeta «PR - IA i seguiment d'esportistes › 03 Metodologia», id `1Trf5DWRnhkFLeJMKOylFGw6ltbsL6QVAxbrrR5RCgxY`). Contiene:
    - qué mide cada pregunta y qué hace la app con ella: dónde sale, si va a la IA o si no se usa nunca;
    - los datos que se recogen y no se usan, como el ciclo menstrual;
    - lo que hay que revisar en el formulario.

    **Si cambias cómo usa la app una pregunta del semanal, díselo al usuario.** Con el conector no se puede editar un Google Doc, así que hay que crear una versión nueva en la misma carpeta.
  - «Formulari de benestar i salut mental 2026-2027 — estructura» (en Mi unidad): el mensual, con SWEMWBS (7 ítems), APSQ (10 ítems, nunca en la app) y el texto libre.

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
- **Estilos:** `THEMES` (`gpt` por defecto, más `hibrid`, `editorial` y `grafit`) × modo oscuro/claro, todo con tokens `C.*` y `G.*`. Nunca pongas colores fijos (`#000`, etc.).
  - `C.well` para paneles de gráfico dentro de tarjetas.
  - `alertC(col, a)` para cualquier aviso (en claro, más marcado).
  - `G.gpt` cambia la estructura: con ChatGPT, `sideEl` + `topEl` + `reportChat`; con el resto, `railIcons` + `reportBox`.
- **IA:** `fetchAI` → `window.claude.complete`, con respuesta en JSON y caché en `localStorage` (`mt-desk-ai-v12`; súbele la versión si cambian los datos o los prompts).
  - Prompts: `promptDaily`, `promptWeek` (tipo `week2` en Semana), `promptMonth`, `promptTrend`.
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
- **Estado persistente (`localStorage`):** `mt-lang`, `mt-desk-theme-v3`, `mt-desk-mode`, `mt-desk-side`, `mt-desk-done`, `mt-desk-trk` (respuestas de «¿Cómo va hoy?» y «Ya estoy bien», una por problema), `mt-desk-charts-v2`, `mt-desk-ai-v12`, `mt-proto-hidden`, `mt-desk-chatlab` (chat de prueba encendido o no; la conversación nunca se guarda), `mt-desk-chatw` (ancho de la vista general del chat).
- **Matraz** (`protoEl`, tecla E). Es temporal; se quitará cuando acabe la fase de prototipo. Contiene:
  - el escenario;
  - «Opciones de gráficos»;
  - «Salud en el formulario (semana 38)» (`pbSim`: Escenario / Lesión / Mental / Dos);
  - «Probar chat» (`chatLab`), la versión de prueba del chat;
  - «Simular cuestionarios sin responder» (`simOverdue`);
  - abrir la versión móvil y ocultar el botón.

## Páginas (estado a 30-09-2026)

**Hoy** (`pageAvui`), de arriba abajo:
1. Salud mental (`wellBlockEl`): si el último formulario semanal la marca en curso, sale el bloque `menBlockEl`, el mismo de Semana. Si no, no sale nada (se quitó el aviso de bienestar aparte por WEMWBS bajo, que duplicaba este bloque).
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
     - la curva, con la vez anterior del mismo problema en discontinuo.
   - Si todos han terminado, sale una línea «Terminó en N días», solo si terminó en los últimos 7 días.
   - «Precaución» solo mientras hay un problema físico en curso.
4. «Ideas para recuperarte» (`recoveryPlanEl`): hábitos que propone la IA y el hueco del preparador físico («Próximamente»).
5. Franja «Tú vs tu cuerpo» (`feelStripEl`, id `mt-feel`).
6. «Esta semana» (`weekNowEl`): nombres de los datos a la izquierda y 7 tarjetas de día negras con el borde del color de su estado, la palabra del estado, 4 barras que se llenan según el estado y la palabra del entrenador. Los días que faltan van en discontinuo. Clic en un día → Evolución › Día a día; «Ver semana» → Semana.
7. Notificación flotante del entrenador (`coachToastEl`), solo si lo que dijo al entrenador no cuadra con el estado de sus datos. Entra deslizándose por la derecha, sigue al hacer scroll y tiene ✕. Al tocarla baja a `mt-feel`.

**Semana** (`pageSetmana`, oficial desde el 30-09-2026; la antigua con anillos, «Salud y participación», «Los 7 días» y el mapa de semanas se borró).
- Centrada en el feedback del formulario semanal. Se abre en el **último formulario respondido**. La semana en curso solo sale bajo el título: «Próximo formulario…», o el aviso con «Responder».
- Arriba a la derecha, `weekPickEl`: flechas ‹ › y un desplegable con todas las semanas de la temporada (fechas y 3 palabras). La que está sin responder sale en discontinuo, con «Responder».
- De arriba abajo, de más a menos importante:
  1. Salud mental (`menBlockEl`, solo si hay). Es la versión **compacta** (elegida el 01-10-2026): tarjeta ámbar con desde cuándo, una frase, con quién hablar en tres botones (el detalle al pasar el ratón) y los teléfonos 024 y 112 en una línea. Para verla: matraz › «Salud en el formulario» › Mental.
  2. Las 3 palabras en grande, el titular de la IA y 2–3 claves en tarjetas negras (`wkHeroF`, `wkKeys`).
  3. «Salud», solo lesión o enfermedad (`weekHealthF` → `healthDays`):
     - nombre + «Nueva / Ha vuelto / Ha empeorado / Crónica» + estado;
     - «2.ª vez esta temporada · la anterior duró 5 días» con enlace;
     - una **fila continua** de fichas por día. Con dos problemas comparten fechas; los días sin registro van en discontinuo;
     - la ficha «Revisión · Formulario» del próximo formulario (en rojo si está sin responder);
     - «Ir al seguimiento» → Hoy, con ese problema elegido.
  4. «Tus respuestas» (solo esta semana): «más / menos / como tu normal», con la media de las 4 anteriores (`normOf`) y una marca blanca en la barra.
  5. «Lo que dijiste vs tus datos» (`wkVsEl` → `wkVsBars`): dos barras por pareja, tú en blanco y tus datos en violeta; los días sin registro se avisan. Las parejas: fatiga ↔ FC y HRV, descanso ↔ sueño, salud ↔ estado de los días, entrenador.
  6. «Semana a semana» (`WeekAnswersF` con `mode: 'panel'`): solo para mirar, no cambia la página. La semana sin responder es una columna en discontinuo.

**Mes**, **Evolución**, **Privacidad**, **Ajustes**: existen. Mes es la siguiente página que toca repasar. Ya se abre en el último mes respondido: el que falta sale en discontinuo, con el aviso «Sin responder» (`monthPendEl`).

**Chat «Pregunta a tus datos»** (versión de prueba del 30-09-2026, matraz › «Probar chat», pendiente de aprobar). Lo que eligió el usuario: preguntas sugeridas + texto libre; disponible en todas las páginas; respuestas con texto, tarjetas o gráficos; comparar periodos, datos entre sí, formularios en el tiempo y antes/después de un problema.
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

- Chat: versión de prueba en el matraz, pendiente de que el usuario la apruebe. La IA real solo existe dentro del host (`window.claude`); en local responden las sugeridas.
- Mes: por repasar con el mismo criterio que Hoy y Semana.
- Privacidad: la fila «Informes de la IA → Entrenador»; el APSQ todavía se nombra en textos de Privacidad y Mes (debe desaparecer).
- El nombre del deportista todavía se envía a la IA.
- **Formulario semanal** (detalles en el documento «Cómo lo usa la app»):
  - Confirmar si la opción «Cap» de las zonas de lesión quiere decir «cabeza». La app no tiene esa zona.
  - En salud mental, el «desde cuándo» debería salir de P29, que es obligatoria. Ahora la app usa P15, que es opcional.
  - El ciclo menstrual no se puede usar todavía: el formulario pregunta cuánto duró la regla, no cuándo empezó.
- **Protocolo para las respuestas de riesgo de salud mental** (autolesión o pensamientos suicidas en el formulario semanal): la app no avisa a nadie. Hay que decidir quién las revisa y qué se hace.
- H3: aviso temprano de cansancio acumulado (quizá por correo).
- Portar la versión móvil al motor nuevo.
- Quitar el matraz cuando acabe la fase de prototipo.
