---
title: "Cómo monté este blog con Hugo y Congo"
date: 2025-01-20
draft: false
description: "Guía rápida de cómo configuré Hugo con el tema Congo y despliegue en Cloudflare Pages."
summary: "Guía rápida de cómo configuré Hugo con el tema Congo y despliegue en Cloudflare Pages."
tags: ["hugo", "tutorial", "cloudflare"]
categories: ["tech"]
---

## El stack

Este blog está construido con:

- **Hugo** como generador de sitios estáticos
- **Congo** como tema
- **Cloudflare Pages** para el despliegue

### ¿Por qué Hugo?

Velocidad. Hugo compila cientos de páginas en milisegundos. No necesita Node.js, no tiene dependencias pesadas. Es un binario que funciona y punto.

### ¿Por qué Congo?

Es limpio, moderno, y tiene soporte bilingüe nativo. Además tiene modo oscuro, búsqueda integrada y un sistema de configuración muy organizado.

### Despliegue

Cloudflare Pages detecta Hugo automáticamente. Solo necesitas:

1. Conectar tu repositorio de GitHub
2. Configurar el comando de build: `hugo --minify`
3. Esperar 30 segundos

Así de simple.
