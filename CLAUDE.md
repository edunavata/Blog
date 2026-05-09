# Hugo Blog — AI Coding Rules

> Este archivo es leído automáticamente por Claude Code al inicio de cada sesión.
> También puede usarse como `.cursorrules`, `AGENTS.md` o contexto manual en chat.
> Última actualización: 2026-05-09

---

## ⚠️ REGLA CERO — BUSCAR ANTES DE INVENTAR (CRÍTICO)

La IA tiene conocimiento desactualizado o incorrecto sobre Hugo. Hugo cambia frecuentemente
con breaking changes entre versiones. Por tanto:

**ANTES de generar CUALQUIER código Hugo (templates, config, shortcodes, pipes):**

1. **BUSCAR en la documentación oficial** en https://gohugo.io/documentation/
2. **VERIFICAR** que la función/método/sintaxis existe para Hugo v0.161.1
3. **SI NO ESTÁS SEGURO** de una función, método o parámetro: decirlo explícitamente
   y buscar en https://gohugo.io/functions/ o https://gohugo.io/methods/
4. **NUNCA confiar en memoria de entrenamiento** para sintaxis de Hugo — la documentación
   oficial en gohugo.io es la ÚNICA fuente de verdad

### URLs de referencia oficial (buscar aquí SIEMPRE)

| Recurso | URL |
|---|---|
| Documentación completa | https://gohugo.io/documentation/ |
| Funciones (todas) | https://gohugo.io/functions/ |
| Métodos (todos) | https://gohugo.io/methods/ |
| Templates intro | https://gohugo.io/templates/introduction/ |
| Content organization | https://gohugo.io/content-management/organization/ |
| Front matter | https://gohugo.io/content-management/front-matter/ |
| Image processing | https://gohugo.io/content-management/image-processing/ |
| Taxonomías | https://gohugo.io/content-management/taxonomies/ |
| Permalinks config | https://gohugo.io/configuration/permalinks/ |
| Render hooks | https://gohugo.io/render-hooks/ |
| Hugo Pipes (assets) | https://gohugo.io/hugo-pipes/ |
| css.Build (NUEVO) | https://gohugo.io/functions/css/build/ |
| Template lookup order | https://gohugo.io/templates/lookup-order/ |
| Release notes | https://gohugo.io/news/ |
| GitHub releases | https://github.com/gohugoio/hugo/releases |

### Patrón obligatorio ante la duda

```
SI: conozco con certeza la función/método para Hugo 0.161.1 → usar directamente
SI: tengo duda sobre sintaxis, parámetros o existencia → BUSCAR en gohugo.io ANTES de responder
SI: no encuentro referencia → DECIRLO EXPLÍCITAMENTE, NO inventar
```

---

## 1. Contexto del proyecto

- **Generador:** Hugo (static site generator escrito en Go)
- **Versión EXACTA:** `v0.161.1-ea8f66a7ce+extended linux/amd64` (BuildDate: 2026-04-29)
- **Edición:** Extended (incluye Sass transpiler embebido — NOTA: LibSass embebido está DEPRECADO desde v0.153.0, usar Dart Sass)
- **Go version:** 1.26.1 (incluye fix de seguridad CVE-2026-27142 para html/template)
- **Formato de configuración:** `hugo.toml` (NO `config.toml`, que es legacy)
- **Formato de front matter:** YAML (delimitadores `---`)
- **Motor de Markdown:** Goldmark (ÚNICO motor soportado — Blackfriday fue eliminado en v0.100.0)
- **Idioma principal:** Español (`languageCode = 'es'`)
- **Alojamiento:** [especificar: Netlify / Vercel / Cloudflare Pages / GitHub Pages]

### Versión fijada en CI/CD

```yaml
# GitHub Actions — SIEMPRE fijar versión exacta
- name: Setup Hugo
  uses: peaceiris/actions-hugo@v2
  with:
    hugo-version: '0.161.1'
    extended: true
```

---

## 2. Novedades y deprecaciones de v0.153–v0.161 (IMPORTANTE)

Estas son las novedades y deprecaciones recientes que la IA probablemente NO conozca
de su entrenamiento. VERIFICAR siempre en las release notes oficiales.

### Nuevas funcionalidades disponibles

| Función | Desde | Descripción |
|---|---|---|
| `css.Build` | v0.158.0 | Bundling/transformación/minificación nativa de CSS vía esbuild. Reemplaza workflows complejos de PostCSS para muchos casos |
| `strings.ReplacePairs` | v0.158.0 | Reemplazo múltiple de strings de alto rendimiento |
| `css.TailwindCSS` | anterior | Procesamiento nativo de Tailwind CSS v4+ |
| Permalinks con target matchers | v0.161.0 | Configuración de permalinks más flexible usando el mismo sistema de target que cascade |
| Filename dimension identifiers | v0.161.0 | Nuevo esquema `_role_X_version_Y` para archivos de contenido y layouts |
| `hugo:vars` en CSS | v0.158+ | `@import "hugo:vars"` para inyectar variables Hugo en CSS nativo |
| Cascade en site config | v0.153.0 | Cascade front matter configurable desde `hugo.toml`, no solo desde `_index.md` |

