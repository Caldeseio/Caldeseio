# README RPG Hybrid Design — Caldeseio

**Fecha:** 2026-06-26  
**Estado:** Aprobado por usuario

## Resumen

Rediseño completo del GitHub profile README usando una estética RPG retro (pixel art, ventanas con doble borde, ratings de habilidad, items de stack). Diseño híbrido: encabezado dramático del estilo A + cuerpo codex del estilo C, sin terminología de ningún juego específico.

## Referencia visual

Mockup aprobado: `.superpowers/brainstorm/428-1782503475/content/hybrid-v1.html`

## Secciones del README

### 1. Banner (PNG, canvas-generated)
- Fondo: `#0d1a0f` → `#1a2e1c` gradiente
- Glow radial verde en el centro
- 10 estrellas pixel animadas (estáticas en PNG)
- Corner brackets decorativos (14×14px, `rgba(79,157,91,.5)`)
- Logo SVG con `filter: drop-shadow` verde
- Título "Caldeseio" 40px Bricolage Grotesque, glow verde
- "LUIS CALDERÓN JIMÉNEZ" 10px JetBrains Mono, color bone
- "SOFTWARE DEVELOPER · DATA ANALYST" 10px JetBrains Mono, color verde
- 3 badges: `FULL STACK` · `● DISPONIBLE` · `DATA`
- Dimensiones: 1280×640 @2x

### 2. Habilidades (SVG estático)
Frame con doble borde pixel (`rgba(79,157,91,.35)` externo, `rgba(79,157,91,.12)` a 3px). Grid 2×2:
- FRONTEND ★ S — barra 95%
- BACKEND  ★ S — barra 92%
- DATA & BI ★ A+ — barra 87%
- DEVOPS   ★ A — barra 78%

### 3. Stack Técnico (shields.io badges)
Badges en color `#2C3A2E` / `#4F9D5B`, style `flat-square`, con icono.
Destacados (hi): React, Python, Laravel, Power BI  
Normales: SQL, Docker, Node.js, Pandas, TypeScript, Git

### 4. GitHub Stats (github-readme-stats dinámico)
URL con colores de marca: `bg_color=2C3A2E&text_color=F1EDE3&icon_color=4F9D5B&title_color=4F9D5B&hide_border=true`

### 5. Proyectos (HTML table + badges)
3 cards con doble borde pixel simulado, border-left verde, badges EN CURSO / COMPLETADO.
Placeholders para que el usuario llene con sus repos reales.

### 6. Footer
`CALDESEIO · LUIS CALDERÓN JIMÉNEZ · 2026` · `●` pulsante

## Paleta de colores
- Bosque:   `#2C3A2E` — fondos oscuros
- Verde:    `#4F9D5B` — acentos, glow, items destacados
- Tinta:    `#272A26` — texto oscuro (no usado en README oscuro)
- Bone:     `#F1EDE3` — texto principal sobre oscuro
- BoneOnInk:`#AFC3B2` — texto secundario

## Archivos a producir
| Archivo | Método |
|---|---|
| `images/banner.png` | Usuario descarga de `banner-generator.html` |
| `images/logo.svg` | Ya existe (geometría pura) |
| `banner-generator.html` | Actualizar con diseño A-style |
| `README.md` | Reescribir completo |

## Restricciones GitHub README
- No CSS externo ni `<style>` → usar imágenes SVG/PNG para secciones estilizadas
- No JavaScript
- Sí: `<img>`, `<div align>`, `<table>`, `<details>`, shields.io
- Las secciones con pixel frame se implementan como HTML en el README usando tablas y shields.io, no SVGs separados (evita complejidad de mantenimiento)
