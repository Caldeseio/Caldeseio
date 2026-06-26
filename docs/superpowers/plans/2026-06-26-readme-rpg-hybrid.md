# GitHub Profile README — RPG Hybrid Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Rewrite el GitHub profile README de Caldeseio con estética RPG pixel-art (fondo oscuro con glow, ventanas con doble borde pixel, ratings de habilidad S/A/B, items de tech stack, badges de estado de proyectos) igualando el mockup `hybrid-v1.html` aprobado.

**Architecture:** Tres archivos — `banner-generator.html` (canvas HTML, usuario lo abre una vez y descarga el PNG), `images/skills.svg` (card estática de habilidades, SVG puro sin fuentes externas), `README.md` (ensambla todo: imagen del banner, SVG de skills, badges shields.io para tech stack, github-readme-stats para stats dinámicos, HTML para proyectos). Las limitaciones de GitHub README (sin CSS externo ni JS) se manejan usando imágenes para secciones estilizadas.

**Tech Stack:** HTML Canvas API, SVG, Markdown + HTML limitado de GitHub, shields.io, github-readme-stats

## Global Constraints
- Colores: Forest `#2C3A2E` · Green `#4F9D5B` · Ink `#272A26` · Graphite `#6E7268` · Bone `#F1EDE3` · BoneOnInk `#AFC3B2`
- Fuentes en canvas: `'Bricolage Grotesque'` (display) + `'JetBrains Mono'` (labels) vía Google Fonts
- Fuentes en SVG: system monospace (GitHub bloquea fuentes externas en SVG)
- GitHub username: `Caldeseio`
- LinkedIn: `https://www.linkedin.com/in/luis-calderon-962a57258`
- Email: `l.calderon@uempresarial.com`
- Sin terminología de juegos específicos
- Banner canvas: 1280×640 lógico, 2560×1280 físico (scale 2x)
- Referencia visual: `.superpowers/brainstorm/428-1782503475/content/hybrid-v1.html`

---

### Task 1: Reescribir `banner-generator.html` — diseño A-style

**Files:**
- Modify: `banner-generator.html`

**Interfaces:**
- Produces: PNG descargable `images/banner.png` (1280×640 @2x)

- [ ] **Step 1: Reemplazar `banner-generator.html` completo**

