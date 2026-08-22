# PROMPT.md — Replicar CociNADA

Este archivo es un **prompt listo para copiar y pegar** en un agente de IA de
programación (Claude, opencode, etc.) para construir una versión propia de
CociNADA desde cero. Está pensado como una **página única autosuficiente**:
sin backend propio, sin base de datos externa, con "login" simulado en el
propio navegador y con una única llamada a un servicio externo de IA para
generar recetas.

\---

## Prompt

```
Eres un desarrollador full-stack especializado en frontend. Vas a construir
desde cero, en un proyecto nuevo, una aplicación web llamada "CociNADA", un
asistente de cocina con estética ilustrada, cálida y artesanal. Va a vivir
como una única página autosuficiente (sin backend propio): todo el estado
persiste en el navegador y la única pieza "server-side" es una llamada a un
servicio externo de generación de recetas por IA.

Trabajá por etapas y al final de cada una mostrame qué quedó andando.

========================================================================
## EL PRODUCTO
========================================================================

CociNADA es "tu asistente inteligente de cocina". La idea:

- La persona anota los ingredientes que tiene, o el nombre de un plato que
  se le antoja, o le pide al asistente que la sorprenda.
- La app le devuelve una o varias sugerencias de plato (o la receta
  completa) usando un servicio externo de IA.
- Puede guardar cualquier plato en un planificador semanal (desayuno,
  almuerzo, cena, de lunes a domingo).
- A partir del planificador se arma sola una lista de compras, agrupada
  por plato y filtrable por día.
- Hay un sistema de cuenta simulado (registro/login solo en el navegador,
  sin servidor de autenticación real) que habilita enviarse la lista de
  compras por mail.

Toda la UI y los textos van en español, con un tono cálido y cercano, nunca
corporativo. La estética es "ilustración de libro de recetas hecho a mano":
colores tierra y pastel, formas muy redondeadas, sombras suaves y
animaciones de rebote elástico con personalidad.

========================================================================
## TECNOLOGÍAS Y ENTORNO
========================================================================

- Página web autosuficiente (HTML/CSS/JS o el framework que prefieras),
  pensada para abrir directo en el navegador sin instalar un backend propio.
  No hay servidor de aplicación, ni base de datos externa, ni Docker.
- Todo el estado (usuarios registrados, sesión activa, historial de
  recetas, planificador semanal) se guarda en el propio navegador
  (almacenamiento local persistente) y sobrevive a cerrar y volver a abrir
  la pestaña.
- La única conexión a internet real de la app es hacia un servicio externo
  de generación de recetas por IA (a elección tuya, documentá cuál usás y
  cómo se configura la clave de acceso) y, opcionalmente, hacia un servicio
  de envío de mails para la lista de compras. Si no hay conexión o falla la
  clave, la app tiene que avisar con un error claro, nunca romperse en
  blanco.
- No hay verificación real de identidad: el registro/login es una
  simulación de cara al usuario, guardada en el propio navegador. Dejá esto
  explícito en el código con un comentario, porque nunca debe tratarse como
  autenticación real ni guardar contraseñas en texto plano si eventualmente
  se conecta a un backend real.
- Dos familias tipográficas del sistema o vía Google Fonts: una redondeada
  y con carácter para títulos, otra neutra y legible para el resto.

========================================================================
## QUÉ TIENE QUE SABER HACER (ETAPA ÚNICA)
========================================================================

### 1. Cuenta y sesión (simuladas)
- Registro con correo (solo se aceptan correos de dominio Gmail),
  contraseña y confirmación de contraseña. No se puede registrar dos veces
  el mismo correo.
- Login con correo y contraseña ya registrados.
- La sesión activa persiste entre visitas. Un botón "Cerrar sesión" la
  termina.
- La barra superior refleja el estado: sin sesión muestra "Iniciar sesión"
  / "Registrarse"; con sesión muestra el correo del usuario y "Cerrar
  sesión".

### 2. Pedir una sugerencia (tres modos, en pestañas)
- \*\*"Cocina con lo que hay"\*\*: se cargan ingredientes de a uno (chips que
  se pueden quitar) y un botón "¡Preparar!" pide sugerencias con esos
  ingredientes.
- \*\*"¿Tienes un plato en mente?"\*\*: se escribe el nombre de un plato
  puntual y "Buscar receta" trae su receta directamente.
- \*\*"Deja que el asistente decida"\*\*: se elige momento del día (desayuno /
  almuerzo / cena) y estilo (indulgente, saludable, sin gluten) y
  "Sorpréndeme" pide una receta libre.
- Mientras se espera la respuesta del servicio de IA se muestra un estado
  de carga con texto acorde a la acción en curso.
- El resultado son una o varias tarjetas de plato; cada una se puede abrir
  para ver la receta completa (ingredientes, pasos, tiempo estimado,
  dificultad, consejo del chef opcional) o asignar directo al planificador.
- Cada receta consultada se guarda en un historial de la sesión (sin
  duplicarse) para volver a verla sin pedirla de nuevo.

### 3. Planificador semanal
- Grilla de lunes a domingo, con tres casillas por día (desayuno, almuerzo,
  cena). Cada casilla vacía dice claramente que no tiene plan asignado.
- Asignar un plato (desde una sugerencia o desde el historial) abre un
  modal para elegir día y comida; al confirmar, queda guardado y avisa con
  una notificación.
- Cada casilla ocupada se puede vaciar con un botón de "quitar".

### 4. Lista de compras
- Se arma sola a partir de todo lo asignado en el planificador: una tarjeta
  por plato asignado, con sus ingredientes en checkboxes.
- Filtro por día (o "todos") y botón para copiar la lista completa al
  portapapeles.
- Botón para enviar la lista por mail: si no hay sesión activa, pide
  iniciar sesión primero; si la hay, se envía el planificador completo con
  sus ingredientes.

### 5. Ayudas visuales automáticas
- Cada ingrediente o nombre de plato se asocia automáticamente a un emoji
  representativo según una lista de palabras clave en español conocidas
  (huevo, tomate, pollo, pescado, pasta, arroz, pizza, sushi, ensalada,
  sopa, postre, fruta, verdura, etc.); si no coincide con ninguna, usa un
  emoji genérico de plato de comida.
- Cualquier ingrediente pegado como texto (separado por comas o saltos de
  línea) se limpia automáticamente de viñetas o numeración sobrante antes
  de mostrarse como chip.

========================================================================
## DISEÑO (importante, es un proyecto de clase y se mira)
========================================================================

### Concepto y tono
Estética ilustrada, cálida y artesanal, tipo libro de recetas hecho a mano:
colores tierra y pastel, formas muy redondeadas hasta llegar a píldoras
completas, sombras suaves y difusas (nunca duras), y animaciones de rebote
elástico en botones e iconos que dan personalidad juguetona sin perder
elegancia.

### Paleta de colores
- Coral/terracota cálido como color principal de acción, con una variante
  oscura para interacción y una clara para acentos y bordes.
- Crema/beige cálido como fondo general.
- Dos blancos rotos (uno muy pálido, uno casi puro pero cálido) para
  tarjetas y paneles.
- Durazno/melocotón como panel destacado, con variante más profunda para
  bordes.
- Verde menta suave como acento secundario (éxito, calendario,
  confirmaciones), con variante oscura y variante casi blanca.
- Amarillo mostaza cálido como acento puntual (botón de registro, detalle
  del logo).
- Tres tonos de marrón para texto: oscuro casi negro (principal), medio
  (secundario), claro (terciario / placeholder).

### Fondo de toda la página
Sobre el crema base: tres manchas circulares muy suaves y difuminadas
(durazno arriba a la izquierda, menta arriba a la derecha, coral tenue
abajo a la derecha) y una textura repetida y muy sutil de pequeños emojis
de comida (pizza, ensalada, pan, pastel, aguacate) esparcidos de forma
irregular, con muy baja opacidad para no competir con el contenido.

### Estructura general
Todo centrado en una columna de ancho cómodo de lectura. De arriba abajo:
barra de navegación superior, sección de bienvenida (hero), zona de trabajo
de tres paneles, zona de resultados (oculta hasta generar algo),
planificador semanal, lista de compras, pie de página. Un personaje
flotante (emoji de chef) queda fijo en la esquina inferior derecha en todo
momento, flotando suavemente, sin ser clickeable.

### Barra de navegación
Forma de píldora completa, pegada arriba al hacer scroll, fondo en
degradado diagonal coral → coral oscuro con sombra cálida debajo. A la
izquierda, logo (emoji de olla + nombre de marca en tipografía destacada,
color crema). A la derecha, la zona de autenticación descrita en la sección
1. Todos los botones se elevan y ganan sombra al pasar el cursor, y se
encogen al hacer clic. En pantallas angostas pasa a organizarse en columna.

### Hero
Centrado: un icono grande de olla feliz dibujado con trazo grueso tipo
hecho a mano, cuerpo amarillo, tres burbujas de colores (coral, menta,
amarillo) asomando como ingredientes, y tres líneas de vapor animadas
subiendo con leve rotación y desvanecimiento, cada una desfasada en el
tiempo para que se vea orgánico. Todo el icono flota en bucle y se inclina
y agranda levemente al pasar el cursor. Junto al icono, el nombre de marca
en grande. Debajo, el eslogan en una píldora sutil. Debajo, el selector de
los tres modos (sección 2) en pestañas tipo píldora: la activa con fondo
menta sólido, borde menta oscuro, texto oscuro y leve sombra; las inactivas
en blanco con borde durazno, que se iluminan y elevan al pasar el cursor.

### Zona de trabajo (tres paneles)
Lado a lado en pantallas anchas (el central más ancho), apilados en
angostas. Esquinas muy redondeadas y sombra suave en los tres.
- \*\*Izquierdo — Ingredientes\*\* (fondo blanco cálido): campo de texto +
  botón circular coral de "más" para agregar (también con Enter). Debajo,
  nube de chips menta pálido con emoji + nombre + botón de quitar, con
  animación de aparición tipo rebote.
- \*\*Central — Formas de cocinar\*\* (fondo sólido durazno): cambia según la
  pestaña activa del hero (ver sección 2: campo de plato + "Buscar receta",
  o selects de momento/estilo + "Sorpréndeme", o el botón "¡Preparar!" con
  brillo pulsante permanente). Los campos y selects usan fondo blanco
  translúcido para resaltar sobre el durazno.
- \*\*Derecho — Platos anteriores\*\* (fondo blanco cálido): lista vertical de
  píldoras de historial (emoji + nombre + flecha), clickeables para reabrir
  el detalle; si está vacío, texto en cursiva; si crece mucho, scroll
  interno.

### Zona de resultados
Estado de carga en píldora centrada menta pálido con emoji de olla girando
y rebotando, con texto que cambia según la acción. Cuadrícula de tarjetas
de sugerencia (emoji grande, nombre, descripción corta, botones "Ver
receta" / "Asignar") que se elevan y resaltan en coral al pasar el cursor.
Tarjeta de receta detallada insertada en el flujo (no como modal), con
borde superior coral grueso, sombra pronunciada, aparición animada,
ingredientes, pasos numerados, consejo del chef opcional en caja menta con
borde izquierdo, y botón ancho "Guardar en menú semanal". Botón de contorno
menta "Probar otra búsqueda" para reiniciar. Notificaciones tipo toast en
píldora, menta pálido para éxito o rosado suave para error, con
autodesaparición.

### Planificador y lista de compras
Dos tarjetas blancas grandes, apiladas. El planificador con grilla de
lunes a domingo (1 a 7 columnas según ancho disponible) y tres casillas por
día (mini tarjetas blancas con etiqueta de comida en mayúsculas pequeñas,
nombre del plato con emoji o "sin plan" en cursiva gris, botón de quitar en
la esquina). La lista de compras con filtro de días en píldoras
desplazables horizontalmente, botón "Copiar", tarjetas por plato con
checkboxes de ingredientes, y al final una caja centrada con el botón rojo
"Enviar a mi Gmail" y un texto de ayuda que cambia según haya sesión o no.

### Modales
Dos ventanas centradas sobre fondo oscuro semitransparente con desenfoque,
botón "×" que gira levemente al pasar el cursor.
- \*\*Autenticación\*\*: tarjeta con borde superior coral grueso, pestañas
  internas "Iniciar sesión" / "Registrarse", campos en píldora (borde
  durazno que resalta en coral al enfocar), confirmación de contraseña solo
  en el registro, mensaje de error en rojo si falla, botón sólido coral.
- \*\*Planificación\*\*: misma estética, nombre del plato en campo de solo
  lectura, select de día, select de comida, botón ancho "Guardar en
  calendario".

### Animaciones
Vapor de la olla del logo (sube, rota levemente, se desvanece, desfasado
entre líneas), brillo pulsante del botón "¡Preparar!", giro y rebote del
emoji de carga, aparición tipo "pop" de chips y toasts, aparición suave de
modales y tarjeta de receta, flotación continua del icono del hero y de la
mascota fija.

### Accesibilidad y responsive
Contorno coral visible en foco de teclado para todo elemento interactivo.
Si el sistema tiene activada la preferencia de reducir movimiento, las
animaciones decorativas se desactivan. Diseño mobile-first: la zona de
trabajo pasa de una columna a tres, el planificador de una columna a siete,
la barra superior pasa a columna y la mascota se achica en pantallas muy
angostas.

### Pie de página
Línea pequeña y centrada con emoji de olla y el eslogan: "CociNADA · cocina
con lo que tienes, sin estrés".

========================================================================
## ARQUITECTURA SUGERIDA
========================================================================

- Un módulo de estado (`store`) que centraliza usuarios registrados, sesión
  activa, ingredientes cargados, historial de recetas y planificador
  semanal, y lo persiste en el navegador ante cada cambio.
- Un módulo de "IA" (`recipes-api`) que concentra la única llamada de red
  real de la app: arma el pedido según el modo activo (ingredientes /
  nombre de plato / sorpresa) y devuelve el resultado ya normalizado
  (nombre, tiempo, dificultad, ingredientes, pasos, consejo). Toda la app
  llama a este módulo, nunca hace la petición a mano en otro lado.
- Un módulo de emojis (`emoji-map`) con el diccionario de palabras clave en
  español y la función que resuelve el emoji de un ingrediente o plato.
- Un módulo de autenticación simulada (`auth`) separado del resto: guarda
  y valida usuarios/contraseñas localmente y expone quién está conectado.
  Dejá un comentario bien visible aclarando que esto no es autenticación
  real.
- La renderización de cada sección (barra, hero, paneles, resultados,
  planificador, lista de compras, modales) en piezas separadas, aunque
  vivan en el mismo archivo de página, para que sea fácil ubicar cada
  bloque descrito arriba.

========================================================================
## CRITERIOS DE ACEPTACIÓN (al final, verificá cada uno)
========================================================================

- \[ ] La página abre directo en el navegador sin backend propio ni base de
      datos externa.
- \[ ] Puedo registrarme con un correo de Gmail y contraseñas coincidentes;
      registrar el mismo correo dos veces da error; un correo que no sea de
      Gmail también da error.
- \[ ] Cerrar la pestaña y volver a abrirla mantiene mi sesión, mi
      historial y mi planificador tal como quedaron.
- \[ ] Los tres modos del hero muestran contenidos distintos en el panel
      central y cada uno produce una sugerencia o receta real al usar el
      botón correspondiente.
- \[ ] Si el servicio externo de IA falla o no hay conexión, aparece una
      notificación de error clara y la app no se rompe.
- \[ ] Puedo asignar un plato al planificador eligiendo día y comida, y
      puedo quitarlo después.
- \[ ] La lista de compras se arma sola con lo asignado, se puede filtrar
      por día y se puede copiar al portapapeles.
- \[ ] "Enviar a mi Gmail" pide iniciar sesión si no hay sesión activa.
- \[ ] El diseño respeta la paleta, las formas redondeadas, las sombras
      suaves y las animaciones descritas, sin verse genérico.
- \[ ] La app funciona bien en pantallas angostas: la zona de trabajo, el
      planificador y la barra de navegación se reorganizan como se describe.
- \[ ] El foco de teclado se ve claramente en todos los elementos
      interactivos, y las animaciones se desactivan con "reducir
      movimiento" activado.

========================================================================
## FUERA DE ALCANCE (NO hacer en esta etapa)
========================================================================

- Nada de backend propio, base de datos externa, Docker ni variables de
  entorno de producción más allá de la clave del servicio de IA.
- Nada de autenticación real: no hay que verificar identidad contra un
  proveedor externo ni recuperar contraseñas.
- Nada de pagos, roles de usuario ni panel de administración.
- Nada de compartir el planificador entre distintas cuentas: cada
  navegador guarda lo suyo.
```



