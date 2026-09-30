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

## Archivos

| Archivo | Qué es |
|---|---|
| `Max Tracking - Ordenador.dc.html` | Página que abre el usuario para ordenador/iPad. Solo importa el motor y pasa las props (`athleteName`, `aiLive`, `formDaily`, `formWeekly`, `formMonthly`). |
| `Max Tracking Escritorio.dc.html` | **El motor de ordenador/iPad (~570 KB).** Casi todo el trabajo se hace aquí. |
| `Max Tracking - Móvil.dc.html` + `Max Tracking.dc.html` | Versión móvil con el diseño y el modelo de datos **antiguos**. Se portará más adelante al motor nuevo; no la toques salvo que lo pida. |
| `support.js` | Runtime de los `.dc.html` (no editar). |
| `Max Tracking - Avui.dc.html`, `ios-frame.jsx`, `uploads/` | Restos o recursos antiguos. |

- Hay repo git y el usuario hace sus commits. **No hagas commit si no te lo pide.**
- **Para verlo:** sirve la carpeta con `python3 -m http.server <puerto> --bind 127.0.0.1` y abre `http://127.0.0.1:<puerto>/Max%20Tracking%20-%20Ordenador.dc.html`. Si el usuario no ve un cambio, casi siempre es la caché: pídele una recarga forzada (Cmd+Shift+R; en Safari, Cmd+Option+R). Los `localStorage` son por dirección, así que en un puerto nuevo sale en catalán y con el estilo por defecto.
- **Comprobar sintaxis:** extrae el `<script>` más largo del motor y pásale `node --check`.

## Cómo está hecho el motor (`Max Tracking Escritorio.dc.html`)

- Una clase `Component` de React sin JSX: `h = React.createElement`. Los comentarios del código están en **catalán**; escribe los nuevos igual.
- **Textos:** diccionario `TX` con hojas `{ ca, es, fr, en }`. `L()` lo resuelve al idioma activo y `t(clave, ...args)` lee `TX.UI` (las entradas con datos son funciones). **Todo texto visible, en los 4 idiomas.** El panel del matraz (prototipo) va solo en castellano. Cuando dejes de usar una clave, bórrala.
- **Días:** índice `i`, con `day(i)`, `dShort`, `dLong`, `dFull`. `0` = viernes 14 ago 2026; **`41` = hoy (jueves 24 sep 2026)**. Semana 38 = índices 31–37; semana 39 (en curso) = 38–44. El formulario semanal se envía el domingo (`a + 6`) y el mensual el día 31 ago (índice 17); el siguiente mensual es el 30 sep (índice 47). `formState(sendDay, done)` → `future` / `new` (ámbar, día de envío y el siguiente) / `late` (rojo, desde el 2.º día) / `done`.
- **Datos:** `BASE` + `TAILS[escenario]` → `data(sc)` devuelve `{ vals, coach, band, pos, feel, obj, st, nBand, nData }`.
  - Datos diarios: `rhr` (FC en reposo), `hrv`, `son` (sueño), `estat` (cómo estás, 1–5) y `coach` (lo que le dijo al entrenador: `be` / `reg` / `mal` / `res`).
  - `WEEKS` (semanales: 5 escalas de 1 a 10 con `fatiga` invertida, 3 palabras, comentario —la pregunta 9 explica la nota de Organización—, `ostrc` 0–3 + `inici`, `pb` = detalle OSTRC-H2 del problema: lesión con zona y si empezó de golpe o poco a poco, o enfermedad con síntomas, más cuánto afectó al entrenamiento, al rendimiento y los síntomas; `hrvSet`) se sobrescribe por escenario con `WEEK_SC`; `MONTHS` (WEMWBS, 7 ítems).
- **Escenarios** (`SCN`, matraz): `normal` Día normal · `descans` Fatiga · `salut` Problema de salud · `disc` Discrepancia · `gran` Gran diferencia · `estres` Te notas peor · `buit` Sin datos · `nou` Deportista nuevo. Cambiar de escenario tiene que enseñar diseños distintos en todas las páginas.
- **Escala de color:**
  - `qOf` (distancia a la franja), `hOf` / `lvlOf` (`ok` / `mid` / `bad`) y `status()`.
  - `tone(h)` para texto, líneas, puntos y barras; `toneF(h)` para rellenos grandes (anillos, celdas, bloques de estado); `sTone(v, min, max, inv)` para los cuestionarios (curva `SQ`).
  - `dayState(sc, i)` (`fons` Óptimo / `mod` Moderado / `rec` Recuperación / `prec` Precaución) y `dayTone`.
- **Estilos:** `THEMES` (`gpt` por defecto, más `hibrid`, `editorial` y `grafit`) × modo oscuro/claro, todo con tokens `C.*` y `G.*`. Nunca pongas colores fijos (`#000`, etc.).
  - `C.well` para paneles de gráfico dentro de tarjetas.
  - `alertC(col, a)` para cualquier aviso (en claro, más marcado).
  - `G.gpt` cambia la estructura: con ChatGPT, `sideEl` + `topEl` + `reportChat`; con el resto, `railIcons` + `reportBox`.
- **IA:** `fetchAI` → `window.claude.complete`, con respuesta en JSON y caché en `localStorage` (`mt-desk-ai-v11`; súbele la versión si cambian los datos o los prompts).
  - Prompts: `promptDaily`, `promptWeek`, `promptWeekCur`…
  - Textos de reserva generados con datos: `fbDaily`, `fbWeek`, `fbWeekCur`.