```html
<!DOCTYPE html>
<html lang="es">
<head>
<meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>Caldeseio — Banner Generator</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body {
    background: #0d1210; font-family: 'Bricolage Grotesque', sans-serif;
    color: #F1EDE3; min-height: 100vh;
    display: flex; flex-direction: column; align-items: center;
    justify-content: center; padding: 48px 24px; gap: 28px;
    -webkit-font-smoothing: antialiased;
  }
  .logo { display: flex; align-items: center; gap: 12px; }
  .logo-name { font-size: 22px; font-weight: 700; letter-spacing: -.03em; color: #F1EDE3; }
  .card {
    background: #111a12; border: 1px solid rgba(79,157,91,.25);
    border-radius: 12px; padding: 28px;
    display: flex; flex-direction: column; align-items: center; gap: 18px;
    max-width: 680px; width: 100%;
    box-shadow: 0 8px 48px rgba(0,0,0,.5);
  }
  canvas { width: 100%; height: auto; border-radius: 6px; display: block; }
  .meta {
    font-family: 'JetBrains Mono', monospace; font-size: 11px;
    letter-spacing: .1em; color: #6E7268;
  }
  button {
    font-family: 'JetBrains Mono', monospace; font-size: 13px;
    letter-spacing: .1em; background: #2C3A2E; color: #F1EDE3;
    border: 1px solid rgba(79,157,91,.4); border-radius: 999px;
    padding: 12px 32px; cursor: pointer; transition: background .15s, border-color .15s;
    width: 100%;
  }
  button:hover { background: #3d5c3f; border-color: #4F9D5B; color: #4F9D5B; }
</style>
</head>
<body>
<div class="logo">
  <svg width="32" height="32" viewBox="0 0 100 100" fill="none">
    <circle cx="50" cy="50" r="40" stroke="#F1EDE3" stroke-width="7"
      stroke-linecap="round" stroke-dasharray="190 251" transform="rotate(38 50 50)"/>
    <circle cx="78" cy="28" r="8.5" fill="#4F9D5B"/>
  </svg>
  <span class="logo-name">Caldeseio</span>
</div>

<div class="card">
  <canvas id="cv"></canvas>
  <div class="meta">1280 × 640 · GitHub Social / README banner</div>
  <button id="dlbtn">↓ Descargar banner.png</button>
  <div class="meta">Guardar como images/banner.png en el repo</div>
</div>

<script>
(function () {
  const C = {
    forest: '#2C3A2E', green: '#4F9D5B', bone: '#F1EDE3',
    boneOnInk: '#AFC3B2', graphite: '#6E7268',
  };
  const FB = "'Bricolage Grotesque', sans-serif";
  const FM = "'JetBrains Mono', monospace";
  const W = 1280, H = 640, S = 2;

  // Fonts
  const link = document.createElement('link');
  link.rel = 'stylesheet';
  link.href = 'https://fonts.googleapis.com/css2?family=Bricolage+Grotesque:opsz,wght@12..96,400;12..96,600;12..96,700&family=JetBrains+Mono:wght@400;500&display=swap';
  document.head.appendChild(link);

  async function waitFonts() {
    await Promise.allSettled([
      document.fonts.load(`700 80px "Bricolage Grotesque"`),
      document.fonts.load(`500 20px "JetBrains Mono"`),
    ]);
    await document.fonts.ready;
  }

  // Load mark SVG as Image
  function loadMark(stroke, node) {
    const svg = `<svg xmlns="http://www.w3.org/2000/svg" width="100" height="100" viewBox="0 0 100 100" fill="none">
      <circle cx="50" cy="50" r="40" stroke="${stroke}" stroke-width="7"
        stroke-linecap="round" stroke-dasharray="190 251" transform="rotate(38 50 50)"/>
      <circle cx="78" cy="28" r="8.5" fill="${node}"/>
    </svg>`;
    return new Promise((res, rej) => {
      const img = new Image();
      img.onload = () => res(img);
      img.onerror = rej;
      img.src = 'data:image/svg+xml;charset=utf-8,' + encodeURIComponent(svg);
    });
  }

  // Corner bracket (top-left, top-right, bottom-left, bottom-right)
  function drawCorners(x, pad, size, color) {
    x.strokeStyle = color; x.lineWidth = 2;
    const L = size;
    // TL
    x.beginPath(); x.moveTo(pad + L, pad); x.lineTo(pad, pad); x.lineTo(pad, pad + L); x.stroke();
    // TR
    x.beginPath(); x.moveTo(W - pad - L, pad); x.lineTo(W - pad, pad); x.lineTo(W - pad, pad + L); x.stroke();
    // BL
    x.beginPath(); x.moveTo(pad + L, H - pad); x.lineTo(pad, H - pad); x.lineTo(pad, H - pad - L); x.stroke();
    // BR
    x.beginPath(); x.moveTo(W - pad - L, H - pad); x.lineTo(W - pad, H - pad); x.lineTo(W - pad, H - pad - L); x.stroke();
  }

  // Pixel stars
  const STARS = [
    { x:.08, y:.12, r:2.5, c: C.boneOnInk },
    { x:.28, y:.08, r:3,   c: C.green },
    { x:.50, y:.18, r:2,   c: C.bone },
    { x:.72, y:.10, r:2.5, c: C.boneOnInk },
    { x:.90, y:.05, r:3,   c: C.green },
    { x:.06, y:.30, r:2,   c: C.bone },
    { x:.94, y:.35, r:2,   c: C.boneOnInk },
    { x:.62, y:.22, r:1.5, c: C.green },
    { x:.38, y:.28, r:2,   c: C.bone },
    { x:.82, y:.15, r:3,   c: C.boneOnInk },
  ];

  // Draw badge pill
  function drawBadge(x, label, cx, cy, color, textColor) {
    const tf = H * 0.024;
    x.font = `500 ${tf}px ${FM}`;
    x.letterSpacing = (tf * 0.12) + 'px';
    const tw = x.measureText(label).width;
    const pw = tw + tf * 2.4, ph = tf * 1.9;
    x.strokeStyle = color; x.lineWidth = Math.max(1, H * 0.002);
    x.fillStyle = 'rgba(79,157,91,0.08)';
    const rx = cx - pw / 2, ry = cy - ph / 2;
    // rounded rect
    x.beginPath();
    x.moveTo(rx + ph/2, ry);
    x.arcTo(rx + pw, ry, rx + pw, ry + ph, ph/2);
    x.arcTo(rx + pw, ry + ph, rx, ry + ph, ph/2);
    x.arcTo(rx, ry + ph, rx, ry, ph/2);
    x.arcTo(rx, ry, rx + pw, ry, ph/2);
    x.closePath();
    x.fill(); x.stroke();
    x.fillStyle = textColor;
    x.textBaseline = 'middle'; x.textAlign = 'center';
    x.fillText(label, cx, cy);
    x.letterSpacing = '0px'; x.textAlign = 'left';
  }

  async function draw() {
    await waitFonts();
    const markImg = await loadMark(C.bone, C.green);

    const cv = document.getElementById('cv');
    cv.width = W * S; cv.height = H * S;
    const x = cv.getContext('2d');
    x.scale(S, S);

    // ── Background gradient
    const bg = x.createLinearGradient(0, 0, 0, H);
    bg.addColorStop(0,   '#0d1a0f');
    bg.addColorStop(0.5, '#1a2e1c');
    bg.addColorStop(1,   '#0d1a0f');
    x.fillStyle = bg; x.fillRect(0, 0, W, H);

    // ── Radial glow
    const glow = x.createRadialGradient(W/2, H*0.55, 0, W/2, H*0.55, W*0.45);
    glow.addColorStop(0,   'rgba(79,157,91,0.20)');
    glow.addColorStop(0.5, 'rgba(79,157,91,0.06)');
    glow.addColorStop(1,   'transparent');
    x.fillStyle = glow; x.fillRect(0, 0, W, H);

    // Side glow left
    const gl = x.createRadialGradient(W*0.15, H*0.8, 0, W*0.15, H*0.8, W*0.3);
    gl.addColorStop(0, 'rgba(44,58,46,0.5)'); gl.addColorStop(1, 'transparent');
    x.fillStyle = gl; x.fillRect(0, 0, W, H);

    // ── Scan lines (very subtle)
    for (let y = 0; y < H; y += 4) {
      x.fillStyle = 'rgba(0,0,0,0.025)';
      x.fillRect(0, y + 3, W, 1);
    }

    // ── Pixel stars
    STARS.forEach(s => {
      x.fillStyle = s.c;
      x.beginPath();
      x.arc(s.x * W, s.y * H, s.r, 0, Math.PI * 2);
      x.fill();
    });

    // ── Corner brackets
    drawCorners(x, H * 0.055, H * 0.06, 'rgba(79,157,91,0.5)');

    // ── Logo mark (with glow)
    const markR = H * 0.10;
    const markBox = markR * 2.5;
    const cx = W / 2, markY = H * 0.33;
    x.save();
    x.shadowColor = '#4F9D5B'; x.shadowBlur = H * 0.06;
    x.drawImage(markImg, cx - markBox/2, markY - markBox/2, markBox, markBox);
    x.restore();

    // ── Title "Caldeseio"
    const titleFont = H * 0.115;
    x.save();
    x.shadowColor = '#4F9D5B'; x.shadowBlur = H * 0.08;
    x.fillStyle = C.bone;
    x.font = `700 ${titleFont}px ${FB}`;
    x.letterSpacing = (-0.04 * titleFont) + 'px';
    x.textBaseline = 'middle'; x.textAlign = 'center';
    x.fillText('Caldeseio', cx, H * 0.535);
    x.restore();
    x.letterSpacing = '0px'; x.textAlign = 'left';

    // ── Name
    const nameFont = H * 0.028;
    x.fillStyle = C.boneOnInk;
    x.font = `500 ${nameFont}px ${FM}`;
    x.letterSpacing = (nameFont * 0.22) + 'px';
    x.textBaseline = 'middle'; x.textAlign = 'center';
    x.fillText('LUIS CALDERÓN JIMÉNEZ', cx, H * 0.635);
    x.letterSpacing = '0px';

    // ── Role
    const roleFont = H * 0.026;
    x.fillStyle = C.green;
    x.font = `500 ${roleFont}px ${FM}`;
    x.letterSpacing = (roleFont * 0.16) + 'px';
    x.fillText('SOFTWARE DEVELOPER · DATA ANALYST', cx, H * 0.695);
    x.letterSpacing = '0px'; x.textAlign = 'left';

    // ── Badges
    const badgeY = H * 0.82;
    const gap = W * 0.095;
    drawBadge(x, 'FULL STACK', cx - gap, badgeY, 'rgba(79,157,91,0.35)', C.green);
    drawBadge(x, '● DISPONIBLE', cx, badgeY, 'rgba(79,157,91,0.5)', C.green);
    drawBadge(x, 'DATA', cx + gap, badgeY, 'rgba(79,157,91,0.35)', C.green);
  }

  // Download
  document.getElementById('dlbtn').addEventListener('click', async () => {
    const btn = document.getElementById('dlbtn');
    btn.textContent = '…';
    try {
      await draw();
      const cv = document.getElementById('cv');
      cv.toBlob(blob => {
        const url = URL.createObjectURL(blob);
        const a = document.createElement('a');
        a.href = url; a.download = 'banner.png';
        document.body.appendChild(a); a.click(); a.remove();
        setTimeout(() => URL.revokeObjectURL(url), 4000);
        btn.textContent = '✓ Descargado';
        setTimeout(() => { btn.textContent = '↓ Descargar banner.png'; }, 3000);
      }, 'image/png');
    } catch(err) {
      console.error(err);
      btn.textContent = '↓ Descargar banner.png';
      alert('Error al generar el PNG. Probá en Chrome.');
    }
  });

  // Preview on load
  waitFonts().then(draw).catch(console.error);
})();
</script>
</body>
</html>
```

