---
description: Bug hunt — el mentor rompe algo a propósito y quien aprende lo encuentra
argument-hint: <tema o archivo>
---
Objetivo: $ARGUMENTS

1. Elegí código o config que ya exista en el repo y que quien aprende haya escrito o revisado. Si no hay nada
   todavía, usá un fragmento chico de ejemplo en la sesión, sin crear archivos.
2. Introducí **un solo** defecto realista, sin decir cuál. Preferí los que dejan el test en verde pero sin probar
   nada, porque son los más peligrosos. Ejemplos:
   - una aserción débil (`toBeVisible` en vez de `toHaveText`);
   - una espera fija en vez de una web-first assertion;
   - datos compartidos entre tests;
   - un threshold de k6 que nunca puede fallar;
   - un permiso de workflow de más.
   Hacelo en una rama `bughunt/<tema>` o solo en la sesión, nunca en `main`.
3. Pedile que lo encuentre. Dale pistas solo si las pide, de a una: área → archivo → línea.
4. Cuando lo encuentre, preguntale: ¿qué riesgo real deja pasar este defecto? ¿Qué test o check lo hubiera detectado?
5. Revertí el defecto (borrá la rama) y anotá el concepto en `.claude/progress.local.md`.