### Deprecaciones ACTIVAS (NO USAR — serán eliminadas pronto)

| ❌ Deprecado | ✅ Usar en su lugar | Desde |
|---|---|---|
| `.URL` | `.RelPermalink` o `.Permalink` | Hace tiempo |
| `.Hugo` | `hugo` (función global) | Hace tiempo |
| `.RSSLink` | `.OutputFormats.Get "RSS"` | Hace tiempo |
| `.Site.Sites` / `.Page.Sites` | `hugo.Sites` | v0.161.0 |
| Edición `extended` (naming) | Edición estándar (la distinción se depreca) | v0.161.0 |
| Embedded LibSass (`toCSS`) | Dart Sass externo o `css.Build` para CSS puro | v0.153.0 |
| `Blackfriday` (config) | Goldmark (eliminado completamente en v0.100.0) | v0.100.0 |
| `config.toml` (nombre) | `hugo.toml` | v0.110.0 |
| `.GetPage "section" "posts"` | `.GetPage "/posts"` | Hace tiempo |
| `preserveTaxonomyNames` | Eliminado en v0.55, usar `.Page.Title` | v0.55 |

### Ejemplo: css.Build (NUEVO — preferir sobre PostCSS para CSS puro)

```go-html-template
{{/* ✅ NUEVO en v0.158+ — bundling nativo de CSS */}}
{{/* Ref: https://gohugo.io/functions/css/build/ */}}
{{ with resources.Get "css/main.css" }}
  {{ $opts := dict "minify" (not hugo.IsDevelopment) }}
  {{ with . | css.Build $opts }}
    <link rel="stylesheet" href="{{ .RelPermalink }}">
  {{ end }}
{{ end }}
```

---

## 3. Estructura de contenido — OBLIGATORIA

Todo el contenido DEBE usar **Leaf Bundles** (carpeta con `index.md`).
NUNCA crear archivos `.md` sueltos en una sección.

```
✅ CORRECTO
content/posts/mi-post/index.md
content/posts/mi-post/imagen.jpg

❌ INCORRECTO
content/posts/mi-post.md
static/images/mi-post-imagen.jpg
```

### Reglas de bundles
- `index.md` (sin guión bajo) = Leaf Bundle = página individual con sus recursos
- `_index.md` (con guión bajo) = Branch Bundle = sección/listado
- Las imágenes y assets del post van DENTRO de su carpeta bundle
- Los assets globales (CSS, JS, imágenes compartidas) van en `/assets/`
- SOLO archivos que no necesitan procesamiento van en `/static/` (favicon, robots.txt)

### Estructura de directorios

```
├── archetypes/
│   └── posts.md              # Archetype para nuevo contenido
├── assets/
│   ├── css/                   # Estilos procesables por Hugo Pipes / css.Build
│   └── js/                    # Scripts procesables por Hugo Pipes
├── content/
│   ├── posts/
│   │   ├── _index.md          # Branch bundle (listado de posts)
│   │   └── mi-post/
│   │       ├── index.md       # Leaf bundle (el post)
│   │       └── imagen.jpg     # Recurso local del post
│   └── _index.md              # Home page
├── data/                      # Archivos de datos (YAML/JSON/TOML)
├── layouts/
│   ├── _default/
│   │   ├── baseof.html        # Plantilla base
│   │   ├── list.html          # Listados
│   │   └── single.html        # Páginas individuales
│   ├── partials/              # Componentes reutilizables
│   ├── shortcodes/            # Shortcodes personalizados
│   ├── index.html             # Home page layout
│   └── _default/_markup/      # Render hooks
│       ├── render-link.html
│       └── render-image.html
├── static/                    # Archivos copiados sin procesar
├── hugo.toml                  # Configuración principal
└── CLAUDE.md                  # Este archivo
```

---

## 4. Front matter estándar de posts

Cada post DEBE incluir estos campos. Usar el archetype `archetypes/posts.md`.

```yaml
---
title: ""
date: 2026-01-01
draft: true
slug: ""           # OBLIGATORIO: URL estable independiente del nombre de archivo
description: ""    # Para meta description y Open Graph
summary: ""        # Para listados (si difiere del auto-summary)
tags: []
cover:
  image: ""
  alt: ""
---
```

### Reglas de front matter
- SIEMPRE definir `slug` explícitamente para desacoplar URL del nombre de archivo
- NUNCA añadir campos al front matter sin verificar que el archetype y las plantillas los usan
- Si necesitas un campo nuevo: actualizar archetype → actualizar plantilla → crear contenido