- [ ] **Step 2: Verificar preview**
  - Abrir `banner-generator.html` en Chrome
  - Debe verse: fondo oscuro con gradiente, glow verde central, 10 estrellas pixel, brackets en esquinas, logo con glow, título "Caldeseio" grande, nombre y rol en mono, 3 badges

- [ ] **Step 3: Descargar y mover PNG**
  - Clic "↓ Descargar banner.png"
  - Mover el archivo a `images/banner.png` del repo

- [ ] **Step 4: Commit**
```bash
git add banner-generator.html images/banner.png
git commit -m "feat: redesign banner generator with A-style dark RPG aesthetic"
```

---

### Task 2: Crear `images/skills.svg` — card estática de habilidades

**Files:**
- Create: `images/skills.svg`

**Interfaces:**
- Produces: SVG imagen que se usa en README.md como `<img src="images/skills.svg">`
- Dimensiones: 520×170px, fondo `#0a0f0b`, sin fuentes externas (usa `monospace`)

- [ ] **Step 1: Crear `images/skills.svg`**

```svg
<svg xmlns="http://www.w3.org/2000/svg" width="520" height="170" viewBox="0 0 520 170">
  <!-- background -->
  <rect width="520" height="170" fill="#0a0f0b"/>
  <!-- outer pixel border -->
  <rect x="0.5" y="0.5" width="519" height="169" fill="none"
    stroke="rgba(79,157,91,0.35)" stroke-width="1"/>
  <!-- inner pixel border -->
  <rect x="3.5" y="3.5" width="513" height="163" fill="none"
    stroke="rgba(79,157,91,0.12)" stroke-width="1"/>

  <!-- section label -->
  <text x="14" y="22" font-family="monospace" font-size="9" letter-spacing="2"
    fill="#4F9D5B">◆ HABILIDADES</text>
  <line x1="120" y1="18" x2="506" y2="18" stroke="rgba(79,157,91,0.18)" stroke-width="1"/>

  <!-- FRONTEND block -->
  <rect x="12" y="30" width="238" height="58" rx="3" fill="rgba(44,58,46,0.25)"
    stroke="rgba(79,157,91,0.12)" stroke-width="1"/>
  <text x="22" y="47" font-family="monospace" font-size="8" letter-spacing="2" fill="#6E7268">FRONTEND</text>
  <text x="22" y="66" font-family="monospace" font-size="15" font-weight="600" fill="#F1EDE3">★ S</text>
  <rect x="22" y="76" width="216" height="3" rx="2" fill="rgba(44,58,46,0.6)"/>
  <rect x="22" y="76" width="205" height="3" rx="2" fill="url(#barGrad)"/>

  <!-- BACKEND block -->
  <rect x="258" y="30" width="250" height="58" rx="3" fill="rgba(44,58,46,0.25)"
    stroke="rgba(79,157,91,0.12)" stroke-width="1"/>
  <text x="268" y="47" font-family="monospace" font-size="8" letter-spacing="2" fill="#6E7268">BACKEND</text>
  <text x="268" y="66" font-family="monospace" font-size="15" font-weight="600" fill="#F1EDE3">★ S</text>
  <rect x="268" y="76" width="228" height="3" rx="2" fill="rgba(44,58,46,0.6)"/>
  <rect x="268" y="76" width="210" height="3" rx="2" fill="url(#barGrad)"/>

  <!-- DATA block -->
  <rect x="12" y="98" width="238" height="58" rx="3" fill="rgba(44,58,46,0.25)"
    stroke="rgba(79,157,91,0.12)" stroke-width="1"/>
  <text x="22" y="115" font-family="monospace" font-size="8" letter-spacing="2" fill="#6E7268">DATA &amp; BI</text>
  <text x="22" y="134" font-family="monospace" font-size="15" font-weight="600" fill="#F1EDE3">★ A+</text>
  <rect x="22" y="144" width="216" height="3" rx="2" fill="rgba(44,58,46,0.6)"/>
  <rect x="22" y="144" width="188" height="3" rx="2" fill="url(#barGrad)"/>

  <!-- DEVOPS block -->
  <rect x="258" y="98" width="250" height="58" rx="3" fill="rgba(44,58,46,0.25)"
    stroke="rgba(79,157,91,0.12)" stroke-width="1"/>
  <text x="268" y="115" font-family="monospace" font-size="8" letter-spacing="2" fill="#6E7268">DEVOPS</text>
  <text x="268" y="134" font-family="monospace" font-size="15" font-weight="600" fill="#F1EDE3">★ A</text>
  <rect x="268" y="144" width="228" height="3" rx="2" fill="rgba(44,58,46,0.6)"/>
  <rect x="268" y="144" width="178" height="3" rx="2" fill="url(#barGrad)"/>

  <defs>
    <linearGradient id="barGrad" x1="0" y1="0" x2="1" y2="0">
      <stop offset="0%" stop-color="#2C3A2E"/>
      <stop offset="100%" stop-color="#4F9D5B"/>
    </linearGradient>
  </defs>
</svg>
```

