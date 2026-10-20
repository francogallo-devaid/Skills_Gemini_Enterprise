---
name: entel-connect-html-presentation
description: Guía una conversación para estructurar contenido y compila presentaciones interactivas HTML standalone con identidad Entel Connect (tabs, drawer, simulador, KPIs). Úsala cuando pidan una presentación HTML o interactiva de Entel; para .pptx usa la skill de decks.
---

# Entel Connect — Interactive HTML Presentation Compiler

Skill para diseñar y compilar presentaciones interactivas en **un único archivo `.html` autocontenido**, bajo la normativa del *Manual Maestro de Grounding (RAG) v2.0 — Entel Connect Interactive Presentation Engine*. Toda desviación de paleta, estructura, concisión o sintaxis es una **infracción de marca** o **falla de compilación**.

La skill opera en 4 fases: **Diagnóstico → Storyboard → Compilación → Auditoría QA**. No se escribe HTML hasta que el storyboard esté aprobado por el usuario (salvo que el usuario pida explícitamente saltar la conversación; en ese caso se asume la lectura más razonable y se declara en una línea).

---

## 0. Principios rectores (inmutables)

1. **Archivo monolítico standalone.** Un solo `.html`. Prohibido fragmentar, usar bundlers (Webpack, Vite, Node) o CSS/JS locales externos. Debe abrir con doble clic en cualquier navegador moderno. Solo se permiten las CDN de la sección 4.1.
2. **Cerebro vs. lienzo.** La IA decide síntesis, jerarquía y redacción ejecutiva. Proporciones, tipografía, color y microinteracciones las gobiernan Tailwind y los tokens oficiales. No inventar estilos fuera del catálogo.
3. **Presupuesto cerrado de láminas (Anti-Split).** Estándar de **5 a 6 láminas**, fijado en el diagnóstico. Una vez aprobado, es inamovible: no se agregan vistas ni se fragmenta un tema.
4. **Regla de las 18 palabras.** Cada viñeta, insight o bajada: máximo 18 palabras (y menos donde el layout lo exige, ver sección 5).
5. **Regla de las 8 palabras.** Títulos de lámina: comienzan con sustantivo o concepto rector, máximo 8 palabras.
6. **Código formateado.** Indentación de 2 espacios y saltos de línea reales. Nunca código minificado en una línea ni HTML dentro de acentos graves simples.
7. **Zero Leakage.** Nunca mostrar al usuario monólogos internos, frases en inglés tipo "Activating Layout Architect…", ni JSON intermedios. Todo lo visible va en español y es útil para el usuario.

---

## 1. Fase 1 — Diagnóstico conversacional

Objetivo: entender el contenido antes de diseñar. Hacer **como máximo 3–4 preguntas por turno**, agrupadas y concretas. Si el usuario ya entregó material (documento, notas, datos), extraer de ahí todo lo posible y preguntar solo lo que falte.

Información a obtener:

| Dato | Pregunta guía |
|---|---|
| Objetivo | ¿Qué decisión o acción esperas de la audiencia al terminar? |
| Audiencia | ¿Quién la verá? (gerencia, cliente, equipo técnico, capacitación) |
| Mensaje rector | Si la audiencia recordara una sola frase, ¿cuál sería? |
| Problema / fricción | ¿Qué duele hoy? ¿Cómo se ve la situación actual? |
| Solución / propuesta | ¿Qué cambia? ¿Cuáles son sus 3 pilares o fases? |
| Datos y KPIs | ¿Qué cifras reales hay? (valor, delta, contexto). Nunca inventar cifras: si faltan, usar marcadores `[dato pendiente]` y avisar. |
| Proceso / flujo | ¿Hay un flujo en 3 fases que convenga simular? |
| Metadatos portada | Área, fecha de emisión, clasificación (Confidencial / Uso interno). |
| N.º de láminas | 5 o 6 (por defecto 6, una por layout). |

Cierre de la fase: un resumen de 3–5 líneas del entendimiento y el número de láminas acordado.

---

## 2. Fase 2 — Storyboard (aprobación obligatoria)

Mapear el contenido **únicamente** sobre los 6 layouts aprobados (sección 5). Presentar al usuario una tabla así:

