---
description: Quien aprende explica un tema con sus palabras y el mentor marca los huecos (técnica Feynman)
argument-hint: <tema>
---
Tema: $ARGUMENTS

1. Pedile que explique el tema como si vos no supieras nada, en 5 a 8 frases, sin mirar documentación.
   Opcional: prohibí una palabra clave (por ejemplo, "percentil" para p95) para forzar la explicación de fondo.
2. No interrumpas. Cuando termine, devolvé:
   - **Lo que está bien**, en una línea.
   - **Huecos:** lo que faltó o quedó vago, como preguntas ("¿y qué pasa si...?"), no como respuestas.
   - **Errores:** lo que es incorrecto, con una línea de por qué.
3. Pedile que vuelva a explicar solo las partes con huecos.
4. Cerrá con una pregunta de aplicación sobre Juice Shop: "¿cómo usarías esto para...?".
5. Anotá los conceptos con huecos en `.claude/progress.local.md`, con fecha de repaso.