- [ ] **Step 2: Verificar SVG**
  - Abrir `images/skills.svg` en el navegador
  - Debe verse: fondo negro, doble borde verde tenue, 4 bloques con etiqueta, rating, barra de gradiente

- [ ] **Step 3: Commit**
```bash
git add images/skills.svg
git commit -m "feat: add static skills card SVG for README"
```

---

### Task 3: Reescribir `README.md` — layout híbrido completo

**Files:**
- Modify: `README.md`

**Interfaces:**
- Consumes: `images/banner.png`, `images/logo.svg`, `images/skills.svg`
- Produces: perfil GitHub renderizado con todas las secciones del diseño aprobado

- [ ] **Step 1: Reescribir `README.md` completo**

```markdown
<div align="center">
  <img src="images/banner.png" alt="Caldeseio" width="100%">
</div>

<br>

<div align="center">

<img src="images/logo.svg" width="48" alt="">

### Luis Calderón Jiménez
`Software Developer · Data Analyst`

*Ingeniería precisa, hecha con criterio propio.*

<br>

[![LinkedIn](https://img.shields.io/badge/LinkedIn-2C3A2E?style=flat-square&logo=linkedin&logoColor=4F9D5B)](https://www.linkedin.com/in/luis-calderon-962a57258)
&nbsp;
[![Portfolio](https://img.shields.io/badge/Portfolio-2C3A2E?style=flat-square&logo=vercel&logoColor=4F9D5B)](https://caldeseio.dev)
&nbsp;
[![Email](https://img.shields.io/badge/Email-2C3A2E?style=flat-square&logo=gmail&logoColor=4F9D5B)](mailto:l.calderon@uempresarial.com)

</div>

---

<img src="images/skills.svg" width="100%" alt="Habilidades">

<br>

**`◆ STACK TÉCNICO`**

![React](https://img.shields.io/badge/React-2C3A2E?style=flat-square&logo=react&logoColor=4F9D5B)
![Python](https://img.shields.io/badge/Python-2C3A2E?style=flat-square&logo=python&logoColor=4F9D5B)
![Laravel](https://img.shields.io/badge/Laravel-2C3A2E?style=flat-square&logo=laravel&logoColor=4F9D5B)
![Power BI](https://img.shields.io/badge/Power_BI-2C3A2E?style=flat-square&logo=powerbi&logoColor=4F9D5B)
![TypeScript](https://img.shields.io/badge/TypeScript-2C3A2E?style=flat-square&logo=typescript&logoColor=4F9D5B)
![SQL](https://img.shields.io/badge/SQL-2C3A2E?style=flat-square&logo=postgresql&logoColor=4F9D5B)
![Node.js](https://img.shields.io/badge/Node.js-2C3A2E?style=flat-square&logo=node.js&logoColor=4F9D5B)
![Pandas](https://img.shields.io/badge/Pandas-2C3A2E?style=flat-square&logo=pandas&logoColor=4F9D5B)
![Docker](https://img.shields.io/badge/Docker-2C3A2E?style=flat-square&logo=docker&logoColor=4F9D5B)
![Git](https://img.shields.io/badge/Git-2C3A2E?style=flat-square&logo=git&logoColor=4F9D5B)

---

**`◆ ACTIVIDAD`**

<div align="center">
  <img src="https://github-readme-stats.vercel.app/api?username=Caldeseio&show_icons=true&hide_border=true&bg_color=0a0f0b&text_color=F1EDE3&icon_color=4F9D5B&title_color=4F9D5B&rank_icon=github" height="160" alt="GitHub Stats">
  &nbsp;
  <img src="https://github-readme-stats.vercel.app/api/top-langs/?username=Caldeseio&layout=compact&hide_border=true&bg_color=0a0f0b&text_color=F1EDE3&title_color=4F9D5B&langs_count=6" height="160" alt="Top Languages">
</div>

---

**`◆ PROYECTOS`**

<table>
<tr>
<td width="33%" valign="top" style="border:0">

**`[ PROYECTO 1 ]`**

> Descripción breve del proyecto y qué problema resuelve.

![Tech](https://img.shields.io/badge/Tech-2C3A2E?style=flat-square)
![Stack](https://img.shields.io/badge/Stack-2C3A2E?style=flat-square)
![EN CURSO](https://img.shields.io/badge/EN_CURSO-4F9D5B?style=flat-square&labelColor=2C3A2E)

[Ver repo →](https://github.com/Caldeseio)

</td>
<td width="33%" valign="top" style="border:0">

**`[ PROYECTO 2 ]`**

> Descripción breve del proyecto y qué problema resuelve.

![Tech](https://img.shields.io/badge/Tech-2C3A2E?style=flat-square)
![Stack](https://img.shields.io/badge/Stack-2C3A2E?style=flat-square)
![COMPLETADO](https://img.shields.io/badge/COMPLETADO-6E7268?style=flat-square&labelColor=2C3A2E)

[Ver repo →](https://github.com/Caldeseio)

</td>
<td width="33%" valign="top" style="border:0">

**`[ PROYECTO 3 ]`**

> Descripción breve del proyecto y qué problema resuelve.

![Tech](https://img.shields.io/badge/Tech-2C3A2E?style=flat-square)
![Stack](https://img.shields.io/badge/Stack-2C3A2E?style=flat-square)
![EN CURSO](https://img.shields.io/badge/EN_CURSO-4F9D5B?style=flat-square&labelColor=2C3A2E)

[Ver repo →](https://github.com/Caldeseio)

</td>
</tr>
</table>

---

<div align="center">
  <br>
  <img src="https://img.shields.io/badge/●_DISPONIBLE_PARA_PROYECTOS-4F9D5B?style=flat-square&labelColor=2C3A2E" alt="Disponible">
  <br><br>
  <a href="https://caldeseio.dev"><img src="https://img.shields.io/badge/caldeseio.dev-2C3A2E?style=flat-square&logoColor=4F9D5B" alt="caldeseio.dev"></a>
  &nbsp;·&nbsp;
  <img src="https://img.shields.io/badge/@caldeseio-2C3A2E?style=flat-square&logo=github&logoColor=4F9D5B" alt="@caldeseio">
  <br><br>
  <sub><code>CALDESEIO · LUIS CALDERÓN JIMÉNEZ · 2026</code></sub>
</div>
```