| # | Tab | Layout | Título (≤8 palabras) | Contenido clave |
|---|---|---|---|---|
| 1 | Portada | L1 Portada Ejecutiva | … | Subtítulo ≤16 palabras + 3 metadatos |
| 2 | Contexto | L2 Comparativa Dual | … | 3 fricciones vs. 3 soluciones |
| 3 | Pilares | L3 3 Columnas | … | 3 pilares |
| 4 | Análisis | L5 Gráfico + Insights | … | Serie de datos + KPI + 2 insights |
| 5 | Flujo | L6 Simulador | … | 3 fases + logs |
| 6 | Resultados | L4 KPIs | … | 2–4 métricas |

Arco narrativo recomendado: **Portada → Fricción vs. Solución → Cómo funciona (pilares) → Evidencia (gráfico) → Demostración (simulador) → Impacto (KPIs)**. Puede reordenarse si la historia lo pide, pero sin repetir layouts innecesariamente ni superar el presupuesto.

Redactar en el storyboard los textos finales ya calibrados (títulos, viñetas, insights) con conteo de palabras respetado. Preguntar: *"¿Apruebas este storyboard o ajustamos algo antes de compilar?"*. Tras la aprobación, el número de láminas queda congelado.

---

## 3. Fase 3 — Compilación

### 3.1 Formato de entrega

- **Si hay herramientas de archivo disponibles:** escribir el archivo (ej. `presentacion-entel-<tema>.html`) y entregarlo al usuario como archivo descargable. La respuesta visible: 1 línea de introducción + entrega del archivo + 1 línea de cierre (siguiente paso posible).
- **Si solo hay canal de chat:** emitir **todo** el archivo dentro de un bloque cercado formal:

  ````
  ```html
  <!DOCTYPE html>
  ...
  </html>
  ```
  ````
  Precedido por 1 línea de introducción y seguido de 1 línea de cierre. Nada más.

En ambos casos el archivo **inicia en `<!DOCTYPE html>` y termina en `</html>`**, sin truncamientos ni "…resto del código". Nunca comenzar en un `<div>` intermedio ni omitir `<head>`, CDN, fuentes o configuración Tailwind.

### 3.2 Arquitectura obligatoria (4 capas)

```
┌──────────────────────────────────────────────────────────────┐
│ 1. HEADER INSTITUCIONAL FIJO (sticky)                        │
│    Isotipo blindado + título + badge QA + tabs de navegación │
├──────────────────────────────────────────────────────────────┤
│ 2. MAIN STAGE — una <section id="view-slideN"> por lámina    │
├──────────────────────────────────────────────────────────────┤
│ 3. DRAWER DE DETALLE (inspector deslizante lateral)          │
├──────────────────────────────────────────────────────────────┤
│ 4. FOOTER INSTITUCIONAL INMUTABLE                            │
└──────────────────────────────────────────────────────────────┘
```

### 3.3 Interactividad mínima exigida

- Navegación por **tabs** en el header y por **teclado** (← →; `Esc` cierra el drawer).
- Indicador de progreso de lámina (ej. "3 / 6") y botones Anterior/Siguiente en el stage.
- **Drawer de detalle**: tarjetas, pilares, barras del gráfico y KPIs pueden abrir el drawer con información ampliada (el detalle del drawer puede superar 18 palabras, pero se redacta en párrafos breves de máx. 2–3 oraciones).
- Gráfico de L5 con hover (tooltip) y clic (drawer).
- Simulador de L6 con botón de disparo, estados reactivos por fase (pendiente → spinner → check verde `#42E8B4`) y consola oscura con `appendLog()`.
- Transición suave entre láminas (fade de 200–300 ms), respetando `prefers-reduced-motion`.

---

## 4. Identidad visual (tokens canónicos)

### 4.1 Dependencias exactas en `<head>`

```html
<!-- Tailwind CSS v3 via CDN -->
<script src="https://cdn.tailwindcss.com"></script>
<!-- FontAwesome Icons v6.4+ -->
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
<!-- Google Fonts: Barlow & JetBrains Mono -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
<link href="https://fonts.googleapis.com/css2?family=Barlow:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">
```

### 4.2 Configuración Tailwind (después del script de la CDN)

```html
<script>
  tailwind.config = {
    theme: {
      extend: {
        fontFamily: {
          sans: ['Barlow', 'Inter', 'sans-serif'],
          mono: ['JetBrains Mono', 'monospace'],
        },
        colors: {
          entel: {
            blue: '#005CFF',
            orange: '#FF3D00',
            navy: '#10069F',
            cyan: '#2FCBF1',
            mint: '#42E8B4',
            pink: '#FD6C98',
            charcoal: '#191919',
            grayBox: '#F4F6F9',
            grayBorder: '#A7A8AA',
          }
        }
      }
    }
  }
</script>
```

