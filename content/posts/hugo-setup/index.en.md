---
title: "How I built this blog with Hugo and Congo"
date: 2025-01-20
draft: false
description: "Quick guide on how I set up Hugo with the Congo theme and deployed to Cloudflare Pages."
summary: "Quick guide on how I set up Hugo with the Congo theme and deployed to Cloudflare Pages."
tags: ["hugo", "tutorial", "cloudflare"]
categories: ["tech"]
---

## The stack

This blog is built with:

- **Hugo** as a static site generator
- **Congo** as the theme
- **Cloudflare Pages** for deployment

### Why Hugo?

Speed. Hugo compiles hundreds of pages in milliseconds. It doesn't need Node.js, it has no heavy dependencies. It's a binary that just works.

### Why Congo?

It's clean, modern, and has native bilingual support. It also has dark mode, built-in search and a very organized configuration system.

### Deployment

Cloudflare Pages detects Hugo automatically. You just need to:

1. Connect your GitHub repository
2. Set the build command: `hugo --minify`
3. Wait 30 seconds

That simple.
