# Meridian Data

Noticias publicadas y configuración de Meridian. La app no lee este repo directamente: la API (un Worker de Cloudflare, en el repo privado `meridian-api`) sirve lo que se publica acá en GitHub Pages.

El código vive en los repos privados `meridian-api` y `meridian-web`. Acá solo hay datos públicos (titulares y enlaces) y configuración.

## Qué hay

- `config/ingest.json`: los interruptores de la ingesta y de la traducción.
  - `googleNewsEnabled`: usar o no Google News.
  - `disabledCountries`: países que no se procesan, por código (`"IS"`).
  - `disabledSources`: feeds que no se leen, por dirección.
  - `translation.providers`: proveedores de traducción en orden. Una lista vacía apaga la traducción.
  - `translation.dailyCharLimit`: tope diario de caracteres enviados a traducir.
- `config/app.json`: la configuración de la app. Se publica como `/api/v1/config.json`.
  - `routes`: las rutas que usa la app. Sacar una ruta apaga esa parte de la app.
  - `features`: funciones encendidas o apagadas.
  - `message`: un aviso para mostrar en la app, o `null`.
- `.github/workflows/publish.yml`: la ingesta programada y la publicación.
- La rama `data` (la escribe el workflow, no se toca a mano):
  - `site/`: lo que se publica en Pages.
  - `state/`: la memoria entre rondas.

## Apagar algo en una emergencia

1. Editá `config/app.json` o `config/ingest.json` desde la web de GitHub (el lápiz) y hacé commit a `main`.
2. Un cambio en `app.json` se publica solo en un minuto. Un cambio en `ingest.json` se aplica en la ronda siguiente, y en la traducción en hasta 10 minutos.
3. Para volver atrás, revertí ese commit.

## Correr una ronda a mano

En Actions → publish → Run workflow, elegí el nivel (0 son todos los países).

## Programación

| Nivel | Frecuencia |
|---|---|
| 1 | Cada 15 minutos |
| 2 | Cada hora |
| 3 | Cada 6 horas |

GitHub puede demorar las rondas programadas unos minutos. Si una ronda falla, GitHub avisa por email.