- **Piezas útiles:**
  - `box({ title, right, id, st }, …)` / `secHead`: título fuera de la tarjeta.
  - `missChip`: dato que falta, en discontinuo.
  - `flashTo(id)`: baja al bloque y lo ilumina en ámbar.
  - `goWeek(wi, target)`: -1 = semana en curso; destinos `form`, `q-<escala>`, `ost`, `days`.
  - `goMetric(k)`: va a Evolución › Día a día.
  - `formBtn(url, …)`, `stateMark`, `markWord`.
- **Estado persistente (`localStorage`):** `mt-lang`, `mt-desk-theme-v3`, `mt-desk-mode`, `mt-desk-side`, `mt-desk-done`, `mt-desk-trk`, `mt-desk-charts-v2`, `mt-desk-ai-v11`, `mt-proto-hidden`, `mt-desk-wklab`.
- **Matraz** (`protoEl`, tecla E): escenario, «Opciones de gráficos», «Probar Semana nueva» (`wkLab`, versión de prueba), «Simular cuestionarios sin responder» (`simOverdue`), «Simular aviso de bienestar» (`simWell`), abrir la versión móvil y ocultar el botón. Es temporal; se quitará cuando acabe la fase de prototipo.

## Páginas (estado a 30-09-2026)

**Hoy** (`pageAvui`), de arriba abajo:
1. Aviso de bienestar (`wellAlertEl`), solo con WEMWBS bajo o que haya bajado mucho.
2. Informe del día:
   - A la izquierda, el veredicto con la palabra del estado y 4 anillos (`RingsV`). Cada anillo se llena según dónde cae el dato respecto a tu normal (más lleno = mejor) y lleva debajo un texto en dos líneas: la dirección en blanco y el nivel con el color del anillo.
   - A la derecha, el titular y el párrafo de la IA, cortado a unas 4 líneas con «Seguir leyendo» (`ClampBox`).
   - Debajo, «Tus cuestionarios» (`formsSummaryEl`): dos tarjetas negras alineadas (subgrid) con las notas en barras de color, sin ▲▼. Al pie, «Próximo…» o el aviso «Sin responder · Semana 38» con «Responder».
3. Seguimiento de salud (`trackingEl`), si el último formulario semanal marca un problema.
4. «Ideas para recuperarte» (`recoveryPlanEl`): hábitos que propone la IA y el hueco del preparador físico («Próximamente»).
5. Franja «Tú vs tu cuerpo» (`feelStripEl`, id `mt-feel`).
6. «Esta semana» (`weekNowEl`): nombres de los datos a la izquierda y 7 tarjetas de día negras con el borde del color de su estado, la palabra del estado, 4 barras que se llenan según el estado y la palabra del entrenador. Los días que faltan van en discontinuo.
7. Notificación flotante del entrenador (`coachToastEl`), solo si lo que dijo al entrenador no cuadra con el estado de sus datos. Entra deslizándose por la derecha, sigue al hacer scroll y tiene ✕. Al tocarla baja a `mt-feel`.

**Semana** (`pageSetmana`): se abre en la **semana en curso** y se navega con flechas.
- **Versión de prueba (30-09-2026, matraz › «Probar Semana nueva», `pageSetmanaF`)**, pendiente de aprobar. Estructura propia, centrada en el feedback del formulario semanal (nada de veredicto con anillos como Hoy). Se abre en el último formulario respondido; la semana en curso solo sale bajo el título («Próximo formulario…» o el aviso con «Responder»). De arriba abajo: sus 3 palabras en grande + titular de la IA + 2–3 claves en tarjetas negras (`wkHeroF`, `wkKeys`) · «Salud» solo si hay problema y **solo con el progreso hasta que termina** (`healthInfo` + `healthDays`): qué fue y «✓ Terminó en N días» / «En curso · día N», y una ficha negra por día como «Esta semana» (borde y barra del color de ese día, «Inicio» y «✓ Fin»), desde el día que empezó hasta que tus datos vuelven a tu normal por primera vez · «Tus respuestas» (solo esta semana): cada nota con «más / menos / como tu normal» (media de las 4 anteriores, `normOf`) y una **marca blanca en la barra donde está tu normal** · «Semana a semana» en un bloque aparte (panel negro con las líneas, `WeekAnswersF` con `mode: 'panel'`). Cada bloque habla de un solo momento · «Lo que dijiste vs tus datos» (fatiga ↔ FC y HRV, descanso ↔ sueño, salud ↔ estado de los días, entrenador; `wkVs`, `wkVsEl`). Al aprobarla: hacerla la oficial y borrar lo viejo y el interruptor.
- Informe de la semana: veredicto con anillos a la izquierda y el párrafo de la IA a la derecha.
- «Salud y participación» (`weekHealthEl`): el estado en una frase, la línea de tiempo del problema y 3 datos.
- «Tu formulario / Tu último formulario» (`weekFormEl`): 5 barras de un solo color según la nota, sin comparar con la semana anterior. Al lado, «Los 7 días» (`weekDays2El`).
- Al final, el mapa de semanas (`heat2El`).

**Mes**, **Evolución**, **Privacidad**, **Ajustes**: existen. Mes es la siguiente página que toca repasar.

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

- Mes: por repasar con el mismo criterio que Hoy y Semana.
- Privacidad: la fila «Informes de la IA → Entrenador»; el APSQ todavía se nombra en textos de Privacidad y Mes (debe desaparecer).
- El nombre del deportista todavía se envía a la IA.
- H3: aviso temprano de cansancio acumulado (quizá por correo).
- Portar la versión móvil al motor nuevo.
- Quitar el matraz cuando acabe la fase de prototipo.
