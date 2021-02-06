---
title: Tests de primalidad
---

{% include mathjax.html %}


**Tabla de contenido**

- [División por tentativa](#división-por-tentativa)
- [Test de Fermat](#test-de-fermat)
- [Test de primalidad de Miller-Rabin](#test-de-primalidad-de-miller-rabin)
- [Versión determinística](#versión-determinística)
- [Problemas de práctica](#problemas-de-práctica)

## División por tentativa

Por definición un número primo no tiene otros divisores que $1$ y sí mismo. Un número compuesto tiene por lo menos un divisor adicional, vamos a llamarlo $d$. Naturalmente $\frac{n}{d}$ también es un divisor de $n$. Se puede ver fácilmente que tanto $d \le \sqrt{n}$ o $\frac{n}{d} \le \sqrt{n}$, por lo tanto uno de los divisores $d$  y $\frac{n}{d}$ es $\le \sqrt{n}$.
Podemos usar esta información para comprobar la primalidad.

Podemos tratar de encontrar un divisor no trivial comprobando si alguno de los números entre $2$ y $\sqrt{n}$ es un divisor de $n$.
Si se encuentra algún divisor, definitivamente $n$ no es un primo, en caso contrario, lo es.

```cpp
bool isPrime(int x) {
    for (int d = 2; d * d <= x; d++) {
        if (x % d == 0)
            return false;
    }
    return true;
}
```

Esta es la forma más simple de comprobar si un número es primo. Se puede optimizar un poco esta función, por ejemplo solo comprobando los números impares, ya que el único número primo par es el 2.
Múltiples optimizaciones están descritas en el artículo de factorización de enteros (link)

## Test de Fermat

Es un test probabilistico.

El pequeño teorema de Fermat afirma, que para un número primo $p$ y un entero coprimo $a$ con p, la siguiente ecuaciones de congruencia es válida:

$$a^{p-1} \equiv 1 \bmod p$$

En general, este teorema no se mantiene para números compuestos.

Esto puede ser usado para crear un test de primalidad.
Podemos agarrar un entero $2 \le a \le p - 2$, y comprobar si la ecuación es válida o no. Si no es válida, por ejemplo $a^{p-1} \not\equiv 1 \bmod p$, sabemos que $p$ no puede ser un número primo.
En este caso, llamaremos a la base $a$ como *Testigo de Fermat* para la composición de $p$.

Sin embargo, puede ser posible que la ecuación sea válida para números compuestos.
Si la ecuación se mantiene, no tenemos una prueba certera de primalidad.
Solo podemos decir que $p$ es *probablemente primo*.
Si el número resulta ser un número compuesto, llamamos a la base $a$ como base *Mentirosa de Fermat*.

Corriendo el test para todas las posibles bases $a$, podemos realmente probar que el número efectivamente es primo. Sin embargo, esto no es hecho en la práctica, ya que se debe realizar mucho más esfuerzo que simplemente hacer *División por tentativa*.
En cambio, el test se puede repetir múltiples veces con opciones aleatorias de $a$.
Si no encontramos bases testigos para la composición, es bastante probable que el número sea efectivamente un primo.


```cpp
bool probablyPrimeFermat(int n, int iter=5) {
    if (n < 4)
        return n == 2 || n == 3;

    for (int i = 0; i < iter; i++) {
        int a = 2 + rand() % (n - 3);
        if (binpower(a, n - 1, n) != 1)
            return false;
    }
    return true;
}
```

Usamos (LINK, exponenciación Binaria), para computar eficientemente la potencia de $a^{p-1}$.

Hay una mala noticia sin embargo:
Existen algunos números compuestos donde $a^{n-1} \equiv 1 \bmod n$ es valido para cualquier $a$ coprimo con n, por ejemplo el número $561 = 3 \cdot 11 \cdot 17$.
Estos números se llaman "Números de Carmichael".
El test de Fermat sólo puede identificar estos números como compuestos en caso que tengamos la suerte que se elija una base $a$ con $\gcd(a, n) \ne 1$.

El test es todavía usado en la práctica, ya que es bastante rápido y los números de Carmichael son bastante raros.
Por ejemplo, solo existen 646 de estos números más pequeños de $10^9$

## Test de primalidad de Miller-Rabin

El test de Miller-Rabin extiende las ideas del test de Fermat.

Para un número impar $n$, $n-1$ es par y podemos encontrar todas sus factores que son potencias de dos.
Podemos escribir:
$$n - 1 = 2^s \cdot d,~\text{con}~d~\text{impar}.$$
Esto nos permite factorizar la ecuación del Pequeño Teorema de Fermat:
$$\begin{array}{rl}
a^{n-1} \equiv 1 \bmod n &\Longleftrightarrow a^{2^s d} - 1 \equiv 0 \bmod n \\\\
&\Longleftrightarrow (a^{2^{s-1} d} + 1) (a^{2^{s-1} d} - 1) \equiv 0 \bmod n \\\\
&\Longleftrightarrow (a^{2^{s-1} d} + 1) (a^{2^{s-2} d} + 1) (a^{2^{s-2} d} - 1) \equiv 0 \bmod n \\\\
&\quad\vdots \\\\
&\Longleftrightarrow (a^{2^{s-1} d} + 1) (a^{2^{s-2} d} + 1) \cdots (a^{d} + 1) (a^{d} - 1) \equiv 0 \bmod n \\\\
\end{array}$$

Si $n$ es primo, entonces $n$ tiene que dividir a uno de estos factores (recordar que los números primos tienen la propiedad que si d | a * b entonces d|a o d|b, que es algo que no se cumple necesariamente para números compuestos)
y en el test podemos comprobar exactamente esta afirmación, con lo cual, es una versión más estricta que la afirmación del test de Fermat.
Para una base $2 \le a \le n-2$ comprobamos si
$$a^d \equiv 1 \bmod n$$
o
$$a^{2^r d} \equiv -1 \bmod n$$
se cumple para algún $0 \le r \le s - 1$.

Si encontramos una base $a$ que no satisface alguna de las ecuaciones de arriba, entonces encontramos un *testigo* para la composición de $n$.
En este caso probamos que $n$ no es un número primo.

De forma similar al test de Fermat, es también posible que el conjunto de ecuaciones pueda ser satisfecho por un número compuesto.
En ese caso, la base $a$ se denomina *fuertemente mentirosa* (strong liar).
Si una base $a$ satisface la ecuación (una de ellas), $n$ es solo un *primo fuertemente probable*.
Sin embargo, no hay números como los de Carmichael, donde todas sus bases no triviales son mentirosas.
Incluso es posible demostrar, que hay al menos $\frac{1}{4}$ de las bases que son fuertemente mentirosas.
Si $n$ es compuesta, tenemos una probabilidad de $\ge 75\%$ que una base aleatoria nos pueda decir que es compuesto.
Realizando este procedimiento con múltiples iteraciones, eligiendo diferentes bases al azar, podemos decir con una alta probabilidad si el número es verdaderamente primo o es compuesto.

Aquí una implementación para enteros de 64 bits.


```cpp
using u64 = uint64_t;
using u128 = __uint128_t;

u64 binpower(u64 base, u64 e, u64 mod) {
    u64 result = 1;
    base %= mod;
    while (e) {
        if (e & 1)
            result = (u128)result * base % mod;
        base = (u128)base * base % mod;
        e >>= 1;
    }
    return result;
}

bool check_composite(u64 n, u64 a, u64 d, int s) {
    u64 x = binpower(a, d, n);
    if (x == 1 || x == n - 1)
        return false;
    for (int r = 1; r < s; r++) {
        x = (u128)x * x % n;
        if (x == n - 1)
            return false;
    }
    return true;
};

bool MillerRabin(u64 n, int iter=5) { // devuelve verdadero si n es probablemente primo, sino devuelve falso.
    if (n < 4)
        return n == 2 || n == 3;

    int s = 0;
    u64 d = n - 1;
    while ((d & 1) == 0) {
        d >>= 1;
        s++;
    }

    for (int i = 0; i < iter; i++) {
        int a = 2 + rand() % (n - 3);
        if (check_composite(n, a, d, s))
            return false;
    }
    return true;
}
```

Antes de realizar el test, se puede adicionalmente probar si alguno de los primeros números primos es un divisor.
Esto puede aumentar la velocidad bastante, ya que la mayoría de los números compuestos tienen números primos muy pequeños como divisores.
Por ejemplo, $88\%$ de todos los números tienen un factor primo más pequeño que $100$.

### Versión determinística

Miller mostró que es posible hacer el algoritmo determinístico solamente comprobando todas las bases $\le O((\ln n)^2)$.
Bach luego dio una cota concreta, tal que solo sea necesario comprobar todas las bases $a \le 2 \ln(n)^2$.

Esto sin embargo sigue siendo un número bastante grande de bases.
Algunas personas han utilizado bastante poder de cómputo para encontrar cotas inferiores.
Resultó que, para comprobar un número entero de 32 bit es solo necesario comprobar las primeras 4 bases: 2,3, 5 y 7.
El número compuesto más pequeño que falla este test es $3,215,031,751 = 151 \cdot 751 \cdot 28351$.
y para comprobar enteros de 64 bits, es suficiente comprobar las primeras 12 números primos: 2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31 y 37.
Esto resulta en la siguiente implementación determinista
```cpp
bool MillerRabin(u64 n) { // returns true if n is prime, else returns false.
    if (n < 2)
        return false;

    int r = 0;
    u64 d = n - 1;
    while ((d & 1) == 0) {
        d >>= 1;
        r++;
    }

    for (int a : {2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37}) {
        if (n == a)
            return true;
        if (check_composite(n, a, d, r))
            return false;
    }
    return true;
}
```
Es también posible comprobar solo con 7 bases: 2, 325, 9375, 28178, 450775, 9780504 y 1795265022.
Sin embargo, como estos números no son primos (excepto el 2), hay que comprobar adicionalmente si el número que se esta mirando es igual a algún divisor primo de las siguiente bases: 2, 3, 5, 13, 19, 73, 193, 407521, 299210837.

## Problemas de práctica

- [SPOJ - Prime or Not](https://www.spoj.com/problems/PON/)