---

## 5. Go Templates — Reglas críticas

### El punto (.) y el contexto
- `.` (dot) representa el **contexto actual**, NO siempre la página
- Dentro de `range` y `with`, el `.` CAMBIA al elemento iterado
- Usar `$` para acceder al contexto raíz del template desde dentro de `range`/`with`
- Usar `$.Site`, `$.Title`, etc. cuando necesites datos de página dentro de un loop

```go-html-template
{{/* ✅ CORRECTO */}}
{{ range .Pages }}
  {{ .Title }}              {{/* . = página actual del loop */}}
  {{ $.Site.Title }}        {{/* $ = contexto raíz del template */}}
{{ end }}

{{/* ❌ INCORRECTO — . ya no es la página dentro de range */}}
{{ range .Params.tags }}
  {{ .Title }}              {{/* ERROR: un string no tiene .Title */}}
{{ end }}
```

### Partials
- SIEMPRE pasar el contexto al partial: `{{ partial "nombre.html" . }}`
- Preferir pasar un `dict` con solo lo necesario: `{{ partial "card.html" (dict "title" .Title "url" .RelPermalink) }}`
- Usar `partialCached` para partials cuyo output NO varía por página

```go-html-template
{{/* ✅ Output idéntico en todas las páginas → cachear */}}
{{ partialCached "css.html" . }}

{{/* ✅ Output varía por sección → cachear con variante */}}
{{ partialCached "sidebar.html" . .Section }}

{{/* ✅ Output varía por página → partial normal */}}
{{ partial "meta-tags.html" . }}
```

### Comentarios
- USAR `{{/* comentario */}}` (comentario Go Template)
- NUNCA usar `<!-- comentario con {{ código }} -->` (Hugo EJECUTA el código dentro)

### Whitespace
- Usar `{{-` y `-}}` para controlar líneas vacías en el HTML de salida
- `{{-` trim izquierda, `-}}` trim derecha

---

## 6. Procesamiento de assets

### Imágenes (Page Resources)
```go-html-template
{{/* Ref: https://gohugo.io/content-management/image-processing/ */}}
{{ with .Resources.GetMatch "portada.*" }}
  {{ $img := .Resize "800x webp" }}
  <img src="{{ $img.RelPermalink }}" alt="..." width="{{ $img.Width }}" height="{{ $img.Height }}" loading="lazy">
{{ end }}
```

### CSS — Tres opciones (de más simple a más compleja)

```go-html-template
{{/* Opción 1: css.Build (NUEVO v0.158+) — bundling nativo via esbuild */}}
{{/* Ref: https://gohugo.io/functions/css/build/ */}}
{{ with resources.Get "css/main.css" }}
  {{ $opts := dict "minify" (not hugo.IsDevelopment) }}
  {{ with . | css.Build $opts }}
    <link rel="stylesheet" href="{{ .RelPermalink }}">
  {{ end }}
{{ end }}

{{/* Opción 2: minify + fingerprint — CSS simple ya procesado */}}
{{ $css := resources.Get "css/main.css" | minify | fingerprint "sha512" }}
<link rel="stylesheet" href="{{ $css.RelPermalink }}" integrity="{{ $css.Data.Integrity }}">

{{/* Opción 3: Dart Sass (requiere instalación externa de dart-sass) */}}
{{/* Ref: https://gohugo.io/functions/css/sass/ */}}
{{ $scss := resources.Get "scss/main.scss" | css.Sass | minify | fingerprint }}
```

---

## 7. Antipatrones — NUNCA hacer esto

1. **NUNCA** crear archivos `.md` sueltos fuera de un bundle
2. **NUNCA** poner imágenes procesables en `/static/` (usar `/assets/` o el bundle)
3. **NUNCA** editar archivos dentro de `/themes/` directamente (usar lookup order)
4. **NUNCA** usar comentarios HTML para desactivar código Go Template
5. **NUNCA** llamar a `partial` sin pasar contexto (`.` o `dict`)
6. **NUNCA** declarar taxonomías en `hugo.toml` que no se usen activamente
7. **NUNCA** generar código con APIs deprecadas (ver tabla en sección 2)
8. **NUNCA** crear shortcodes para cosas que un Render Hook resuelve mejor
9. **NUNCA** usar `hugo-version: latest` en CI/CD — fijar `0.161.1`
10. **NUNCA** hardcodear URLs; usar `.RelPermalink`, `relref`, o `absURL`
11. **NUNCA** usar `.Site.Sites` — usar `hugo.Sites` (deprecado en v0.161.0)
12. **NUNCA** usar `toCSS` con LibSass embebido — usar Dart Sass externo o `css.Build`

---

## 8. Reglas anti-alucinación para la IA (CRÍTICO)

