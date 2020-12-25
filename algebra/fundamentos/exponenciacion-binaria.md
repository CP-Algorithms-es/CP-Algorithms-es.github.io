---
title: Exponenciación Binaria
---

{% include mathjax.html %}

# Exponenciación binaria

La exponenciación binaria (también conocida como exponenciación por cuadratura) es un truco que permite calcular $a^n$ usando solo $O(\log n)$ multiplicaciones (en lugar de $O(n)$ multiplicaciones requeridas por el enfoque ingenuo).

También tiene aplicaciones importantes en muchas tareas no relacionadas con la aritmética, ya que
se puede utilizar con cualquier operación que tenga la propiedad de **asociatividad**:

$$ (X \cdot Y) \cdot Z = X \cdot (Y \cdot Z) $$

Obviamente, esto se aplica a la multiplicación modular, a la multiplicación de matrices y
a otros problemas que discutiremos a continuación.

## Algoritmo

Elevar $a$ a la potencia de $n$ se expresa ingenuamente como una multiplicación por $a$ hecha $n - 1$ veces:
$a^{n} = a \cdot a \cdot \ldots \cdot a$. Sin embargo, este enfoque no es práctico para $a$ o $n$ grandes.

$a^{b + c} = a^b \cdot a^c$ y $a^{2b} = a^b \cdot a^b = (a^b)^2$.

La idea de la exponenciación binaria es que dividimos el trabajo usando la representación binaria del exponente.

Escribamos $n$ en base $2$, por ejemplo:
$$ 3^{13} = 3^{1101_2} = 3^8 \cdot 3^4 \cdot 3^1 $$

Dado que el número $n$ tiene exactamente $\lfloor \log_2 n \rfloor + 1$ dígitos en base 2, solo necesitamos realizar $O(\log n)$ multiplicaciones si conocemos las potencias $a^1, a^2, a^4, a^8, \dots, a^{\lfloor \log n \rfloor}$.

Así que solo necesitamos conocer una forma rápida de calcularlas.
Afortunadamente, esto es muy fácil, ya que un elemento de la secuencia es solo el cuadrado del elemento anterior.

$$\begin{align}
3^1 &= 3 \\
3^2 &= \left (3^1 \right)^2 = 3^2 = 9 \\
3^4 &= \left (3^2 \right)^2 = 9^2 = 81 \\
3^8 &= \left (3^4 \right)^2 = 81^2 = 6561
\end{align}$$

Entonces, para obtener la respuesta final para $3^{13}$, solo necesitamos multiplicar tres de ellos (omitiendo $3^2$ porque el bit correspondiente en $n$ no está configurado):
$3^{13} = 6561 \cdot 81 \cdot 3 = 1594323$

La complejidad final de este algoritmo es $O(\log n)$: tenemos que calcular $\log n$ potencias de $a$, y luego tenemos que hacer como máximo $\log n$ multiplicaciones para obtener la respuesta final de ellos.

El siguiente enfoque recursivo expresa la misma idea:

$$ a^n = \begin{cases}
1 & \text{si } n == 0 \\
\left (a^{\frac{n}{2}} \right)^2 & \text{si } n > 0 \text{ y } n \text{ par} \\
\left (a^{\frac{n - 1}{2}} \right)^2 \cdot a &\text{si } n > 0 \text{ y } n \text{ impar} \\
\end{cases} $$

## Implementación

Primero, el enfoque recursivo, que es una traducción directa de la fórmula recursiva:

```cpp
long long binpow (long long a, long long b) {
    if (b == 0)
        return 1;
    long long res = binpow(a, b/2);
    if (b % 2)
        return res * res * a;
    else
        return res * res;
}
```

El segundo enfoque realiza la misma tarea sin recursividad.
Calcula todas las potencias en un bucle y multiplica las que tienen el bit establecido correspondiente en $ n $.
Aunque la complejidad de ambos enfoques es idéntica, este enfoque será más rápido en la práctica ya que no tenemos la sobrecarga de las llamadas recursivas.