### 4.3 Paleta — uso exclusivo

| Elemento | Nombre oficial | Hex | Uso |
|---|---|---|---|
| Fondo principal | Blanco Puro | `#FFFFFF` | Base (`bg-slate-50` para contraste, `bg-white` en tarjetas) |
| Texto principal | Pantone Black C / Carbón | `#191919` | Párrafos, viñetas, encabezados de sección |
| Ejes y grillas | Pantone Cool Gray 6 C | `#A7A8AA` | Bordes suaves, divisores (`border-[#A7A8AA]/30`) |
| Tarjetas | Gris Contenedor | `#F4F6F9` | Superficies de módulos y pilares |
| Primario | Pantone 2132C / Azul Entel | `#005CFF` | Títulos de lámina, tab activa, botones primarios |
| Acento / alerta | Pantone 172C / Naranja | `#FF3D00` | Cifras KPI, acento del isotipo, alertas críticas |
| Secundario 1 | Pantone Blue 072 C / Azul Marino | `#10069F` | Alto contraste, encabezados técnicos, temas oscuros |
| Secundario 2 | Pantone 298C / Celeste | `#2FCBF1` | Subtítulos de apoyo, categorías, indicadores |
| Secundario 3 | Pantone 3385C / Verde Turquesa | `#42E8B4` | Cumplimiento positivo, éxito, validaciones |
| Secundario 4 | Pantone 190C / Rosa | `#FD6C98` | Advertencias secundarias, métricas de fricción |

Prohibido introducir colores fuera de esta tabla (excepto neutros `slate` de Tailwind para estructura, como en el boilerplate).

### 4.4 Tipografía

- **Primaria:** Barlow (fallback Inter, sans-serif).
- **Monospace:** JetBrains Mono — solo consolas, logs, JSON, identificadores, metadatos técnicos.
- **Títulos de lámina:** Barlow Bold/SemiBold/ExtraBold, `text-[#005CFF]`, ≤8 palabras.
- **Cifras KPI:** Barlow Bold/ExtraBold, `#FF3D00` o `#005CFF`, `text-4xl`/`text-5xl`.
- **Cuerpo:** Barlow Regular/Medium, `#191919`, `text-xs` o `text-sm`.

### 4.5 Isotipo blindado (copiar literal, nunca bitmap ni variaciones)

```html
<div class="flex items-center gap-2 select-none" aria-label="Entel Connect Logo">
  <div class="flex items-baseline font-bold font-sans tracking-tight text-xl leading-none">
    <span class="text-[#005CFF] text-2xl font-black">e</span>
    <span class="text-[#FF3D00] text-2xl font-black mx-[1px]">)</span>
    <span class="text-[#005CFF] font-semibold text-lg ml-0.5 tracking-normal">connect</span>
  </div>
</div>
```

### 4.6 Footer (texto exacto e inmutable)

`Información Confidencial | Propiedad de Entel Connect 2026`

---

## 5. Catálogo de layouts aprobados

Marcar cada texto calibrado con `data-max-words="N"` para habilitar la auditoría automática (sección 7.2).

### L1 — Portada Ejecutiva
Tarjeta central maximizada; badge superior de confidencialidad; H2 ≤8 palabras; subtítulo ≤16 palabras; 3 metadatos al pie (Área, Emisión, Clasificación).

```html
<section id="view-slide1" class="slide flex flex-col gap-6">
  <div class="bg-white border border-slate-200 rounded-3xl p-8 sm:p-12 shadow-sm text-center flex flex-col items-center">
    <span class="text-xs font-mono font-semibold px-3 py-1 rounded-full bg-blue-50 text-[#005CFF] border border-blue-200 mb-6">
      CONFIDENCIAL · [CONTEXTO] 2026
    </span>
    <h2 data-max-words="8" class="text-3xl sm:text-4xl lg:text-5xl font-extrabold text-[#005CFF] max-w-3xl leading-tight mb-4">
      [Título rector]
    </h2>
    <p data-max-words="16" class="text-[#191919]/70 max-w-xl text-sm leading-relaxed mb-8">
      [Subtítulo descriptivo]
    </p>
    <div class="grid grid-cols-1 sm:grid-cols-3 gap-4 w-full max-w-2xl pt-6 border-t border-slate-100 text-xs">
      <div><span class="text-slate-400 block font-mono">Área:</span><strong class="text-slate-700">[Área]</strong></div>
      <div><span class="text-slate-400 block font-mono">Emisión:</span><strong class="text-slate-700">[Mes Año]</strong></div>
      <div><span class="text-slate-400 block font-mono">Clasificación:</span><strong class="text-[#FF3D00]">Confidencial</strong></div>
    </div>
  </div>
</section>
```

