---
title: Divisores
---

{% include mathjax.html %}


**Tabla de contenido**

- [Número de divisores](#número-de-divisores)
- [Suma de divisores](#suma-de-divisores)
- [Funciones multiplicativas](#funciones-multiplicativas)
- [Problemas de práctica](#problemas-de-práctica)

## Número de divisores

Debería ser obvio que la factorización prima de un divisor $ d $ tiene que ser un subconjunto de la factorización prima de $n$, p. Ej. $6 = 2 \cdot 3$ es un divisor de $60 = 2 ^ 2 \cdot 3 \cdot 5$.
Entonces, solo necesitamos encontrar todos los diferentes subconjuntos de la factorización prima de $n$.

Por lo general, el número de subconjuntos es $2 ^ x$ para un conjunto con elementos $x$.
Sin embargo, esto ya no es cierto si hay elementos repetidos en el conjunto. En nuestro caso, algunos factores primos pueden aparecer varias veces en la factorización prima de $n$.

Si un factor primo $p$ aparece $e$ veces en la factorización prima de $n$, entonces podemos usar el factor $p$ hasta $e$ veces en el subconjunto.
Lo que significa que tenemos $e + 1$ opciones.

Por lo tanto, si la factorización prima de $n$ es $p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$, donde $p_i$ son números primos distintos, entonces el número de divisores es:
$$d(n) = (e_1 + 1) \cdot (e_2 + 1) \cdots (e_k + 1)$$

Una forma de pensarlo es la siguiente:


* Si solo hay un divisor primo distinto $n = p_1^{e_1}$, entonces obviamente hay $e_1 + 1$ divisores ($1, p_1, p_1^2, \dots, p_1^{e_1}$).

* Si hay dos divisores primos distintos $n = p_1^{e_1} \cdot p_2^{e_2}$, entonces se pueden organizar todos los divisores en forma de tabla.
$$\begin{array}{c|ccccc}
& 1 & p_2 & p_2^2 & \dots & p_2^{e_2} \\
\hline
1 & 1 & p_2 & p_2^2 & \dots & p_2^{e_2} \\
p_1 & p_1 & p_1 \cdot p_2 & p_1 \cdot p_2^2 & \dots & p_1 \cdot p_2^{e_2} \\
p_1^2 & p_1^2 & p_1^2 \cdot p_2 & p_1^2 \cdot p_2^2 & \dots & p_1^2 \cdot p_2^{e_2} \\
\vdots & \vdots & \vdots & \vdots & \ddots & \vdots \\
p_1^{e_1} & p_1^{e_1} & p_1^{e_1} \cdot p_2 & p_1^{e_1} \cdot p_2^2 & \dots & p_1^{e_1} \cdot p_2^{e_2} \\
\end{array}$$
	Entonces, el número de divisores es trivialmente $(e_1 + 1) \cdot (e_2 + 1)$.

* Se puede hacer un argumento similar si hay más de dos factores primos distintos.

## Suma de divisores

Podemos usar el mismo argumento de la sección anterior.

* Si solo hay un divisor primo distinto $n = p_1^{e_1}$, entonces la suma es:
  $$1 + p_1 + p_1^2 + \dots + p_1^{e_1} = \frac {p_1^{e_1 + 1} - 1} {p_1 - 1}$$

* Si hay dos divisores primos distintos $n = p_1^{e_1} \cdot p_2^{e_2}$, entonces podemos hacer la misma tabla que antes.
  La única diferencia es que ahora queremos calcular la suma en lugar de contar los elementos.
  Es fácil ver que la suma de cada combinación se puede expresar como:
  $$\left(1 + p_1 + p_1^2 + \dots + p_1^{e_1}\right) \cdot \left(1 + p_2 + p_2 ^ 2 + \dots + p_2^{e_2} \right)$$
  $$ = \frac {p_1^{e_1 + 1} - 1} {p_1 - 1} \cdot \frac {p_2 ^ {e_2 + 1} - 1} {p_2 - 1}$$

* En general, para $n = p_1^{e_1} \cdot p_2^{e_2} \cdots p_k^{e_k}$ recibimos la fórmula:
  $$ \sigma (n) = \frac{p_1 ^ {e_1 + 1} - 1} {p_1 - 1} \cdot \frac {p_2 ^ {e_2 + 1} - 1} {p_2 - 1} \cdots \frac {p_k ^ {e_k + 1} - 1} {p_k - 1} $$

## Funciones multiplicativas

Una función multiplicativa es una función $ f (x) $ que satisface
$$f (a \cdot b) = f (a) \cdot f (b)$$
si $a$ y $b$ son coprimos.

Tanto $d(n)$ como $\sigma (n)$ son funciones multiplicativas.

Las funciones multiplicativas tienen una gran variedad de propiedades interesantes, que pueden ser muy útiles en problemas de teoría de números.
Por ejemplo, la convolución de Dirichlet de dos funciones multiplicativas también es multiplicativa.

## Problemas de práctica

  - [SPOJ - COMDIV](https://www.spoj.com/problems/COMDIV/)
  - [SPOJ - DIVSUM](https://www.spoj.com/problems/DIVSUM/)
  - [SPOJ - DIVSUM2](https://www.spoj.com/problems/DIVSUM2/)
