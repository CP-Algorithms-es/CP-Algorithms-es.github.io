---
title: Inverso multiplicativo modular
---

{% include mathjax.html %}

**Tabla de contenido**

- [Inverso multiplicativo modular](#inverso-multiplicativo-modular)
  - [Encontrar el inverso modular usando el algoritmo euclidiano extendido](#encontrar-el-inverso-modular-usando-el-algoritmo-euclidiano-extendido)
  - [Encontrar el inverso modular mediante la exponenciación binaria](#encontrar-el-inverso-modular-mediante-la-exponenciación-binaria)
  - [Encontrar el inverso modular para cada número módulo m](#encontrar-el-inverso-modular-para-cada-número-módulo-m)
    - [Prueba](#prueba)
  - [Problemas de práctica](#problemas-de-práctica)

# Inverso multiplicativo modular

Un inverso multiplicativo modular de un entero $a$ es un entero $x$ tal que $a \cdot x$ es congruente con 1 módulos de algún módulo $m$. Para escribirlo de manera formal: queremos encontrar un número entero $x$ para que

$$ a\cdot x\:≡\:1\:mod\:m $$

También denotaremos $x$ simplemente como $a^{-1}$.

Debemos tener en cuenta que el inverso modular no siempre existe. Por ejemplo, sea $m\:=\:4$, $a\:=\:2$. Comprobando todos los valores posibles módulo $m$ debe quedar claro que no podemos encontrar $a^{-1}$ que satisfaga la ecuación anterior. Se puede probar que el inverso modular existe si y solo si $a$ y $m$ son relativamente primos (es decir $mcd\left(a\:,\:m\right)\:=\:1$, mcd denota al máximo común divisor de $a$ y $m$).

En este artículo, presentamos dos métodos para encontrar el inverso modular en caso de que exista, y un método para encontrar el inverso modular para todos los números en tiempo lineal.

# Encontrar el inverso modular usando el algoritmo euclidiano extendido

Considere la siguiente ecuación (con $x$ y $y$ no conocidas):

$$a\cdot x\:+\:m\cdot y\:=\:1$$

Esta es una [Ecuación Lineal Diofántica](https://cp-algorithms-es.github.io/algebra/fundamentos/ecuaciones-diofanticas-lineales.html) en dos variables. Como se muestra en el artículo vinculado, cuando $mcd\left(a\:,\:m\right)\:=\:1$, la ecuación tiene una solución que se puede encontrar usando el algoritmo euclidiano extendido. Tenga en cuenta que $mcd\left(a\:,\:m\right)\:=\:1$ es también la condición para que exista el inverso modular.

Ahora, si tomamos modulo $m$ de ambos lados, podemos deshacernos de $m\cdot y$, y la ecuación se convierte en:

$$a\cdot\:x\:≡\:1\:mod\:m$$

Por lo tanto, el inverso modular de $a$ es $x$.

La implementación es la siguiente:

```cpp
int x, y;
int g = extended_euclidean(a, m, x, y);
if (g != 1) {
    cout << "¡No hay Solución!" << endl;
}
else {
    x = (x % m + m) % m;
    cout << x << endl;
}
```

Observe la forma en que modificamos `x`. El resultado `x` del algoritmo euclidiano extendido puede ser negativo, por lo que `x % m` también puede ser negativo, y primero tenemos que sumar `m` para que sea positivo.

# Encontrar el inverso modular mediante la exponenciación binaria

Otro método para encontrar la inversa modular es usar el teorema de Euler, que establece que la siguiente congruencia es verdadera si $a$ y $m$ son primos relativos:

$$a^{\phi \left(m\right)}\:≡\:1\:mod\:m$$

$\phi$ es la función Totient de Euler. Una vez más, tenga en cuenta que $a$ y $m$ son primos relativos también es la condición para que existiera el inverso modular.

Si $m$ es un número primo, esto se simplifica al pequeño teorema de Fermat:

$$a^{m−1}\:≡\:1\:mod\:m$$

Multiplicando a ambos lados de las ecuaciones anteriores por $a^{-1}$, obtenemos que:

* Para un arbitrario numero (pero co-primo) módulo $m$: $\:a^{ϕ\left(m\right)−1}\:≡\:a^{−1}\:mod\:m$

* Para un primo modulo $m$: $a^{m−2}\:≡\:a^{-1}\:mod\:m\:$


A partir de estos resultados, podemos encontrar fácilmente el inverso modular utilizando el [Algoritmo de Exponenciación Binaria](https://cp-algorithms-es.github.io/algebra/fundamentos/exponenciacion-binaria.html), que funciona en $O\left(log\:m\right)$ complejidad de tiempo.

Aunque este método es más fácil de entender que el método descrito en el párrafo anterior, en el caso de que $m$ no es un número primo, necesitamos calcular la función phi de Euler, que implica la factorización de $m$, que puede resultar muy difícil. Si la factorización prima de $m$ se conoce, entonces la complejidad de este método es $O\left(log\:m\right)$.

# Encontrar el inverso modular para cada módulo numérico m

El problema es el siguiente: queremos calcular el inverso modular para cada número en el rango $\left[1,\:m-1\right]$.

Aplicando los algoritmos descritos en los apartados anteriores, podemos obtener una solución con complejidad $O\left(m\cdot log\:m\right)$.

Aquí presentamos un mejor algoritmo con complejidad. $O\left(m\right)$. Sin embargo, para este algoritmo específico, requerimos que el módulo $m$ sea primo.

Denotamos por $inv[i]$ el inverso modular de $i$. Entonces para $i\:>\:1$ la siguiente ecuación es válida:

$$inv\left[i\right]=-\left \lfloor \frac {m} {i} \right \cdot inv\left[m\:mod\:i\right]\:mod\:m$$

Por tanto, la implementación es muy sencilla:

```cpp
inv[1] = 1;
for(int i = 2; i < m; ++i)
    inv[i] = m - (m/i) * inv[m%i] % m;
```

# Problemas de Práctica

* [UVa 11904 - One Unit Machine](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=3055)
* [Hackerrank - Longest Increasing Subsequence Arrays](https://www.hackerrank.com/contests/world-codesprint-5/challenges/longest-increasing-subsequence-arrays)
* [Codeforces 300C - Beautiful Numbers](https://codeforces.com/contest/300/problem/C)
* [Codeforces 622F - The Sum of the k-th Powers](https://codeforces.com/contest/622/problem/F)
* [Codeforces 717A - Festival Organization](https://codeforces.com/contest/717/problem/A)
* [Codeforces 896D - Nephren Runs a Cinema](https://codeforces.com/contest/896/problem/D)