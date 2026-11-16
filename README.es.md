# Quiet Axis

**Una skill de diseño portátil para cualquier agente de programación con IA.** Convierte "haz que se vea bien" en una secuencia que el agente puede ejecutar y verificar: clasificar contenido → estructurar la página → construir jerarquía → usar movimiento para explicar cambios de estado.

[English](README.md) · [简体中文](README.zh-CN.md) · [日本語](README.ja.md) · [Español](README.es.md)

> **Creado por [@dream120526-create](https://github.com/dream120526-create) y [@appledream120526-cloud](https://github.com/appledream120526-cloud) (coautores).** Si lo encuentras en otro sitio, los repositorios originales son [dream120526-create/quiet-axis](https://github.com/dream120526-create/quiet-axis) y [appledream120526-cloud/quiet-axis](https://github.com/appledream120526-cloud/quiet-axis).

---

## Por qué existe

La UI generada por agentes falla casi siempre en los mismos tres puntos: el contenido no está clasificado, la página no tiene regiones y todos los elementos pesan lo mismo. Quiet Axis es una skill en Markdown que entrega pocas reglas duras, un contrato de diseño y puertas de aceptación basadas en evidencia, sin atarse a un framework ni a un proveedor.

Es deliberadamente **pequeña para vivir en contexto** y **concreta para cambiar la salida**.

## Qué incluye

| Archivo | Propósito |
| --- | --- |
| `SKILL.md` | Entrada: niveles de cambio, siete pasos, valores por defecto, cuatro curvas, 22 puertas P0 |
| `DESIGN.md` | Contrato de diseño para completar: hechos del producto, estrella polar en una frase, héroe por pantalla, tokens, do/don't |
| `CHEATSHEET.md` | Tarjeta de una página para trabajar en 10 minutos |
| `topics/motion.md` | 6 interacciones de botón + 16 efectos UI, con código ejecutable y equivalentes SwiftUI/Compose |
| `topics/patterns.md` | 10 patrones de página, páginas densas en datos y cómo derivar el patrón 11 |
| `topics/recipes.md` | Tokens, shell, botones, diálogo, toast, skeleton, páginas completas de lista/ajustes/formulario |
| `topics/reference.md` | 48 anti-patrones, contraste medido de tokens, tipografía, accesibilidad, formato de revisión |
| `topics/i18n.md` | Mezcla CJK/latino, puntuación, números, RTL, pilas de fuentes |
| `topics/delivery.md` | Builds HTML / IPA / APK / EXE, firma, aceptación en dispositivo real |
| `references/agent-ui-skills.md` | Comparación metodológica de 14 skills de UI para agentes |

Aspectos clave:

- **No es un catálogo de patrones.** Hay 10 esqueletos y un método documentado para derivar el 11: listar contenido, hacer 2–3 preguntas de forma, aplicar las cinco zonas y registrarlo en `DESIGN.md`.
- **Auditoría de "slop" IA.** 18 señales (degradados morado-azul, cuatro tarjetas KPI idénticas, emojis como iconos, métricas falsas) con el orden Audit → Direction → Passes.
- **Movimiento con intención.** Velocidad constante = `linear`; salida = `ease-in`; entrada = `ease-out`; movimiento interno = `ease-in-out`;resorte solo para feedback.
- **Evidencia, no afirmaciones.** Contraste con hex real, capturas 390/768/1440, recorrido con teclado, desbordamiento a 280px, modo de movimiento reducido.
- **Multiplataforma y agnóstica al agente.** Web, iOS, Android y escritorio; funciona con Claude Code, Codex, Cursor, Gemini CLI, Windsurf, Aider o un LLM normal.

## Instalación

Copia la carpeta en el directorio de skills de tu agente, o indícale `SKILL.md`.

```bash
git clone https://github.com/dream120526-create/quiet-axis.git
```

Ubicaciones habituales:

| Agente | Ruta |
| --- | --- |
| Claude Code | `~/.claude/skills/quiet-axis/` o `<proyecto>/.claude/skills/quiet-axis/` |
| Codex | `~/.agents/skills/quiet-axis/` o `<proyecto>/.agents/skills/quiet-axis/` |
| Gemini CLI / Antigravity | `~/.gemini/config/skills/quiet-axis/` |
| Cursor | `<proyecto>/.cursor/skills/quiet-axis/` o un puntero en `.cursor/rules` |
| Windsurf | `<proyecto>/.windsurf/skills/quiet-axis/` |
| Otro agente | Colócala donde quieras y añade a `AGENTS.md`: "Para UI, lee primero `quiet-axis/SKILL.md`." |

Sin runtime, sin servidor MCP, sin build.

## Uso

```text
Lee quiet-axis/SKILL.md. Crea la página de ajustes de facturación.
Empieza con un borrador de DESIGN.md y sigue los siete pasos. No omitas la clasificación.
```

Para un cambio pequeño:

```text
Usa quiet-axis. Cambia solo el color del botón principal; revisa únicamente el contraste de tokens (P0 9).
```

La skill define niveles de cambio para que un agente no rediseñe todo por editar un texto.

## Principios de diseño

1. **Lógica antes que píxeles.** Clasificación, estructura, jerarquía; el pulido va al final.
2. **Un héroe y una acción principal por pantalla.** Todo lo demás se degrada.
3. **El movimiento explica el cambio de estado.** Si al quitarlo no se pierde significado, se quita.
4. **Sin evidencia no hay aprobación.** Capturas, contraste calculado, teclado, movimiento reducido.
5. **Una dirección, dicha en una frase.** "Moderno y limpio" no es una dirección.

## Estructura del repositorio

```text
quiet-axis/
├── SKILL.md
├── DESIGN.md
├── CHEATSHEET.md
├── LICENSE
├── NOTICE
├── README.md
├── README.zh-CN.md
├── README.ja.md
├── README.es.md
├── topics/ (motion, patterns, forms, states, platforms, i18n, delivery, recipes, reference)
└── references/agent-ui-skills.md
```

## Licencia y atribución

**Quiet Axis License 1.0 (Atribución–NoComercial).** Puedes copiar, modificar y crear derivados. Debes acreditar a ambos autores (o enlazar a un repositorio original) y no puedes venderlo ni cobrar por acceso. Términos completos en [LICENSE](LICENSE).

Creado por [@dream120526-create](https://github.com/dream120526-create) y [@appledream120526-cloud](https://github.com/appledream120526-cloud).

Referencias metodológicas de terceros y atribución CC BY 4.0: [NOTICE](NOTICE).