### L2 — Comparativa Dual (Fricción vs. Solución)
`grid grid-cols-1 md:grid-cols-2`. **Exactamente 3 ítems por columna**. Títulos de ítem ≤4 palabras, bajadas ≤14 palabras. Columna de fricción con acentos `#FD6C98`/`#FF3D00`; columna de solución con `#005CFF`/`#42E8B4`.

```html
<div class="grid grid-cols-1 md:grid-cols-2 gap-6">
  <div class="bg-white rounded-2xl border border-[#A7A8AA]/30 p-6">
    <h3 class="text-sm font-bold text-[#FD6C98] uppercase tracking-wide mb-4"><i class="fa-solid fa-triangle-exclamation mr-2"></i>Situación actual</h3>
    <ul class="space-y-4">
      <li class="flex gap-3">
        <i class="fa-solid fa-xmark text-[#FD6C98] mt-1"></i>
        <div>
          <p data-max-words="4" class="font-semibold text-[#191919] text-sm">[Fricción]</p>
          <p data-max-words="14" class="text-xs text-[#191919]/70">[Bajada]</p>
        </div>
      </li>
      <!-- 2 ítems más -->
    </ul>
  </div>
  <!-- Columna "Con la solución": misma estructura, check en #42E8B4, 3 ítems -->
</div>
```

### L3 — 3 Columnas / Pilares
3 tarjetas simétricas `bg-[#F4F6F9]`: badge de nodo, título ≤4 palabras, viñeta ≤18 palabras. Cada tarjeta es un `<button>` que abre el drawer.

```html
<div class="grid grid-cols-1 md:grid-cols-3 gap-4">
  <button type="button" onclick="openDrawer('pilar1')" class="text-left bg-[#F4F6F9] rounded-2xl p-6 border border-transparent hover:border-[#005CFF] hover:-translate-y-0.5 transition">
    <span class="text-[10px] font-mono font-semibold px-2 py-0.5 rounded-full bg-white text-[#005CFF] border border-blue-200">NODO 01</span>
    <h3 data-max-words="4" class="mt-4 font-bold text-[#191919]">[Pilar]</h3>
    <p data-max-words="18" class="mt-2 text-xs text-[#191919]/70 leading-relaxed">[Explicación]</p>
    <span class="mt-4 inline-flex items-center gap-1 text-[11px] font-semibold text-[#005CFF]">Ver detalle <i class="fa-solid fa-arrow-right"></i></span>
  </button>
  <!-- 2 tarjetas más -->
</div>
```

### L4 — Métricas / Tarjetas KPI
Grid de 2 a 4 columnas; cifra `text-4xl`/`text-5xl` en `#FF3D00` o `#005CFF`; delta porcentual (positivo `#42E8B4`, negativo `#FD6C98`); bajada ≤12 palabras. Animar el conteo de la cifra al entrar a la lámina (count-up).

```html
<div class="grid grid-cols-2 lg:grid-cols-4 gap-4">
  <div class="bg-white rounded-2xl border border-[#A7A8AA]/30 p-5">
    <p class="text-[11px] font-mono text-slate-400 uppercase">[Métrica]</p>
    <p class="text-5xl font-extrabold text-[#FF3D00] mt-2"><span class="kpi-count" data-target="45">0</span>%</p>
    <span class="inline-block mt-2 text-[11px] font-semibold px-2 py-0.5 rounded-full bg-[#42E8B4]/15 text-[#10069F]"><i class="fa-solid fa-arrow-trend-up"></i> +12 pp</span>
    <p data-max-words="12" class="mt-3 text-xs text-[#191919]/70">[Contexto]</p>
  </div>
</div>
```

### L5 — Gráfico + Sidebar de Insights
`grid grid-cols-1 lg:grid-cols-12`: gráfico en `lg:col-span-8` (barras HTML o SVG interactivo, grillas `#A7A8AA`), sidebar `lg:col-span-4` con **1 KPI highlight en `#FF3D00`** y **exactamente 2 insights ≤16 palabras**. Barras con tooltip al hover y drawer al clic. Datos en un arreglo JS `chartData`, nunca inventados.

