# CociNADA

Asistente de cocina que sugiere qué preparar según los ingredientes que tenés en casa, busca la
receta de un plato puntual, o elige uno al azar según el momento del día y el estilo que pidas.
Con lo que vas cocinando arma un planificador semanal y una lista de compras.

Es un proyecto de frontend puro (un solo `index.html`) conectado a un flujo de n8n que hace de
backend: recibe la petición, arma el prompt y le pide a un modelo de lenguaje una respuesta en un
formato fijo.

---

## Correrlo local

No hace falta instalar nada ni levantar un servidor. Alcanza con abrir `index.html` en el
navegador.

Si preferís servirlo (recomendado, para evitar restricciones del navegador con `fetch` al abrir un
archivo directamente con `file://`):

```bash
npx serve .
```

**No hay base de datos.** Todo lo que la app necesita recordar entre sesiones — usuarios
registrados, sesión activa, planificador semanal — se guarda en el `localStorage` del navegador.
Borrar el `localStorage` o cambiar de navegador equivale a empezar de cero.

---

## La función de IA

Tres acciones de la app dependen de un modelo de lenguaje:

| Acción en la interfaz | Qué le pide al modelo |
| --- | --- |
| Cocina con lo que hay | 3 sugerencias de plato a partir de una lista de ingredientes |
| ¿Tienes un plato en mente? | La receta completa de un plato buscado por nombre |
| Deja que el asistente decida | Una receta al azar según momento del día y estilo elegido |

Las tres pasan por el mismo webhook y llegan con un campo `action` distinto en el cuerpo del
`POST`. Del lado del navegador no hay ninguna llave ni credencial: toda la lógica de IA vive en el
workflow de n8n, nunca en el HTML.

### Configurar tu propio backend

El repositorio no incluye un webhook funcional propio; hay que levantar el tuyo.

1. Creá una cuenta en [n8n.cloud](https://n8n.io/) (o corré n8n self-hosted).
2. Importá `n8n/cocinada-workflow.json`.
3. Conectá tus propias credenciales del modelo de lenguaje en los nodos *Chat Model* (el workflow
   original usa Google Gemini; podés cambiarlo por otro proveedor sin tocar el resto del flujo).
4. Activá el workflow y copiá la URL del webhook.
5. En `index.html`, reemplazá el valor de `N8N_WEBHOOK_URL` al inicio del `<script>`:

```js
const N8N_WEBHOOK_URL = 'https://tu-instancia.app.n8n.cloud/webhook/cocinada';
```

Esa URL queda visible en el código del lado del cliente, como cualquier valor en un frontend
estático. No es un problema en sí — es la dirección de un endpoint, no una credencial —, pero
cualquier llave de API (Gemini, servicio de email, etc.) tiene que quedar exclusivamente en n8n. Si
alguien más consigue tu URL y activa el workflow desde afuera, es tu cuota de la API la que se
gasta.

Si el webhook no responde o devuelve un error, la app lo informa con una notificación y no rompe el
resto de la interfaz.

---

## Publicarlo con GitHub Pages

1. En el repositorio: **Settings → Pages**.
2. En "Source", elegí la rama `main` y la carpeta raíz (`/`).
3. GitHub genera una URL pública del tipo `https://TU_USUARIO.github.io/cocinada/`.

No hay build ni paso de compilación: lo que está en `index.html` es lo que se publica.

---

## Cómo está organizado

```
cocinada/
  index.html                   toda la app: HTML, CSS y JS en un solo archivo
  n8n/
    cocinada-workflow.json     workflow importable: webhook + ruteo + llamadas al modelo
  README.md
  LICENSE
  .gitignore
```

### El workflow de n8n

El webhook recibe un `POST` con un campo `action`, y un nodo `Switch` enruta cada valor a su propia
rama:

| `action` | Rama |
| --- | --- |
| `get_dishes` | Sugiere 3 platos a partir de ingredientes |
| `get_recipe_by_name` | Genera la receta completa de un plato buscado por nombre |
| `get_random_recipe` | Genera una receta al azar según momento del día y estilo |
| `send_weekly_menu` | Formatea y envía por correo el menú semanal |

Cada rama usa un nodo *AI Agent* con un *Structured Output Parser* que fuerza la respuesta a un
JSON con un esquema fijo (nombre del plato, ingredientes con cantidad, tiempo, pasos). El frontend
nunca parsea texto libre: siempre recibe una estructura conocida.

### Sesión y autenticación

No hay servidor de autenticación. Registrarse y entrar quedan resueltos enteramente en el
navegador:

- Se acepta cualquier correo que termine en `@gmail.com`, sin verificarlo contra un servidor real.
- Los usuarios registrados, sus contraseñas y la sesión activa se guardan en `localStorage`, sin
  cifrar.
- No hay recuperación de contraseña ni verificación de correo.

Es intencional: el objetivo del proyecto es la interfaz y la integración con IA, no un sistema de
cuentas real. No uses una contraseña que uses en otro lado para probarlo.

## Qué no tiene, a propósito

- **Backend propio.** Todo el cómputo "inteligente" vive en n8n, no en un servidor que mantenga
  este repositorio.
- **Base de datos.** El estado de cada usuario vive en su propio navegador; no hay nada compartido
  entre dispositivos ni entre usuarios distintos.
- **Autenticación real.** Ver la sección anterior.
- **Tests.** Es un proyecto de clase.

## Licencia

MIT — ver [LICENSE](./LICENSE).
