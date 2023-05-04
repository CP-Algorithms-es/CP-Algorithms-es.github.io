---
title: Inverso multiplicativo modular
---

{% include mathjax.html %}

**Tabla de contenido**

- [Inverso multiplicativo modular](#inverso-multiplicativo-modular)
  - [Definición](#definición)
  - [Encontrar el inverso modular usando el algoritmo euclidiano extendido](#encontrar-el-inverso-modular-usando-el-algoritmo-euclidiano-extendido)
  - [Encontrar el inverso modular usando exponenciación binaria](#encontrar-el-inverso-modular-usando-exponenciación-binaria)
  - [Encontrar el inverso modular para cada número módulo $m$](#encontrar-el-inverso-modular-para-cada-número-módulo-$m$)
	- [Prueba](#prueba)
  - [Problemas de Práctica](#problemas-de-práctica)

# Inverso multiplicativo modular

## Definición

Un [inverso multiplicativo modular](https://es.wikipedia.org/wiki/Inverso_multiplicativo_(aritm%C3%A9tica_modular)) de un entero $a$ es un entero $x$ tal que $a \cdot x$ es congruente con $1$ en algún módulo $m$.
Para escribirlo de manera formal: queremos encontrar un número entero $x$ tal que
$$a \cdot x \equiv 1 \mod m.$$
También denotaremos $x$ simplemente con $a^{-1}$.

Debemos tener en cuenta que el inverso modular no siempre existe. Por ejemplo, sea $m = 4$, $a = 2$.
Al verificar todos los valores posibles módulo $m$ queda claro que no podemos encontrar $a^{-1}$ que satisfaga la ecuación anterior.
Se puede probar que el inverso modular existe si y solo si $a$ y $m$ son primos relativos (es decir, $\gcd(a, m) = 1$).

En este artículo, presentamos dos métodos para encontrar el inverso modular en caso de que exista, y un método para encontrar el inverso modular para todos los números en tiempo lineal.

## Encontrar el inverso modular usando el algoritmo euclidiano extendido

Considere la siguiente ecuación (con $x$ y $y$ desconocidos):

$$a \cdot x + m \cdot y = 1$$

Ésta es una [Ecuación lineal diofántica en dos variables](../fundamentos/ecuaciones-diofanticas-lineales.html).
Como se muestra en el artículo vinculado, cuando $\gcd(a, m) = 1$, la ecuación tiene una solución que se puede encontrar usando el [Algoritmo Euclidiano Extendido](http://en.wikipedia.org/wiki/Extended_Euclidean_algorithm).
Tenga en cuenta que $\gcd(a, m) = 1$ también es la condición para que exista el inverso modular.

Ahora, si tomamos el módulo $m$ de ambos lados, podemos deshacernos de $m \cdot y$, y la ecuación se convierte en:

$$a \cdot x \equiv 1 \mod m$$

Por tanto, el inverso modular de $a$ es $x$.

La implementación es la siguiente:

```cpp
int x, y;
int g = extended_euclidean(a, m, x, y);
if (g != 1) {
    cout << "Sin solucion";
} else {
    x = (x % m + m) % m;
    cout << x << endl;
}
```

Observe que la forma en que modificamos `x`.
La `x` resultante del algoritmo euclidiano extendido puede ser negativa, por lo que `x % m` también puede ser negativa, por lo tanto tenemos que sumar `m` para que sea positiva.

## Encontrar el inverso modular usando exponenciación binaria

Otro método para encontrar el inverso modular es usar el teorema de Euler, que establece que la siguiente congruencia es verdadera si $a$ y $m$ son primos relativos:

$$a^{\phi (m)} \equiv 1 \mod m$$

$\phi$ es la [función Totient de Euler](https://es.wikipedia.org/wiki/Funci%C3%B3n_%CF%86_de_Euler). <!---(./ algebra/phi-function.html).-->
Una vez más, tenga en cuenta que el hecho de que $a$ y $m$ sean primos relativos también fue la condición para que exista el inverso modular.

Si $m$ es un número primo, esto se simplifica al [pequeño teorema de Fermat](https://es.wikipedia.org/wiki/Peque%C3%B1o_teorema_de_Fermat):

$$a^{m - 1} \equiv 1 \mod m$$

Multiplica ambos lados de las ecuaciones anteriores por $a^{-1}$, y obtenemos:

* Para un módulo $m$ arbitrario (pero coprimo): $a ^ {\phi (m) - 1} \equiv a ^{-1} \mod m$
* Para un módulo $m$ primo: $a ^ {m - 2} \equiv a ^ {-1} \mod m$

A partir de estos resultados, podemos encontrar fácilmente el inverso modular usando el [algoritmo de exponenciación binaria](../fundamentos/exponenciacion-binaria.html), que trabaja en tiempo $O(\log m)$.

Aunque este método es más fácil de entender que el método descrito en el párrafo anterior, en el caso de que $m$ no sea un número primo, necesitamos calcular la función phi de Euler, que implica la factorización de $m$, lo que puede ser muy difícil. Si se conoce la factorización prima de $m$, entonces la complejidad de este método es $O(\log m)$.

## Encontrar el inverso modular para cada número módulo $m$

El problema es el siguiente:
queremos calcular el inverso modular para cada número en el rango $[1, m-1]$.

Aplicando los algoritmos descritos en los apartados anteriores, podemos obtener una solución con complejidad $O(m \log m)$.

Aquí presentamos un algoritmo mejor con complejidad $O(m)$.
Sin embargo, para este algoritmo específico, requerimos que el módulo $m$ sea primo.

Denotamos por $\text{inv}[i]$ el inverso modular de $i$. Entonces para $i>1$ la siguiente ecuación es válida:

$$\text{inv}[i] = - \left\lfloor \frac{m}{i} \right\rfloor \cdot \text{inv}[m \bmod i] \bmod m$$

Por tanto, la implementación es muy sencilla:

```cpp
inv[1] = 1;
for(int i = 2; i < m; ++i)
    inv[i] = m - (m/i) * inv[m%i] % m;
```

### Prueba

Tenemos:
$$m \bmod i = m -  \left\lfloor \frac{m}{i} \right\rfloor \cdot i$$
Tomando ambos lados módulo $m$ produce:
$$m \bmod i \equiv - \left\lfloor \frac{m}{i} \right\rfloor \cdot i \mod m$$
Multiplica ambos lados por $i^{-1} \cdot (m \bmod i)^{-1}$ produce:
$$(m \bmod i) \cdot i^{-1} \cdot (m \bmod i)^{-1} \equiv -\left\lfloor \frac{m}{i} \right\rfloor \cdot i \cdot i^{-1} \cdot (m \bmod i)^{-1} \mod m,$$
que se simplifica a:
$$i^{-1} \equiv -\left\lfloor \frac{m}{i} \right\rfloor \cdot (m \bmod i)^{-1} \mod m,$$

## Problemas de Práctica

* [UVa 11904 - One Unit Machine](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3055)
* [Hackerrank - Longest Increasing Subsequence Arrays](https://www.hackerrank.com/contests/world-codesprint-5/challenges/longest-increasing-subsequence-arrays)
* [Codeforces 300C - Beautiful Numbers](http://codeforces.com/problemset/problem/300/C)
* [Codeforces 622F - The Sum of the k-th Powers](http://codeforces.com/problemset/problem/622/F)
* [Codeforces 717A - Festival Organization](http://codeforces.com/problemset/problem/717/A)
* [Codeforces 896D - Nephren Runs a Cinema](http://codeforces.com/problemset/problem/896/D)


