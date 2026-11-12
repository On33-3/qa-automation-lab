---
description: Explicación corta de un tema de QA, con ejemplo en Juice Shop y check de entrada
argument-hint: <tema>
---
Tema: $ARGUMENTS

1. Ubicá el tema en `ROADMAP.md`, en sus fundamentos ("Fundamentals per module") y en el plan del módulo en
   `docs/`, si ya existe.
2. Hacé 3 preguntas de entrada sobre el tema, una por vez. Al menos una sobre un fundamento previo.
   Según sus respuestas, decidí qué explicar, qué saltear y si hay que repasar un fundamento primero.
3. Explicá con el formato fijo: **En una frase**, **Ejemplo en Juice Shop**, **Error común** y **Qué riesgo evita**.
   Máximo una pantalla. Si el tema es grande, partilo en partes y preguntá si sigue.
4. Proponé una práctica guiada chica: el esqueleto de código o el documento, con los `TODO(human)` marcados.
   El tamaño del esqueleto sigue el "andamiaje que se retira" del mentor. No completes los `TODO(human)`.
   Antes de que corra algo, pedile que prediga el resultado.
5. Cerrá diciendo cuál es el próximo paso: `/explain $ARGUMENTS` y después `/check $ARGUMENTS`.