### Protocolo de verificación obligatorio

```
ANTES de escribir código Hugo:
┌─────────────────────────────────────────────────────────┐
│ 1. ¿Conozco esta función/método con CERTEZA para       │
│    Hugo v0.161.1?                                       │
│    → SÍ: proceder                                       │
│    → NO o DUDA: BUSCAR en gohugo.io ANTES de responder  │
├─────────────────────────────────────────────────────────┤
│ 2. ¿Existe esta función en la versión 0.161.1?          │
│    → Verificar en https://gohugo.io/functions/          │
│    → Verificar en https://gohugo.io/methods/            │
├─────────────────────────────────────────────────────────┤
│ 3. ¿Está deprecada?                                     │
│    → Verificar tabla de deprecaciones (sección 2)       │
│    → Verificar release notes recientes                  │
├─────────────────────────────────────────────────────────┤
│ 4. ¿No encuentro referencia?                            │
│    → DECIR: "No puedo confirmar que X exista en Hugo    │
│      v0.161.1. Recomiendo verificar en [URL]"           │
│    → NUNCA inventar sintaxis, parámetros o funciones    │
└─────────────────────────────────────────────────────────┘
```

### Errores frecuentes de la IA con Hugo (EVITAR)

| Error típico de la IA | Realidad en v0.161.1 |
|---|---|
| Usar `.URL` en templates | DEPRECADO → usar `.RelPermalink` |
| Configurar `Blackfriday` en config | ELIMINADO desde v0.100.0 |
| Usar `toCSS` sin Dart Sass | LibSass embebido DEPRECADO → usar Dart Sass o `css.Build` |
| Inventar funciones de Go Template | Verificar en https://gohugo.io/functions/ |
| Confundir `index.md` con `_index.md` | Leaf bundle vs Branch bundle — opuestos |
| Usar `.Site.Sites` | DEPRECADO en v0.161.0 → usar `hugo.Sites` |
| Generar config con `config.toml` | Convención moderna es `hugo.toml` |
| Asumir que `preserveTaxonomyNames` existe | Eliminado en v0.55 |
| Usar la sintaxis antigua de permalinks | v0.161.0 tiene nueva sintaxis con target matchers |

### Antes de modificar archivos existentes
1. **LEER** el archivo actual ANTES de proponer cambios
2. **NO re-implementar** lógica que ya existe en otro partial/shortcode
3. **VERIFICAR** que el cambio no rompe el contexto del `.` en templates existentes
4. **INDICAR** la ruta completa del archivo a crear/modificar

### Al proponer nuevas funcionalidades
1. **PRIMERO** buscar en gohugo.io si Hugo lo resuelve nativamente
2. **SEGUNDO** verificar si un Render Hook lo resuelve (más portable que shortcodes)
3. **TERCERO** proponer un partial/shortcode solo si no hay alternativa nativa
4. **SIEMPRE** indicar si la solución tiene impacto en tiempos de compilación
5. **SIEMPRE** incluir link a la documentación oficial de la función/método usado

---

## 9. Convenciones de nombrado

- **Archivos de contenido:** kebab-case (`mi-primer-post/index.md`)
- **Layouts y partials:** kebab-case (`render-link.html`, `post-card.html`)
- **Assets CSS/JS:** kebab-case (`main.css`, `search.js`)
- **Parámetros de front matter:** camelCase (`showReadingTime`, `coverImage`)
- **Parámetros de site config:** camelCase en `[params]`
- **Taxonomías:** plural en config, singular en front matter

---

## 10. Checklist antes de commit

- [ ] `hugo server` compila sin errores ni warnings
- [ ] `hugo 2>&1 | grep -i "deprecated"` no muestra deprecaciones nuevas
- [ ] Todo post nuevo tiene `slug` definido
- [ ] Las imágenes están dentro del bundle, no en `/static/`
- [ ] Los nuevos partials reciben el contexto correcto
- [ ] No hay APIs deprecadas en el código nuevo (verificar tabla sección 2)
- [ ] Los render hooks no rompen el Markdown estándar
- [ ] El HTML de salida no tiene exceso de líneas vacías
- [ ] La versión de Hugo en CI/CD está fijada a `0.161.1`

---

## 11. Comandos útiles

```bash
# Verificar versión instalada
hugo version
# Esperado: hugo v0.161.1-ea8f66a7ce+extended linux/amd64

# Servidor de desarrollo con drafts
hugo server -D --navigateToChanged

# Build de producción
hugo --minify --environment production

# Crear nuevo post (usa archetype)
hugo new content posts/nombre-del-post/index.md

# Verificar la estructura de salida
ls -la public/

# Verificar warnings (Hugo no falla, solo avisa)
hugo 2>&1 | grep -i "warn"

# Verificar deprecaciones en uso
hugo 2>&1 | grep -i "deprecated"
```