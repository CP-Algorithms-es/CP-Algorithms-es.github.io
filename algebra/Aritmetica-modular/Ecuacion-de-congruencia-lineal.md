---
title: Ecuación de congruencia lineal
---

{% include mathjax.html %}

**Tabla de contenido**

- [Ecuación de congruencia lineal](#ecuación-de-congruencia-lineal)
  - [Solución encontrando el elemento inverso](#solución-encontrando-el-elemento-inverso)
  - [Solución con el algoritmo Euclidiano extendido](#solución-con-el-algoritmo-euclidiano-extendido)

# Ecuación de congruencia lineal

Esta ecuación tiene la forma:

$$a \cdot x = b \pmod n,$$

donde $a$, $b$ y $n$ son números enteros conocidos y $x$ es un número entero desconocido.

Se requiere encontrar el valor $x$ del intervalo $[0, n-1]$ (claramente, en toda la recta numérica puede haber infinitas soluciones que diferirán entre sí en $n \cdot k$, donde $k$ es cualquier número entero). Si la solución no es única, consideraremos cómo obtener todas las soluciones.

## Solución encontrando el elemento inverso

Consideremos primero un caso más simple donde $a$ y $n$ son **coprimos** ($\gcd(a, n) = 1$).
Entonces uno puede encontrar la [inversa](./Inverso-multiplicativo-modular.html) de $a$, y multiplicar ambos lados de la ecuación por la inversa, y podemos obtener una solución **única**.

$$x = b \cdot a ^ {- 1} \pmod n$$

Ahora considere el caso donde $a$ y $n$ son **no coprimos** ($\gcd(a, n) \ne 1$).
Entonces, la solución no siempre existirá (por ejemplo, $2 \cdot x = 1 \pmod 4$ no tiene solución).

Sea $g = \gcd(a, n)$, es decir, el [máximo común divisor](../fundamentos/algoritmo-euclidiano.html) de $a$ y $n$ (que en este caso es mayor que uno).

Entonces, si $b$ no es divisible por $g$, no hay solución. De hecho, para cualquier $x$, el lado izquierdo de la ecuación $a \cdot x \pmod n$, siempre es divisible por $g$, mientras que el lado derecho no es divisible por él, por lo tanto no hay soluciones.

Si $g$ divide a $b$, entonces al dividir ambos lados de la ecuación por $g$ (es decir, dividiendo $a$, $b$ y $n$ por $g$), obtenemos una nueva ecuación:

$$a^\prime \cdot x = b^\prime \pmod{n^\prime}$$

en el que $a^\prime$ y $n^\prime$ son primos relativos, y ya hemos aprendido cómo manejar dicha ecuación.
Obtenemos $x^\prime$ como solución para $x$.

Está claro que este $x^\prime$ también será una solución de la ecuación original.
Sin embargo, **no será la única solución**.
Se puede demostrar que la ecuación original tiene exactamente $g$ soluciones, y se verán así:

$$x_i = (x^\prime + i\cdot n^\prime) \pmod n \quad \text{para } i = 0 \ldots g-1$$

Resumiendo, podemos decir que el **número de soluciones** de la ecuación de congruencia lineal es igual a $g = \gcd(a, n)$ o cero.

## Solución con el algoritmo Euclidiano extendido

Podemos reescribir la congruencia lineal con la siguiente ecuación diofántica:

$$a \cdot x + n \cdot k = b,$$

donde $x$ y $k$ son enteros desconocidos.

El método para resolver esta ecuación se describe en el artículo correspondiente [Ecuaciones diofánticas lineales](../fundamentos/ecuaciones-diofanticas-lineales.html) y consiste en aplicar el [algoritmo Euclidiano extendido](../fundamentos/algoritmo-euclidiano-extendido.html).

También describe el método para obtener todas las soluciones de esta ecuación a partir de una solución encontrada y, dicho sea de paso, este método, cuando se considera cuidadosamente, es absolutamente equivalente al método descrito en la sección anterior.