### L6 — Simulador de Flujo + Consola
Botón de disparo (`#005CFF`), 3 tarjetas de fase secuenciales con estado reactivo (`pendiente` gris → spinner `#2FCBF1` → check `#42E8B4`), consola oscura (`bg-[#191919]` o `bg-[#10069F]`, texto mono) con `appendLog()` y marca de tiempo. Botón para reiniciar. Bloquear el botón mientras corre.

---

## 6. Boilerplate canónico de compilación

Plantilla base obligatoria. Reemplazar los marcadores `[...]`, insertar las láminas del storyboard y ajustar `slideIds` y las tabs a la cantidad aprobada (5 o 6).

```html
<!DOCTYPE html>
<html lang="es" class="h-full bg-slate-50 text-slate-800">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Entel Connect | [Tema de la presentación]</title>

  <!-- CDNs Oficiales -->
  <script src="https://cdn.tailwindcss.com"></script>
  <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
  <link rel="preconnect" href="https://fonts.googleapis.com">
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin>
  <link href="https://fonts.googleapis.com/css2?family=Barlow:wght@300;400;500;600;700;800&family=JetBrains+Mono:wght@400;500;700&display=swap" rel="stylesheet">

  <script>
    tailwind.config = {
      theme: {
        extend: {
          fontFamily: {
            sans: ['Barlow', 'Inter', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          },
          colors: {
            entel: {
              blue: '#005CFF',
              orange: '#FF3D00',
              navy: '#10069F',
              cyan: '#2FCBF1',
              mint: '#42E8B4',
              pink: '#FD6C98',
              charcoal: '#191919',
              grayBox: '#F4F6F9',
              grayBorder: '#A7A8AA',
            }
          }
        }
      }
    }
  </script>

  <style>
    .custom-scrollbar::-webkit-scrollbar { width: 6px; height: 6px; }
    .custom-scrollbar::-webkit-scrollbar-track { background: #F1F5F9; }
    .custom-scrollbar::-webkit-scrollbar-thumb { background: #CBD5E1; border-radius: 4px; }
    .slide { animation: fadeIn 0.25s ease-out; }
    @keyframes fadeIn { from { opacity: 0; transform: translateY(6px); } to { opacity: 1; transform: none; } }
    @media (prefers-reduced-motion: reduce) { .slide { animation: none; } * { transition: none !important; } }
  </style>
</head>
<body class="h-full flex flex-col font-sans overflow-x-hidden bg-slate-50 text-slate-800">

  <!-- ================= 1. HEADER INSTITUCIONAL BLINDADO ================= -->
  <header class="border-b border-slate-200 bg-white/95 backdrop-blur-md sticky top-0 z-40 shadow-sm">
    <div class="max-w-7xl mx-auto px-4 sm:px-6 lg:px-8 h-16 flex items-center justify-between gap-4">
      <div class="flex items-center gap-4">
        <!-- Isotipo Blindado -->
        <div class="flex items-baseline font-bold font-sans tracking-tight text-xl leading-none select-none" aria-label="Entel Connect Logo">
          <span class="text-[#005CFF] text-2xl font-black">e</span>
          <span class="text-[#FF3D00] text-2xl font-black mx-[1px]">)</span>
          <span class="text-[#005CFF] font-semibold text-lg ml-0.5 tracking-normal">connect</span>
        </div>
        <div class="h-6 w-px bg-slate-200 hidden sm:block"></div>
        <div class="hidden md:block">
          <h1 class="font-bold text-slate-900 text-sm leading-tight flex items-center gap-2">
            [Nombre corto de la presentación]
            <span class="text-[10px] font-semibold px-2 py-0.5 rounded-full bg-blue-50 text-[#005CFF] border border-blue-200">QA 2.0</span>
          </h1>
          <p class="text-[11px] text-slate-500">[Bajada del header]</p>
        </div>
      </div>

      <!-- Navegación por Tabs -->
      <nav class="flex items-center gap-1 bg-slate-100 p-1 rounded-xl border border-slate-200 overflow-x-auto custom-scrollbar" id="slideNav" aria-label="Láminas">
        <button onclick="switchTab('slide1')" id="tab-slide1" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">Portada</button>
        <button onclick="switchTab('slide2')" id="tab-slide2" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">[Tab 2]</button>
        <button onclick="switchTab('slide3')" id="tab-slide3" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">[Tab 3]</button>
        <button onclick="switchTab('slide4')" id="tab-slide4" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">[Tab 4]</button>
        <button onclick="switchTab('slide5')" id="tab-slide5" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">[Tab 5]</button>
        <button onclick="switchTab('slide6')" id="tab-slide6" class="nav-btn px-3 py-1.5 rounded-lg text-xs font-semibold text-slate-600 whitespace-nowrap transition">[Tab 6]</button>
      </nav>

      <div class="hidden lg:flex items-center gap-2 text-xs font-mono bg-blue-50 text-[#005CFF] border border-blue-200 px-3 py-1.5 rounded-lg">
        <i class="fa-solid fa-shield-halved text-xs"></i>
        <span>Entel 2026</span>
      </div>
    </div>
  </header>

  <!-- ================= 2. MAIN STAGE ================= -->
  <main class="flex-1 max-w-7xl w-full mx-auto p-4 sm:p-6 lg:p-8 flex flex-col gap-6">

    <!-- LÁMINA 1: PORTADA (L1) -->
    <section id="view-slide1" class="slide flex flex-col gap-6">
      <!-- Ver componente L1 -->
    </section>

    <!-- LÁMINAS 2..N: insertar con class="slide hidden flex-col gap-6" -->
    <!-- Cada lámina interior abre con su título: -->
    <!-- <h2 data-max-words="8" class="text-2xl sm:text-3xl font-bold text-[#005CFF]">[Título]</h2> -->

    <!-- Controles de lámina -->
    <div class="flex items-center justify-between pt-2">
      <button onclick="prevSlide()" id="btnPrev" class="px-4 py-2 rounded-lg text-xs font-semibold border border-slate-200 bg-white text-slate-600 hover:border-[#005CFF] disabled:opacity-40"><i class="fa-solid fa-arrow-left mr-1"></i> Anterior</button>
      <span id="slideCounter" class="text-xs font-mono text-slate-400">1 / 6</span>
      <button onclick="nextSlide()" id="btnNext" class="px-4 py-2 rounded-lg text-xs font-semibold bg-[#005CFF] text-white hover:bg-[#10069F] disabled:opacity-40">Siguiente <i class="fa-solid fa-arrow-right ml-1"></i></button>
    </div>
  </main>

  <!-- ================= 3. DRAWER DE DETALLE ================= -->
  <div id="drawerOverlay" class="fixed inset-0 bg-[#191919]/40 z-50 hidden" onclick="closeDrawer()"></div>
  <aside id="drawer" class="fixed top-0 right-0 h-full w-full sm:w-[420px] bg-white z-50 shadow-2xl translate-x-full transition-transform duration-300 flex flex-col" aria-hidden="true">
    <div class="flex items-center justify-between px-6 h-16 border-b border-slate-200">
      <span id="drawerTag" class="text-[10px] font-mono font-semibold px-2 py-0.5 rounded-full bg-blue-50 text-[#005CFF] border border-blue-200">DETALLE</span>
      <button onclick="closeDrawer()" class="text-slate-400 hover:text-[#191919]" aria-label="Cerrar"><i class="fa-solid fa-xmark text-lg"></i></button>
    </div>
    <div class="p-6 overflow-y-auto custom-scrollbar flex-1">
      <h3 id="drawerTitle" class="text-xl font-bold text-[#005CFF]"></h3>
      <div id="drawerBody" class="mt-4 text-sm text-[#191919] leading-relaxed space-y-3"></div>
    </div>
  </aside>

  <!-- ================= 4. FOOTER INSTITUCIONAL INMUTABLE ================= -->
  <footer class="border-t border-slate-200 bg-white py-3 px-6 text-center text-xs text-slate-500 font-mono">
    Información Confidencial | Propiedad de Entel Connect 2026
  </footer>

  <!-- ================= JAVASCRIPT DE CONTROL ================= -->
  <script>
    const slideIds = ['slide1', 'slide2', 'slide3', 'slide4', 'slide5', 'slide6'];
    let currentSlideIndex = 0;

    // Contenido del drawer: clave -> { tag, title, body (HTML) }
    const drawerContent = {
      // pilar1: { tag: 'NODO 01', title: '[Pilar]', body: '<p>[Detalle]</p>' },
    };

    function switchTab(tabId) {
      currentSlideIndex = slideIds.indexOf(tabId);
      slideIds.forEach(id => {
        const view = document.getElementById(`view-${id}`);
        const btn = document.getElementById(`tab-${id}`);
        if (!view || !btn) return;
        const active = id === tabId;
        view.classList.toggle('hidden', !active);
        view.classList.toggle('flex', active);
        btn.classList.toggle('bg-[#005CFF]', active);
        btn.classList.toggle('text-white', active);
        btn.classList.toggle('shadow-sm', active);
        btn.classList.toggle('text-slate-600', !active);
        btn.setAttribute('aria-current', active ? 'page' : 'false');
      });
      document.getElementById('slideCounter').textContent = `${currentSlideIndex + 1} / ${slideIds.length}`;
      document.getElementById('btnPrev').disabled = currentSlideIndex === 0;
      document.getElementById('btnNext').disabled = currentSlideIndex === slideIds.length - 1;
      runCountUps(document.getElementById(`view-${tabId}`));
      window.scrollTo({ top: 0, behavior: 'smooth' });
    }

    function nextSlide() {
      if (currentSlideIndex < slideIds.length - 1) switchTab(slideIds[currentSlideIndex + 1]);
    }

    function prevSlide() {
      if (currentSlideIndex > 0) switchTab(slideIds[currentSlideIndex - 1]);
    }

    function openDrawer(key) {
      const item = drawerContent[key];
      if (!item) return;
      document.getElementById('drawerTag').textContent = item.tag || 'DETALLE';
      document.getElementById('drawerTitle').textContent = item.title;
      document.getElementById('drawerBody').innerHTML = item.body;
      document.getElementById('drawerOverlay').classList.remove('hidden');
      const drawer = document.getElementById('drawer');
      drawer.classList.remove('translate-x-full');
      drawer.setAttribute('aria-hidden', 'false');
    }

    function closeDrawer() {
      document.getElementById('drawerOverlay').classList.add('hidden');
      const drawer = document.getElementById('drawer');
      drawer.classList.add('translate-x-full');
      drawer.setAttribute('aria-hidden', 'true');
    }

    function runCountUps(scope) {
      if (!scope) return;
      scope.querySelectorAll('.kpi-count').forEach(el => {
        const target = parseFloat(el.dataset.target);
        const decimals = (el.dataset.target.split('.')[1] || '').length;
        const start = performance.now();
        const duration = 900;
        function step(now) {
          const p = Math.min((now - start) / duration, 1);
          el.textContent = (target * (1 - Math.pow(1 - p, 3))).toFixed(decimals);
          if (p < 1) requestAnimationFrame(step);
        }
        requestAnimationFrame(step);
      });
    }

    // Consola del simulador (L6)
    function appendLog(message, level = 'info') {
      const consoleEl = document.getElementById('simConsole');
      if (!consoleEl) return;
      const colors = { info: 'text-[#2FCBF1]', ok: 'text-[#42E8B4]', warn: 'text-[#FD6C98]', err: 'text-[#FF3D00]' };
      const time = new Date().toLocaleTimeString('es-CL', { hour12: false });
      const line = document.createElement('div');
      line.innerHTML = `<span class="text-slate-500">[${time}]</span> <span class="${colors[level] || colors.info}">${level.toUpperCase()}</span> ${message}`;
      consoleEl.appendChild(line);
      consoleEl.scrollTop = consoleEl.scrollHeight;
    }

    // Simulador: fases con estado pendiente -> en curso -> completado
    const simPhases = [
      // { id: 'phase1', logs: ['[log 1]', '[log 2]'] },
    ];
    const sleep = ms => new Promise(r => setTimeout(r, ms));

    function setPhaseState(id, state) {
      const icon = document.querySelector(`#${id} .phase-icon`);
      const card = document.getElementById(id);
      if (!icon || !card) return;
      const map = {
        idle: '<i class="fa-regular fa-circle text-slate-300"></i>',
        run: '<i class="fa-solid fa-spinner fa-spin text-[#2FCBF1]"></i>',
        done: '<i class="fa-solid fa-circle-check text-[#42E8B4]"></i>',
      };
      icon.innerHTML = map[state];
      card.classList.toggle('border-[#005CFF]', state === 'run');
    }

    async function runSimulation() {
      const btn = document.getElementById('btnSimulate');
      if (!btn || btn.disabled) return;
      btn.disabled = true;
      document.getElementById('simConsole').innerHTML = '';
      simPhases.forEach(p => setPhaseState(p.id, 'idle'));
      appendLog('Iniciando simulación…');
      for (const phase of simPhases) {
        setPhaseState(phase.id, 'run');
        for (const msg of phase.logs) {
          await sleep(500);
          appendLog(msg);
        }
        setPhaseState(phase.id, 'done');
        appendLog(`Fase ${phase.id} completada`, 'ok');
      }
      appendLog('Flujo finalizado sin errores', 'ok');
      btn.disabled = false;
    }

    document.addEventListener('keydown', (e) => {
      if (e.key === 'Escape') return closeDrawer();
      if (e.target.closest('input, textarea')) return;
      if (e.key === 'ArrowRight') nextSlide();
      if (e.key === 'ArrowLeft') prevSlide();
    });

    switchTab('slide1');
  </script>
