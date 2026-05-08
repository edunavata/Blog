# Mi Blog

Blog personal bilingüe (ES/EN) construido con [Hugo](https://gohugo.io/) y el tema [Congo](https://github.com/jpanther/congo). Desplegado en [Cloudflare Pages](https://pages.cloudflare.com/).

## Stack

- **Hugo Extended 0.147.9+** — generador de sitios estáticos
- **Congo v2.13** — tema (Git Submodule)
- **Cloudflare Pages** — hosting y CDN

## Requisitos

Hugo Extended >= 0.147.9. Descarga el binario desde [GitHub Releases](https://github.com/gohugoio/hugo/releases).

```bash
# Verificar instalación
hugo version  # debe incluir "extended"
```

## Desarrollo local

```bash
git clone --recurse-submodules <url-del-repo>
cd mi-blog
hugo server -D
```

El blog estará disponible en:
- Español: `http://localhost:1313/`
- English: `http://localhost:1313/en/`

> Si clonaste sin `--recurse-submodules`, ejecuta `git submodule update --init` para obtener el tema.

## Estructura

```
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
├── layouts/                 # Overrides de plantillas (vacío por defecto)
├── themes/congo/            # Tema (Git Submodule, no editar)
└── wrangler.toml            # Configuración Cloudflare Pages
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

## Despliegue en Cloudflare Pages

| Parámetro | Valor |
|-----------|-------|
| Build command | `hugo --minify` |
| Output directory | `public` |
| `HUGO_VERSION` (env var) | `0.147.9` |

Conecta el repositorio desde el dashboard de Cloudflare Pages y configura los valores anteriores. El despliegue se lanza automáticamente en cada push a la rama principal.

## Personalización

- **Foto de autor:** coloca una imagen cuadrada (mínimo 256×256px) en `assets/img/author.jpg` y descomenta la línea `# image = "img/author.jpg"` en `config/_default/languages.es.toml` y `languages.en.toml`.
- **Color scheme:** cambia `colorScheme` en `config/_default/params.toml`. Opciones: `ocean`, `forest`, `fire`, `slate`, `sandstone`, `terminal`.
- **Nombre y bio:** edita `config/_default/languages.es.toml` y `languages.en.toml`.
- **Nunca edites** archivos dentro de `themes/congo/` — usa overrides en `layouts/` o `assets/`.
