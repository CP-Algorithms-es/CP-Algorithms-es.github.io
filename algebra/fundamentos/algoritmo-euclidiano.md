---
title: Algoritmo Euclidiano
---

{% include mathjax.html %}

**Tabla de contenido**

- [Algoritmo euclidiano para calcular el máximo común divisor](#algoritmo-euclidiano-para-calcular-el-máximo-común-divisor)
  - [Algoritmo](#algoritmo)
  - [Implementación](#implementación)
  - [Prueba de correctitud](#prueba-de-correctitud)
  - [Complejidad temporal](#complejidad-temporal)
  - [Minimo común multiplo](#minimo-común-multiplo)
  - [MCD binario](#mcd-binario)
  - [Problemas de práctica](#problemas-de-práctica)

# Algoritmo euclidiano para calcular el máximo común divisor

Dados dos números enteros no negativos $a$ y $b$, tenemos que encontrar su **MCD** (máximo común divisor), es decir, el número más grande que es un divisor de $a$ y $b$.
Se denota comúnmente por mcd(a, b). Matemáticamente se define como:
$$mcd(a, b) = \max_ {k = 1 \dots \infty ~ : ~ k \mid a ~ \wedge k ~ \mid b} k.$$
(aquí el símbolo "$\mid$" denota divisibilidad, es decir, "$k \mid a$" significa "$k$ divide $a$")

Cuando uno de los números es cero, mientras que el otro es distinto de cero, su máximo común divisor, por definición, es el segundo número. Cuando ambos números son cero, su máximo común divisor no está definido (puede ser cualquier número arbitrariamente grande), pero podemos definirlo como cero. Lo que nos da una regla simple: si uno de los números es cero, el máximo común divisor es el otro número.

El algoritmo euclidiano, que se analiza a continuación, permite encontrar el máximo común divisor de dos números $a$ y $b$ en $O (\log\min(a, b))$.

El algoritmo se describió por primera vez en "Elementos" de Euclides (alrededor del año 300 a. C.), pero es posible que el algoritmo tenga orígenes incluso anteriores.

## Algoritmo

El algoritmo es extremadamente simple:

$$mcd(a, b) = \begin{cases}a,&\text{si }b = 0 \\\\ mcd(b, a \bmod b),&\text{de lo contrario.}\end{cases}$$

## Implementación

```cpp
int mcs (int a, int b) {
    if(b == 0)
        return a;
    else
        return mcd(b, a % b);
}
```

Usando el operador ternario en C ++, podemos escribirlo como una línea.

```cpp
int gcd (int a, int b) {
    return b ? mcd(b, a % b) : a;
}
```

Y finalmente, aquí hay una implementación no recursiva:

```cpp
int mcd(int a, int b) {
    while(b) {
        a %= b;
        swap(a, b);
    }
    return a;
}
```

## Prueba de correctitud

Primero, observe que en cada iteración del algoritmo euclidiano el segundo argumento es estrictamente decreciente, por lo tanto (dado que los argumentos son siempre no negativos) el algoritmo siempre termina.

Para la prueba de corrección, necesitamos demostrar que $mcd(a, b) = mcd(b, a \bmod b)$ para todo $a \geq 0$, $b > 0$.

Mostraremos que el valor del lado izquierdo de la ecuación divide el valor del lado derecho y viceversa. Obviamente, esto significaría que los lados izquierdo y derecho son iguales, lo que demuestra el algoritmo de Euclides.

Sea $d = mcd(a, b)$. Entonces, por definición, $d \mid a$ y $d \mid b$.

Ahora representemos el resto de la división de $a$ por $b$ de la siguiente manera:
$$a \bmod b = a - b \cdot\Bigl\lfloor\dfrac{a}{b}\Bigr\rfloor$$

De esto se deduce que $d \mid (a \bmod b)$, lo que significa que tenemos el sistema de divisibilidades:
$$\begin{cases} d \mid b, \\\\ d \mid (a \mod b) \end{cases}$$

Ahora usamos el hecho de que para tres números cualesquiera $p$, $q$, $r$, si $p \mid q$ y $p \mid r$ entonces $p \mid mcd(q, r)$. En nuestro caso, obtenemos:
$$d = mcd(a, b) \mid mcd(b, a \mod b)$$

Por tanto, hemos demostrado que el lado izquierdo de la ecuación original divide al derecho. La segunda mitad de la prueba es similar.

## Complejidad temporal

El tiempo de ejecución del algoritmo se estima mediante el teorema de Lamé, que establece una conexión sorprendente entre el algoritmo euclidiano y la secuencia de Fibonacci:

Si $a > b \geq 1$ y $b < F_n$ para algún $n$, el algoritmo euclidiano realiza como máximo $n-2$ llamadas recursivas.

Además, es posible demostrar que el límite superior de este teorema es óptimo. Cuando $a = F_n$ y $b = F_{n-1}$, $mcd(a, b)$ realizará exactamente $n-2$ llamadas recursivas. En otras palabras, los números de Fibonacci consecutivos son la entrada del peor caso para el algoritmo de Euclides.

Dado que los números de Fibonacci crecen exponencialmente, obtenemos que el algoritmo euclidiano funciona en $O (\log \min(a, b))$.

## Minimo común multiplo

El cálculo del mínimo común múltiplo (comúnmente denominado **MCM**) se puede reducir a calcular el MCD con la siguiente fórmula simple:
$$\text{mcm}(a, b) = \frac{a \cdot b}{mcd(a, b)}$$

Por lo tanto, el MCM se puede calcular utilizando el algoritmo euclidiano con la misma complejidad de tiempo:

A continuación, se muestra una posible implementación que evita inteligentemente los desbordamientos de enteros dividiendo primero $a$ con el MCD:

```cpp
int mcm(int a, int b) {
    return a / mcm(a, b) * b;
}
```

## MCD binario

El algoritmo MCD binario es una optimización del algoritmo euclidiano normal.

La parte lenta del algoritmo normal son las operaciones de módulo. Las operaciones de módulo, aunque las vemos como $O (1)$, son mucho más lentas que las operaciones más simples como las operaciones de suma, resta u operaciones binarias.
Entonces sería mejor evitarlos.

Resulta que puede diseñar un algoritmo MCD rápido que evite operaciones de módulo.
Se basa en algunas propiedades:

  - Si ambos números son pares, entonces podemos factorizar el dos de ambos y calcular el MCD de los números restantes: $mcd(2a, 2b) = 2 \ mcd(a, b)$.
  - Si uno de los números es par y el otro es impar, entonces podemos quitar el factor 2 del par: $mcd(2a, b) = mcd(a, b)$ si $b$ es impar.
  - Si ambos números son impares, restar un número al otro no cambiará el MCD: $mcd(a, b) = mcd(b, ab)$ (esto se puede probar de la misma manera que la prueba de correctitud del algoritmo normal)

Usando solo estas propiedades y algunas funciones rápidas binarias de GCC, podemos implementar una versión rápida:

```cpp
int mcd(int a, int b) {
    if (!a || !b)
        return a | b;
    unsigned shift = __builtin_ctz(a | b);
    a >>= __builtin_ctz(a);
    do {
        b >>= __builtin_ctz(b);
        if (a > b)
            swap(a, b);
        b -= a;
    } while (b);
    return a << shift;
}
```

Tenga en cuenta que esta optimización no suele ser necesaria y la mayoría de los lenguajes de programación ya tienen una función GCD en sus bibliotecas estándar.
P.ej. C ++ 17 tiene tal función en el encabezado `numeric`.

## Problemas de práctica

- [Codechef - MCD y MCM] (https://www.codechef.com/problems/FLOW016)