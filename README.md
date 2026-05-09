# Mi Blog

Blog personal bilingüe (ES/EN) construido con [Hugo](https://gohugo.io/) y el
tema [Congo](https://github.com/jpanther/congo). Desplegado en Cloudflare
Pages.

| Despliegue       | URL                          |
|------------------|------------------------------|
| Cloudflare Pages | https://blog-cmj.pages.dev/  |

## Stack

- **Hugo Extended 0.160.0** — generador de sitios estáticos
- **Congo v2.13** — tema (Git Submodule)
- **Cloudflare Pages** — hosting, CDN y previews

## Requisitos

Hugo Extended 0.160.0. La versión está fijada en `.tool-versions`, así que con
[`mise`](https://mise.jdx.dev/) o [`asdf`](https://asdf-vm.com/) basta con:

```bash
mise install     # o: asdf install
hugo version     # debe incluir "extended" y la versión 0.160.0
```

Si prefieres instalar a mano, descarga el binario desde
[GitHub Releases](https://github.com/gohugoio/hugo/releases/tag/v0.160.0) y
asegúrate de que `hugo version` reporta exactamente `0.160.0` con `extended`.
La CI usa esa misma versión, y un mismatch local puede dejar pasar un build
que luego falla en CI.

## Desarrollo local

```bash
git clone --recurse-submodules git@github.com:edunavata/Blog.git
cd Blog
hugo server -D
```

El blog estará disponible en:
- Español: `http://localhost:1313/`
- English: `http://localhost:1313/en/`

> Si clonaste sin `--recurse-submodules`, ejecuta
> `git submodule update --init` para obtener el tema.

## Estructura

```
├── .github/workflows/
│   └── ci.yaml              # Build estricto + link check en PRs y main
├── .tool-versions           # Pin de Hugo (mise/asdf)
├── config/_default/
│   ├── config.toml          # Configuración base
│   ├── languages.es.toml    # Parámetros idioma español
│   ├── languages.en.toml    # Parámetros idioma inglés
│   ├── menus.es.toml        # Menú de navegación ES
│   ├── menus.en.toml        # Menú de navegación EN
│   └── params.toml          # Opciones del tema Congo
├── content/
│   ├── _index.es.md         # Homepage ES
│   ├── _index.en.md         # Homepage EN
│   ├── about/               # Página "Sobre mí"
│   └── posts/               # Artículos del blog
│       └── nombre-post/
│           ├── index.es.md  # Versión en español
│           └── index.en.md  # Versión en inglés
├── assets/img/              # Imágenes (foto de autor, etc.)
├── docs/CLOUDFLARE.md       # Configuración del dashboard de CF Pages
├── layouts/                 # Overrides de plantillas del tema
├── static/_headers          # Headers HTTP servidos por CF Pages
└── themes/congo/            # Tema (Git Submodule, no editar)
```

## Crear un nuevo post

```bash
mkdir -p content/posts/nombre-del-post
```

Crea `content/posts/nombre-del-post/index.es.md`:

```yaml
---
title: "Título del post"
date: 2025-01-15
draft: false
description: "Descripción corta para SEO y cards."
summary: "Resumen para la lista de posts."
tags: ["tag1", "tag2"]
categories: ["tech"]
---

Contenido en Markdown...
```

Duplica el archivo como `index.en.md` con el contenido traducido.

## Build de producción

```bash
hugo --gc --minify --panicOnWarning -b "https://blog-cmj.pages.dev/"
# El sitio generado queda en public/
```

`--panicOnWarning` es la misma flag que usa CI: cualquier deprecation o
warning del tema rompe el build. Si añade un warning legítimo, créa un
override en `layouts/` que lo arregle (ver "Actualizar el tema Congo").

## Despliegue

El sitio se despliega en **Cloudflare Pages** automáticamente en cada push:

- `main` → producción en https://blog-cmj.pages.dev/
- cualquier otra rama → preview deploy

La configuración exacta del dashboard (build command, env vars, submódulos)
está documentada en [`docs/CLOUDFLARE.md`](docs/CLOUDFLARE.md). Tratar ese
archivo como fuente de verdad: si cambias algo en el dashboard, actualízalo
en el mismo PR.

### Previews por rama / PR

- Cualquier push a una rama distinta de `main` genera un preview en
  `https://<commit-sha-prefix>.blog-cmj.pages.dev/` (per-commit) y un alias
  estable `https://<branch-slug>.blog-cmj.pages.dev/`.
- Cloudflare comenta automáticamente cada PR con el enlace del preview.
- Convención: **revisar el preview antes de mergear**, sobre todo cuando el
  cambio toca layouts, config o assets.

### CI

`.github/workflows/ci.yaml` corre en cada PR contra `main` y en cada push a
`main`. Hace:

1. Build con `--panicOnWarning` (idéntico al local).
2. Link check con [lychee](https://github.com/lycheeverse/lychee) en modo
   `--offline` sobre `public/` (solo enlaces internos).

CI **no despliega** — el deploy lo hace Cloudflare Pages. CI es la puerta de
calidad antes de que CF Pages cree el deploy real.

## Actualizar el tema Congo

```bash
cd themes/congo
git fetch origin
git checkout <tag-o-rama>     # ej. v2.14
cd ../..
hugo --gc --minify --panicOnWarning -b "https://blog-cmj.pages.dev/"
# Si OK:
git add themes/congo
git commit -m "chore(theme): bump Congo to vX.Y.Z"
```

### Overrides activos en `layouts/`

- `_partials/functions/warnings.html` — fix de compat con Hugo ≥ 0.160
  (Congo accede a `.Site.Author`, que ya no existe en versiones recientes).
- `_partials/sharing-links.html` — usa `hugo.Data.sharing` en lugar de
  `site.Data.sharing` (deprecado en 0.156).
- `_partials/translations.html` — usa `hugo.Languages` en lugar de
  `site.Languages` (deprecado en 0.156).

Cuando Congo publique una versión que arregle estos puntos upstream,
elimina el override correspondiente y verifica el build con
`--panicOnWarning`.

### Cuándo NO tocar `themes/congo/`

Nunca edites archivos dentro de `themes/congo/` directamente — es un
submódulo y los cambios se perderían en el siguiente bump. Toda
personalización va en `layouts/` o `assets/` del proyecto raíz, donde Hugo
los prioriza sobre los del tema.

## Personalización

- **Foto de autor:** coloca una imagen cuadrada (mínimo 256×256px) en
  `assets/img/author.jpg` y descomenta la línea `# image = "img/author.jpg"`
  en `config/_default/languages.es.toml` y `languages.en.toml`.
- **Color scheme:** cambia `colorScheme` en `config/_default/params.toml`.
  Opciones: `ocean`, `forest`, `fire`, `slate`, `sandstone`, `terminal`.
- **Nombre y bio:** edita `config/_default/languages.es.toml` y
  `languages.en.toml`.

## Licencia

[MIT](LICENSE).