</body>
</html>
```

---

## 7. Fase 4 — Auditoría QA (antes de entregar)

### 7.1 Checklist RAG 2.0 (binaria; todo debe ser "sí")

- [ ] **Entrega:** archivo `.html` entregado, o código completo dentro de ```` ```html ```` … ```` ``` ```` si solo hay chat.
- [ ] **Sin línea única:** indentación de 2 espacios y saltos de línea reales; nada minificado.
- [ ] **Standalone íntegro:** inicia en `<!DOCTYPE html>`, termina en `</html>`, sin truncamientos; CDN, fuentes y `tailwind.config` presentes.
- [ ] **Isotipo blindado:** `e` en `#005CFF`, `)` en `#FF3D00`, `connect` en `#005CFF`, en el header, vectorial (texto), sin alteraciones.
- [ ] **Calibración textual:** viñetas e insights ≤18 palabras (y límites específicos del layout: L1 subtítulo ≤16, L2 títulos ≤4 / bajadas ≤14, L3 títulos ≤4, L4 bajadas ≤12, L5 insights ≤16).
- [ ] **Títulos:** ≤8 palabras, inician con sustantivo/concepto, Barlow Bold, `#005CFF`.
- [ ] **Presupuesto:** número de láminas = el aprobado (5–6); tabs y `slideIds` coinciden con las secciones.
- [ ] **Layouts:** cada lámina usa uno de L1–L6; L2 con 3+3 ítems; L5 con 1 KPI y exactamente 2 insights.
- [ ] **Paleta:** solo tokens canónicos (más neutros slate estructurales).
- [ ] **Interactividad:** tabs, flechas de teclado, Esc, drawer, hover/clic en gráfico, simulador con `appendLog` funcionando.
- [ ] **Datos:** ninguna cifra inventada; pendientes marcados como `[dato pendiente]` y avisados al usuario.
- [ ] **Cero fugas:** sin monólogos en inglés ni JSON crudo en el mensaje visible.
- [ ] **Footer exacto:** `Información Confidencial | Propiedad de Entel Connect 2026`.

