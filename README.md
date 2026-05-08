# Mi Blog

Blog personal bilingüe (ES/EN) construido con [Hugo](https://gohugo.io/) y el tema [Congo](https://github.com/jpanther/congo). Desplegado simultáneamente en Cloudflare Pages y GitHub Pages.

| Despliegue | URL |
|---|---|
| Cloudflare Pages | https://blog-cmj.pages.dev/ |
| GitHub Pages | https://edunavata.github.io/Blog/ |

## Stack

- **Hugo Extended 0.160.0** — generador de sitios estáticos
- **Congo v2.13** — tema (Git Submodule)
- **Cloudflare Pages** + **GitHub Pages** — hosting y CDN

## Requisitos

Hugo Extended >= 0.160.0. Descarga el binario desde [GitHub Releases](https://github.com/gohugoio/hugo/releases).

```bash
# Verificar instalación
hugo version  # debe incluir "extended"
```

## Desarrollo local

```bash
git clone --recurse-submodules git@github.com:edunavata/Blog.git
cd Blog
hugo server -D
```

El blog estará disponible en:
- Español: `http://localhost:1313/`
- English: `http://localhost:1313/en/`

> Si clonaste sin `--recurse-submodules`, ejecuta `git submodule update --init` para obtener el tema.

## Estructura

```
├── .github/workflows/
│   └── hugo.yaml            # Workflow de despliegue a GitHub Pages
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
├── layouts/                 # Overrides de plantillas del tema
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
hugo --minify
# El sitio generado queda en public/
```

## Despliegue

El sitio se despliega automáticamente en dos plataformas en cada push a `main`. Cada pipeline sobrescribe la `baseURL` en build time, así que el dominio configurado en `config/_default/config.toml` solo actúa como fallback para desarrollo local.

### Cloudflare Pages

Conectado vía dashboard. Configuración:

| Parámetro | Valor |
|---|---|
| Build command | `hugo --minify -b $CF_PAGES_URL` |
| Output directory | `public` |
| `HUGO_VERSION` (env var) | `0.160.0` |

El flag `-b $CF_PAGES_URL` aprovecha la variable inyectada por Cloudflare para que la `baseURL` sea dinámica — funciona tanto en producción como en preview deployments de branches/PRs.

> **Submódulos:** Cloudflare Pages clona los submódulos automáticamente si están registrados en `.gitmodules` y son públicos (es el caso del tema Congo).

### GitHub Pages

Configurado vía GitHub Actions en `.github/workflows/hugo.yaml`. Sigue la [guía oficial de Hugo para GitHub Pages](https://gohugo.io/host-and-deploy/host-on-github-pages/). El workflow:

- Se dispara en cada push a `main` (y manualmente vía `workflow_dispatch`).
- Sobrescribe la `baseURL` con `${{ steps.pages.outputs.base_url }}` (resuelve a `https://edunavata.github.io/Blog/`).
- El job `deploy` está gateado con `if: github.ref == 'refs/heads/main'` para que builds en otras ramas (vía `workflow_dispatch`) no publiquen en producción.

Para que funcione, en GitHub: **Settings → Pages → Source = GitHub Actions**.

## Personalización

- **Foto de autor:** coloca una imagen cuadrada (mínimo 256×256px) en `assets/img/author.jpg` y descomenta la línea `# image = "img/author.jpg"` en `config/_default/languages.es.toml` y `languages.en.toml`.
- **Color scheme:** cambia `colorScheme` en `config/_default/params.toml`. Opciones: `ocean`, `forest`, `fire`, `slate`, `sandstone`, `terminal`.
- **Nombre y bio:** edita `config/_default/languages.es.toml` y `languages.en.toml`.
- **Nunca edites** archivos dentro de `themes/congo/` — usa overrides en `layouts/` (ya hay uno: `layouts/_partials/functions/warnings.html`, fix de compatibilidad con Hugo ≥ 0.160).

## Licencia

[MIT](LICENSE).
