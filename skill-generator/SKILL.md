---
name: skill-generator
description: Crea archivos SKILL.md nuevos a partir de una idea, un flujo de trabajo repetido o una conversación. Úsala cuando pidan "crear una skill", "convertir esto en una skill", "hacer un SKILL.md" o mejorar una skill existente.
---

# Generador de Skills

Esta skill guía la creación de otras skills: carpetas con un `SKILL.md` (y opcionalmente archivos de apoyo) que enseñan a Gemini a hacer una tarea concreta de forma consistente.

## Principios

1. **La descripción es el disparador.** Gemini decide si cargar una skill leyendo solo su `name` y `description`. Si la descripción es vaga, la skill nunca se usará; si es demasiado amplia, se usará cuando no corresponde.
2. **Contexto mínimo, máximo efecto.** Escribe solo lo que Gemini no sabría por sí mismo: convenciones del equipo, formatos exactos, pasos no obvios, errores comunes. No expliques cosas generales.
3. **Divulgación progresiva.** El `SKILL.md` debe ser corto (idealmente < 300 líneas). Mueve detalles largos a `references/*.md`, plantillas a `assets/` y código determinista a `scripts/`, y enlázalos desde el `SKILL.md` indicando *cuándo* leerlos.
4. **Instrucciones, no ensayos.** Usa modo imperativo ("Lee", "Valida", "Genera"), pasos numerados cuando el orden importa y ejemplos concretos.
5. **Explica el porqué de las reglas importantes.** Una regla con razón se aplica mejor en casos no previstos que una regla en MAYÚSCULAS.

## Proceso

### Paso 1 — Entender la skill a crear

Antes de escribir, aclara estos puntos. Si la conversación ya los responde, no vuelvas a preguntar; si faltan, haz como máximo 3–4 preguntas concretas:

- **Tarea:** ¿Qué debe lograr la skill? ¿Cuál es el resultado final (archivo, mensaje, análisis, código)?
- **Disparadores:** ¿Qué frases o situaciones deberían activarla? ¿Cuáles parecidas NO deberían activarla?
- **Entradas:** ¿Qué recibe normalmente (archivos, datos, una pregunta)?
- **Conocimiento específico:** ¿Qué reglas, formatos, marca, tono o restricciones propias hay que respetar?
- **Herramientas:** ¿Necesita scripts, librerías, conectores o APIs?
- **Ejemplos:** ¿Hay un ejemplo de buen resultado (y de uno malo)?

Si el usuario dice "convierte esto en una skill", extrae las respuestas del historial de la conversación: los pasos que funcionaron, las correcciones que hizo el usuario y el formato final aceptado.

### Paso 2 — Diseñar la estructura

Decide qué archivos necesita la skill:

```
nombre-de-la-skill/
├── SKILL.md              # obligatorio: instrucciones principales
├── references/           # opcional: documentación detallada que se lee bajo demanda
│   └── guia-detallada.md
├── scripts/              # opcional: código determinista o repetitivo
│   └── validar.py
└── assets/               # opcional: plantillas, logos, ejemplos de salida
    └── plantilla.md
```

Regla práctica: si algo se haría igual siempre y es propenso a errores (convertir formatos, validar, calcular), conviértelo en script. Si es conocimiento que solo a veces hace falta, ponlo en `references/`.

### Paso 3 — Escribir el frontmatter

```yaml
---
name: nombre-en-kebab-case
description: Qué hace + cuándo usarla. Incluye las palabras que el usuario probablemente dirá.
---
```

Requisitos:
- `name`: minúsculas, números y guiones; máximo 64 caracteres; sin las palabras "Gemini" ni "anthropic".
- `description`: una o dos frases, máximo 1024 caracteres, sin `<` ni `>`. Debe decir **qué** hace y **cuándo** usarla. Si hay una skill parecida, di cuándo usar esta en vez de la otra.

Ejemplo débil: `description: Ayuda con informes.`
Ejemplo fuerte: `description: Genera informes mensuales de ventas en .xlsx con el formato del equipo comercial. Úsala cuando pidan el reporte mensual, cierre de mes o resumen de ventas por región.`

### Paso 4 — Escribir el cuerpo

Usa esta plantilla y elimina las secciones que no aporten:

```markdown
# [Nombre legible de la skill]

[1–2 frases: qué produce y para quién.]

## Cuándo usarla
- [Situación 1]
- [Situación 2]
- No usarla para: [casos parecidos que corresponden a otra skill]

## Proceso
1. **[Paso]** — [Qué hacer y por qué.]
2. **[Paso]** — [...]
3. **Verificar** — [Cómo comprobar que el resultado es correcto.]

## Reglas y convenciones
- [Regla específica con su razón.]

## Formato de salida
[Estructura exacta, plantilla o ejemplo del resultado esperado.]

## Ejemplos
**Entrada:** [...]
**Salida esperada:** [...]

## Errores comunes
- [Error] → [Cómo evitarlo.]

## Recursos
- `references/xxx.md` — léelo cuando [condición].
- `scripts/xxx.py` — ejecútalo para [propósito]: `python scripts/xxx.py <args>`
```

### Paso 5 — Revisar con esta lista

Antes de entregar, verifica:

- [ ] El `name` cumple el formato y es descriptivo.
- [ ] La `description` dice qué hace y cuándo usarla, con palabras que el usuario realmente usaría.
- [ ] Cada instrucción aporta algo que Gemini no haría por defecto.
- [ ] Los pasos están en orden y terminan con una verificación.
- [ ] Hay al menos un ejemplo concreto de entrada/salida (si aplica).
- [ ] El `SKILL.md` es breve; lo extenso está en `references/`.
- [ ] Cada archivo referenciado existe y se indica cuándo leerlo o ejecutarlo.
- [ ] No hay secretos, contraseñas ni datos personales en la skill.
- [ ] No hay contradicciones entre secciones.

### Paso 6 — Probar mentalmente

Imagina 3 peticiones de usuario:
1. Una que **debe** activar la skill → ¿la descripción la captura? ¿Las instrucciones bastan para hacerla bien?
2. Una **en el límite** → ¿la skill da una respuesta razonable?
3. Una que **no debe** activarla → ¿la descripción evita el falso positivo?

Ajusta la descripción o las instrucciones según lo que falle.

### Paso 7 — Entregar

- Crea la carpeta con el `name` de la skill y escribe el `SKILL.md` (y los archivos de apoyo) dentro.
- Entrega los archivos al usuario y resume en 2–3 líneas qué hace la skill y qué la activa.
- Sugiere un siguiente paso: probarla con una petición real y ajustar la descripción si no se activa.

## Mejorar una skill existente

1. Lee el `SKILL.md` actual completo y sus archivos de apoyo.
2. Identifica el problema: ¿no se activa (descripción), se activa de más (descripción), o produce malos resultados (instrucciones)?
3. Cambia solo lo necesario y conserva todo lo que funciona.
4. Entrega la versión completa actualizada, no solo el fragmento modificado, y resume qué cambió.

## Errores comunes al crear skills

- **Descripción genérica** ("ayuda con documentos") → nunca se activa o choca con otras skills. Sé específico.
- **Repetir conocimiento general** ("Python es un lenguaje...") → gasta contexto sin aportar. Elimínalo.
- **Todo en un solo archivo enorme** → mueve detalles a `references/`.
- **Reglas sin razón y en mayúsculas** → explica el porqué en una línea.
- **Sin ejemplos** → el formato de salida variará. Incluye al menos uno.
- **Sin paso de verificación** → los errores llegan al usuario. Termina siempre con una comprobación.