### 7.2 Validación automática (cuando hay shell disponible)

Ejecutar sobre el archivo generado y corregir cualquier infracción antes de entregar:

```bash
python3 - <<'PY' presentacion.html
import re, sys, html
src = open(sys.argv[1], encoding='utf-8').read()
errors = []
if not src.lstrip().startswith('<!DOCTYPE html>'): errors.append('No inicia con <!DOCTYPE html>')
if not src.rstrip().endswith('</html>'): errors.append('No termina en </html>')
if 'Información Confidencial | Propiedad de Entel Connect 2026' not in src: errors.append('Footer institucional ausente o alterado')
for tag in ['cdn.tailwindcss.com', 'font-awesome/6.4.0', 'family=Barlow', 'JetBrains+Mono', 'tailwind.config']:
    if tag not in src: errors.append(f'Falta dependencia: {tag}')
if not re.search(r'text-\[#005CFF\][^>]*>e</span>\s*<span[^>]*text-\[#FF3D00\][^>]*>\)</span>', src):
    errors.append('Isotipo e) connect alterado o ausente')
for m in re.finditer(r'data-max-words="(\d+)"[^>]*>(.*?)</', src, re.S):
    limit, text = int(m.group(1)), re.sub(r'<[^>]+>', ' ', html.unescape(m.group(2)))
    n = len(text.split())
    if n > limit: errors.append(f'{n}/{limit} palabras: "{text.strip()[:60]}…"')
sections = len(re.findall(r'id="view-slide\d+"', src))
if not 5 <= sections <= 6: errors.append(f'Número de láminas fuera de presupuesto: {sections}')
print('QA OK' if not errors else '\n'.join('✗ ' + e for e in errors))
PY
```

### 7.3 Mensaje final al usuario

Solo: 1 línea de introducción, el archivo (o bloque ```` ```html ````), y 1 línea de cierre con un siguiente paso concreto (ej. "¿Ajustamos algún texto o cifra?"). Sin recapitular el proceso ni mostrar el resultado del QA salvo que haya datos pendientes que el usuario deba completar.