```cpp
long long binpow (long long a, long long b) {
    long long res = 1;
    while (b > 0) {
        if (b & 1)
            res = res * a;
        a = a * a;
        b >>= 1;
    }
    return res;
}
```

## Aplicaciones

### Cálculo efectivo de grandes exponentes módulo un número

**Problema:**
Calcule $ x^n \bmod m $.
Ésta es una operación muy común. Por ejemplo, se utiliza para calcular el [inverso multiplicativo modular](./algebra/fundamentos/inverso-modular.html).

**Solución:**
Como sabemos que el operador del módulo no interfiere con las multiplicaciones ($ a \cdot b \equiv (a \bmod m) \cdot (b \bmod m) \pmod m $), podemos usar directamente el mismo código y simplemente reemplace cada multiplicación con una multiplicación modular:

```cpp
long long binpow(long long a, long long b, long long m) {
    a% = m;
    long long res = 1;
    while (b > 0) {
        if (b & 1)
            res = res * a % m;
        a = a * a % m;
        b >>= 1;
    }
    return res;
}
```

**Nota:** Si $ m $ es un número primo, podemos acelerar un poco este algoritmo calculando $ x^{n \mod (m-1)} $ en lugar de $ x^n $.
Esto se sigue directamente del [pequeño teorema de Fermat](./algebra/inverso-modular.html#toc-tgt-2).

### Cálculo efectivo de números de Fibonacci

**Problema:** Calcule el $ n $-ésimo número de Fibonacci $ F_n $.

**Solución:** Para obtener más detalles, consulte el [artículo sobre el número de Fibonacci](.algebra/numeros-de-fibonacci.html).
Solo veremos una descripción general del algoritmo.
Para calcular el siguiente número de Fibonacci, solo se necesitan los dos anteriores, como $ F_n = F_{n-1} + F_{n-2} $.
Podemos construir una matriz de $ 2 \times 2 $ que describa esta transformación:
la transición de $ F_i $ y $ F_{i + 1} $ a $ F_{i + 1} $ y $ F_{i + 2} $.
Por ejemplo, aplicar esta transformación al par $ F_0 $ y $ F_1 $ lo cambiaría a $ F_1 $ y $ F_2 $.
Por lo tanto, podemos elevar esta matriz de transformación a la $ n $-ésima potencia para encontrar $ F_n $ en complejidad temporal $ O(\log n) $.

### Aplicar una permutación $ k $ veces

**Problema:** Dada una secuencia de longitud $ n $, aplicarle una
permutación dada $ k $ veces.

**Solución:** Simplemente eleve la permutación a la $ k $-ésima potencia usando exponenciación binaria, y luego aplíquela a la secuencia. Esto le dará una complejidad de tiempo de $ O(n \log k) $.

**Nota:** Esta tarea se puede resolver de manera más eficiente en tiempo lineal construyendo el gráfico de permutación y considerando cada ciclo de forma independiente. Luego, podría calcular $ k $ módulo el tamaño del ciclo y encontrar la posición final para cada número que es parte de este ciclo.

### Aplicación rápida de un conjunto de operaciones geométricas a un conjunto de puntos

**Problema:** Dados $ n $ puntos $ p_i $, aplique transformaciones de $ m $ a cada uno de estos puntos. Cada transformación puede ser un desplazamiento, una multiplicación por un escalar, o una rotación alrededor de un eje dado en un ángulo dado. También hay una operación de "bucle" que aplica una lista dada de transformaciones $ k $ veces (las operaciones de "bucle" se pueden anidar). Debe aplicar todas las transformaciones más rápido que $ O (n \cdot longitud) $, donde $ longitud $ es el número total de transformaciones que se aplicarán (después de desenrollar las operaciones de "bucle").

**Solución:** Veamos cómo los diferentes tipos de transformaciones cambian las coordenadas:

* Operación de cambio: agrega una constante diferente a cada una de las coordenadas.
* Operación de escala: multiplica cada una de las coordenadas por una constante diferente.
* Operación de rotación: la transformación es más complicada (no entraremos en detalles aquí), pero cada una de las nuevas coordenadas aún se puede representar como una combinación lineal de las antiguas.

Como puede ver, cada una de las transformaciones se puede representar como una operación lineal en las coordenadas. Por lo tanto, una transformación se puede escribir como una matriz de $ 4 \times 4 $ de la forma:

$$ \begin{pmatrix}
a_{11} & a_{12} & a_{13} & a_{14} \\\
a_{21} & a_{22} & a_{23} & a_{24} \\\
a_{31} & a_{32} & a_{33} & a_{34} \\\
a_{41} & a_{42} & a_{43} & a_{44}
\end{pmatrix} $$

que, cuando se multiplica por un vector con las coordenadas antiguas y una unidad da un nuevo vector con las nuevas coordenadas y una unidad:

$$ \begin{pmatrix} x & y & z & 1 \end{pmatrix} \cdot
\begin{pmatrix}
a_{11} & a_{12} & a_{13} & a_{14} \\\
a_{21} & a_{22} & a_{23} & a_{24} \\\
a_{31} & a_{32} & a_{33} & a_{34} \\\
a_{41} & a_{42} & a_{43} & a_{44}
\end{pmatrix}
 = \begin{pmatrix} x '& y' & z '& 1 \end{pmatrix} $$

(¿Por qué introducir una cuarta coordenada ficticia? Sin esto, no sería posible implementar la operación de desplazamiento, ya que requiere que agreguemos una constante a las coordenadas. Sin las coordenadas ficticias, solo podríamos aplicar una combinación lineal a las coordenadas, no pudiendo agregar una constante.)

A continuación, se muestran algunos ejemplos de cómo se representan las transformaciones en forma de matriz:

* Operación de cambio: cambiar la coordenada $ x $ por $ 5 $, la coordenada $ y $ por $ 7 $ y la coordenada $ z $ por $ 9 $.
$$ \begin{pmatrix}
1 & 0 & 0 & 0 \\\
0 & 1 & 0 & 0 \\\
0 & 0 & 1 & 0 \\\
5 & 7 & 9 & 1
\end{pmatrix} $$

* Operación de escala: escala la coordenada $ x $ en $ 10 $ y las otras dos en $ 5 $.
$$ \begin{pmatrix}
10 & 0 & 0 & 0 \\\
0 & 5 & 0 & 0 \\\
0 & 0 & 5 & 0 \\\
0 & 0 & 0 & 1
\end{pmatrix} $$

* Operación de rotación: gire $ \theta $ grados alrededor del eje $ x $ siguiendo la regla de la mano derecha (sentido antihorario).
$$ \begin{pmatrix}
1 & 0 & 0 & 0 \\\
0 & \cos \theta & -\sin \theta & 0 \\\
0 & \sin \theta & \cos \theta & 0 \\\
0 & 0 & 0 & 1
\end{pmatrix} $$

Ahora, una vez que cada transformación se describe como una matriz, la secuencia de transformaciones se puede describir como un producto de estas matrices, y un "bucle" de $ k $ repeticiones se puede describir como la matriz elevada a la potencia de $ k $ (que se puede calcular usando exponenciación binaria en $ O(\log{k}) $). De esta manera, la matriz que representa todas las transformaciones se puede calcular primero en $ O(m \log{k}) $, y luego se puede aplicar a cada uno de los $ n $ puntos en $ O(n) $ para un total complejidad de $ O(n + m \log{k}) $.


### Número de caminos de longitud $ k $ en un gráfico

**Problema:** Dado un gráfico dirigido sin pesos de $ n $ vértices, encuentre el número de caminos de longitud $ k $ desde cualquier vértice $ u $ a cualquier otro vértice $ v $.

**Solución:** Este problema se analiza con más detalle en [un artículo separado](./grafos/caminos-de-longitud-fija.html). El algoritmo consiste en elevar la matriz de adyacencia $ M $ del gráfico (una matriz donde $ m_{ij} = 1 $ si hay una arista de $ i $ a $ j $, o $ 0 $ en caso contrario) a la $ k $-ésimo potencia. Ahora $ m_{ij} $ será el número de caminos de longitud $ k $ desde $ i $ a $ j $. La complejidad temporal de esta solución es $ O(n^3 \log k) $.

**Nota:** En ese mismo artículo, se considera otra variación de este problema: cuando los ejes están ponderados y se requiere encontrar la ruta de peso mínimo que contenga exactamente $ k $ ejes. Como se muestra en ese artículo, este problema también se resuelve mediante exponenciación de la matriz de adyacencia. La matriz tendría el peso de la arista desde $ i $ a $ j $, o $ \infty $ si no existe dicha arista.
En lugar de la operación habitual de multiplicar dos matrices, se debe utilizar una modificada:
en lugar de multiplicación,
se suman los dos valores y, en lugar de una suma, se toma un mínimo.
Es decir: $ resultado_{ij} = \min \limits_{1 \ \leq\ k \ \leq\ n}(a_{ik} + b_{kj}) $.

### Variación de la exponenciación binaria: multiplicar dos números módulo $ m $

**Problema** Multiplique dos números $ a $ y $ b $ módulo $ m $. $ a $ y $ b $ encajan en tipos de datos integrados, pero su producto es demasiado grande para caber en un entero de 64 bits. La idea es calcular $ a \cdot b \pmod m $ sin usar aritmética de números grandes.

**Solución:** Simplemente aplicamos el algoritmo de construcción binaria descrito anteriormente, solo realizando sumas en lugar de multiplicaciones. En otras palabras, hemos "expandido" la multiplicación de dos números a $ O(\log m) $ operaciones de suma y multiplicación por dos (que, en esencia, es una suma).

$$ a \cdot b = \begin{cases}
0 & \text {si } a = 0 \\
2 \cdot \frac{a} {2} \cdot b & \text{si } a> 0 \text{ y } a \text{ par} \\
2 \cdot \frac{a-1} {2} \cdot b + b & \text{si } a> 0 \text{ and } a \text{ impar}
\end{cases} $$

**Nota:** Puede resolver esta tarea de una manera diferente utilizando operaciones de punto flotante. Primero calcule la expresión $ \frac{a \cdot b}{m} $ usando números de punto flotante y conviértala en un entero sin signo $ q $. Reste $ q \cdot m $ de $ a \cdot b $ usando aritmética de enteros sin signo y tómelo módulo $ m $ para encontrar la respuesta. Esta solución parece poco fiable, pero es muy rápida y muy fácil de implementar. Consulte [aquí](https://cs.stackexchange.com/questions/77016/modular-multiplication) para obtener más información.

## Problemas de práctica

* [UVa 1230 - MODEX](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=24&page=show_problem&problem=3671)
* [UVa 374 - Big Mod](http://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&category=24&page=show_problem&problem=310)
* [UVa 11029 - Leading and Trailing](https://uva.onlinejudge.org/index.php?option=onlinejudge&page=show_problem&problem=1970)
* [Codeforces - Parking Lot](http://codeforces.com/problemset/problem/630/I)
* [SPOJ - The last digit](http://www.spoj.com/problems/LASTDIG/)
* [SPOJ - Locker](http://www.spoj.com/problems/LOCKER/)
* [LA - 3722 Jewel-eating Monsters](https://icpcarchive.ecs.baylor.edu/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1723)
* [SPOJ - Just add it](http://www.spoj.com/problems/ZSUM/)