# Cloudflare Pages — configuración

Esta es la fuente de verdad de la configuración del dashboard de Cloudflare
Pages para este blog. Si algún parámetro cambia en el dashboard, actualiza
este archivo en el mismo PR.

Producción: <https://blog-cmj.pages.dev/>

## Build settings

| Parámetro              | Valor                                          |
|------------------------|------------------------------------------------|
| Framework preset       | None                                           |
| Build command          | `hugo --gc --minify -b "$CF_PAGES_URL"`        |
| Output directory       | `public`                                       |
| Root directory         | `/`                                            |
| Production branch      | `main`                                         |

`$CF_PAGES_URL` lo inyecta Cloudflare en runtime. En producción resuelve a
`https://blog-cmj.pages.dev/`; en previews, a la URL del deploy concreto. Esto
mantiene la `baseURL` correcta sin tocar `config/_default/config.toml`.

## Environment variables

Configuradas tanto para **Production** como **Preview**:

| Variable       | Valor      | Notas                                          |
|----------------|------------|------------------------------------------------|
| `HUGO_VERSION` | `0.160.0`  | Debe coincidir con `.tool-versions` y CI.      |

Si subes la versión de Hugo, actualiza los tres sitios a la vez:
`.tool-versions`, `.github/workflows/ci.yaml` (`HUGO_VERSION`) y esta variable.

## Submódulos

`Settings → Builds & deployments → Build configurations → Include submodules`
debe estar **activo**. El tema Congo vive en `themes/congo/` como submódulo
público; sin esta opción CF Pages clona el repo sin tema y el build falla.

## Branch deploys (previews)

- Cualquier push a una rama distinta de `main` genera un preview deploy.
- URLs: `https://<commit-sha-prefix>.blog-cmj.pages.dev/` (per-commit) y
  `https://<branch-slug>.blog-cmj.pages.dev/` (alias por rama).
- Los PRs reciben un comentario automático de Cloudflare con el enlace.
- Convención del proyecto: revisar el preview antes de mergear.

## Custom domain

Pendiente. Cuando se conecte un dominio propio:

1. `Workers & Pages → blog-cmj → Custom domains → Set up a custom domain`.
2. Crear el registro CNAME (o el que indique el wizard) en el DNS del dominio.
3. Esperar a que el certificado TLS quede en estado *Active*.
4. Anotar aquí el dominio final y la fecha del cambio.

## Headers HTTP

Servidos vía `static/_headers` (CF Pages lo lee automáticamente al deployar
`public/`). Cambios a headers van por PR como cualquier otro código — no se
configuran en el dashboard.
