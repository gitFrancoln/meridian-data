# Meridian Data

Noticias publicadas y configuración de Meridian. La app no lee este repo directamente: la API (un Worker de Cloudflare, en el repo privado `meridian-api`) sirve lo que se publica acá en GitLab Pages.

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
- `.gitlab-ci.yml`: la ingesta programada y la publicación.
- Lo publicado (`public/`) y la memoria entre rondas (`state/`) viajan como artefactos de la última corrida buena; no se tocan a mano.

## Apagar algo en una emergencia

1. Editá `config/app.json` o `config/ingest.json` desde la web de GitLab (Edit) y hacé commit a `main`.
2. Un cambio en `app.json` se publica solo en un minuto. Un cambio en `ingest.json` se aplica en la ronda siguiente, y en la traducción en hasta 10 minutos.
3. Para volver atrás, revertí ese commit.

## Correr una ronda a mano

En Build → Pipelines → Run pipeline, cargá la variable `TIER` (0 son todos los países).

## Programación

| Nivel | Frecuencia |
|---|---|
| Todos (`TIER=0`) | Cada 6 horas |

GitLab puede demorar las rondas programadas unos minutos. Si una ronda falla, GitLab avisa por email. Los minutos de CI gratuitos son 400 por mes, por eso por ahora corre todo cada 6 horas.