- [ ] **Step 2: Verificar localmente**
  - Abrir `README.md` en el previsualizador de markdown del editor
  - Confirmar que el banner ocupa el ancho completo
  - Confirmar que el SVG de skills se ve correctamente
  - Confirmar que los badges de tech stack aparecen

- [ ] **Step 3: Commit y push**
```bash
git add README.md
git commit -m "feat: complete RPG hybrid profile README redesign"
git push origin main
```

- [ ] **Step 4: Verificar en GitHub**
  - Abrir `https://github.com/Caldeseio` en el navegador
  - Confirmar banner visible a ancho completo
  - Confirmar skills.svg renderiza correctamente
  - Confirmar badges de tech stack y stats cargan
  - Las cards de stats pueden tardar 5–10s la primera vez

---

## Self-Review

**Spec coverage:**
- [x] Banner A-style (dark bg, glow, stars, brackets, mark, title, badges) → Task 1
- [x] Skills card pixel-frame S/A/B → Task 2
- [x] Tech stack como items → Task 3 (shields.io badges)
- [x] Actividad GitHub → Task 3 (github-readme-stats)
- [x] Proyectos con EN CURSO / COMPLETADO → Task 3
- [x] Footer con disponibilidad → Task 3
- [x] Sin terminología de juegos → todos los tasks ✓

**Placeholder scan:** Los 3 proyectos son placeholders intencionales documentados — el usuario los completa con sus repos reales.

**Type consistency:** No hay funciones compartidas entre tasks. Las rutas de imágenes son consistentes (`images/banner.png`, `images/logo.svg`, `images/skills.svg`).
