---
description: Revisión de la rama actual o de un PR como lo haría un QA lead
argument-hint: [número de PR o vacío para la rama actual]
---
Objetivo: $ARGUMENTS (si está vacío, revisá la rama actual contra `main`).

1. Mirá el diff (`git diff main...HEAD` o `gh pr diff $ARGUMENTS`) y corré lint, tipos y los tests afectados.
2. Revisá como QA lead, en este orden de importancia:
   - ¿Prueba lo que el plan del módulo dice que hay que probar? ¿Falta algún riesgo alto?
   - ¿Los tests pueden fallar por la razón correcta? Buscá aserciones débiles, esperas fijas y dependencia de datos compartidos.
   - Diseño: Page Objects, fixtures, nombres, duplicación.
   - ¿El PR se entiende? Descripción, commits, plan y ADR si hubo cambio de rumbo.
3. Devolvé como máximo 7 comentarios, cada uno con archivo y línea, el problema, por qué importa y una pregunta
   que lo haga pensar. No reescribas su código.
