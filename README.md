# Agente de Búsqueda de Trabajo

### Un regalo para la comunidad

No armé esto para venderlo. Lo armé para mí, después de quince años como arquitecto de infraestructura y nube, cansado de perder horas buscando ofertas manualmente en vez de prepararme para las entrevistas que de verdad importaban. Decidí compartirlo porque sospecho que no soy el único al que le pasa esto — si a ti también te agota la búsqueda de empleo, aquí tienes la herramienta completa, gratis, para que la adaptes a tu propio perfil.

## ¿Qué hace por ti?

- Lee tu currículum y entiende qué tipo de puesto buscas.
- Sale a buscar en LinkedIn, Indeed, Glassdoor, Reddit y los boards que le indiques.
- Califica cada oferta del 0 al 100 contra tu perfil real — no contra uno genérico.
- Te redacta un borrador de carta de presentación por cada oferta que valga la pena.
- Te lo muestra todo en un dashboard donde marcas qué aplicaste y qué descartaste.

## Las piezas que hacen el trabajo

| Pieza | Para qué sirve |
|---|---|
| Firecrawl | Busca y scrapea la web, extrae ofertas individuales de cada página |
| Bright Data | Scrapea LinkedIn específicamente (Firecrawl se niega a tocarlo) |
| Claude | Lee, analiza, califica y redacta — el cerebro del asunto |
| FastAPI + un HTML | La cara visible: dashboard local, nada más que instalar |

## El truco que te ahorra dinero

La forma "correcta" de construir algo así sería llamar a la API de Claude con tu propia API key, pagando por cada token que entra y sale — cada currículum leído, cada oferta calificada, cada carta redactada. Con cientos de ofertas por corrida, la cuenta crece rápido.

En vez de eso, este proyecto usa el **Claude Code CLI en modo headless** (`claude -p`), invocado como un subproceso desde Python — el mismo Claude Code que usarías para programar, solo que sin interfaz. Si ya pagas una suscripción de Claude (Pro o Max), la aprovechas aquí también, en vez de sumar una factura de API aparte. No es la manera "de manual" de construir un agente en producción, pero para correrlo desde tu laptop un par de veces por semana, es la diferencia entre "ya está pagado" y ver tu tarjeta sangrar cada vez que buscas trabajo.

## Correrlo una vez por semana te cuesta $0

Claude ya lo cubre tu suscripción, como explico arriba. Las otras dos piezas — Firecrawl y Bright Data — también caben cómodamente dentro de sus capas gratuitas si lo corres semanalmente en vez de reventarlo todo el día:

- **La capa gratis de Firecrawl da 1,000 créditos al mes, sin tarjeta.** Una búsqueda cuesta 2 créditos por cada 10 resultados, y un scrape con extracción JSON (lo que usa este proyecto para sacar ofertas estructuradas de cada página) cuesta 5 créditos. Una corrida típica — unas 10 queries de búsqueda más el scrape de hasta 20 páginas — anda por los 120 créditos. Cuatro corridas al mes (una por semana) son unos 480 créditos: menos de la mitad de la capa gratis.
- **La capa gratis de Bright Data da 5,000 registros al mes, sin tarjeta**, y este proyecto solo la gasta en ofertas de LinkedIn. Incluso una corrida generosa que toque 20 páginas de LinkedIn son 80 registros al mes con cadencia semanal — lejísimos del límite.

Si lo corres a diario, en algún momento vas a chocar con el techo de Firecrawl. Si lo corres una vez por semana — de sobra para una búsqueda activa de empleo — no vas a gastar ni un peso más de lo que ya le pagas a Anthropic por Claude.

## Ponlo a andar

1. Instala Node.js (para el Claude CLI) y Python 3.10+.
2. `npm install -g @anthropic-ai/claude-code` y luego `claude` para iniciar sesión una vez.
3. Consigue tu propia key de [Firecrawl](https://firecrawl.dev) (obligatoria) y, si quieres mejores resultados de LinkedIn, de [Bright Data](https://brightdata.com/cp/start) (opcional, trial gratis de 5,000 registros/mes).
4. `pip install -r requirements.txt` y `cp .env.example .env` — llena tus keys ahí.
5. Crea tu propio `resume.md` (tu currículum en markdown) y edita `CLAUDE.md` con tus roles objetivo, tu stack y tus preferencias. Ninguno de los dos se sube a GitHub.
6. `python server.py` y abre `http://127.0.0.1:8000`. O, sin interfaz: `python agent.py`.

Los resultados quedan en `output/` (gitignored). Cada corrida gasta créditos reales de tus cuentas — revisa sus precios si lo vas a correr seguido.

```bash
pytest   # por si quieres confirmar que nada se rompió
```

## Genera tu resume.md desde un PDF, sin transcribir nada

No hace falta que copies tu currículum a mano. El Claude CLI puede leer un PDF directamente y escribirlo como markdown por ti. Con el proyecto ya instalado, abre una terminal ahí y corre:

```bash
claude
```

Y dentro de la sesión interactiva, pégale algo como:

```
Lee mi currículum en PDF que está en C:\ruta\a\tu\cv.pdf y escríbelo completo
como resume.md en este proyecto, en formato markdown. No resumas ni omitas nada:
quiero toda la experiencia, habilidades, certificaciones y educación tal como
están en el PDF.
```

Revisa el `resume.md` que te deje — a veces vale la pena ajustar algún detalle — y ya puedes correr el pipeline. Si tienes más de un CV (por ejemplo, versiones para distintos roles), puedes pedirle que combine lo relevante de varios PDFs en un solo `resume.md`.

## Configura tus preferencias de país y ciudad

Todo esto vive en la sección `## Preferences` de `CLAUDE.md` — es texto libre que Claude lee antes de calificar cada oferta, así que entiende instrucciones en lenguaje natural, no un formato rígido. Algunos ejemplos:

Solo remoto, sin importar el país:
```
## Preferences
- Remote only, anywhere in the world
```

Remoto o híbrido en ciudades específicas:
```
## Preferences
- Remote or hybrid roles based in Mexico City or Guadalajara
- Also open to fully remote roles from anywhere in Mexico
```

Excluir un país por completo (por ejemplo, si no tienes visa para trabajar ahí — este repo ya trae este caso resuelto para México/Canadá en `agent.py`, pero puedes adaptarlo a tu situación):
```
## Preferences
- Remote or hybrid roles based in Mexico or Canada
- Hard exclude: roles based in the United States, or requiring US work
  authorization/visa/citizenship — treat any such posting as an automatic
  skip regardless of how well the stack matches
```

Si tu exclusión es estricta (como la de arriba), vale la pena reforzarla también en `prompts/analyze.md` como una regla dura, no solo como preferencia — así no depende únicamente del criterio del modelo. Este repo ya tiene ese patrón implementado; solo cámbialo por tu país/situación.

## Licencia

MIT — ver [LICENSE](LICENSE). Es tuyo tanto como mío: bifúrcalo, cámbialo, mejóralo, y si le sirve a alguien más de la comunidad, mejor todavía.

— Alvar
