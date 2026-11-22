# Meridian Data

Noticias publicadas y configuración de Meridian. La app no lee este repo directamente: la API (un Worker de Cloudflare, en el repo privado `meridian-api`) sirve lo que se publica acá en GitHub Pages.

El código vive en los repos privados `meridian-api` y `meridian-web`. Acá solo hay datos públicos (titulares y enlaces) y configuración.

## Qué hay

- `config/ingest.json`: los interruptores de la ingesta y de la traducción.
  - `googleNewsEnabled`: usar o no Google News.
  - `disabledCountries`: países que no se procesan, por código (`"IS"`).
  - `disabledSources`: feeds que no se leen, por dirección.
  - `translation`: proveedores de traducción, topes diarios y el interruptor general.
- `config/app.json`: la configuración de la app. Se publica como `/api/v1/config.json`.
  - `routes`: las rutas que usa la app. Sacar una ruta apaga esa parte de la app.
  - `features`: funciones encendidas o apagadas.
  - `message`: un aviso para mostrar en la app, o `null`.
- `.github/workflows/publish.yml`: la ingesta programada y la publicación.
- La rama `data`: lo publicado (`site/`) y la memoria entre rondas (`state/`), en un solo commit que se reescribe en cada ronda. No se toca a mano.
- `.gitlab-ci.yml`: el pipeline anterior en GitLab, que queda como respaldo con la programación pausada.

## Apagar algo en una emergencia

1. Editá `config/app.json` o `config/ingest.json` desde la web de GitHub (lápiz de editar) y hacé commit a `main`.
2. Un cambio en `app.json` se publica solo en un par de minutos. Un cambio en `ingest.json` se aplica en la ronda siguiente, y en la traducción en hasta 5 minutos.
3. Para volver atrás, revertí ese commit.

## Correr una ronda a mano

En Actions → publish → Run workflow, elegí el nivel (0 son todos los países).

## Programación

| Nivel | Frecuencia |
|---|---|
| 1 | Cada 30 minutos |
| 2 | Cada 2 horas |
| 3 | Cada 6 horas |

El nivel de cada país está en el registro de medios de `meridian-api`. GitHub demora y a veces saltea las corridas programadas, así que el workflow se despierta cada 10 minutos, se fija qué nivel toca (la hora de la última ronda de cada nivel está en `state/schedule/` de la rama `data`) y corre ese, o termina en segundos si no toca ninguno. Una corrida salteada la cubre la siguiente. Si una ronda falla, GitHub avisa por email. Si el repo pasa 60 días sin actividad, GitHub pausa las programaciones y avisa; se reactivan con un clic.
