---
title: Algoritmo Euclidiano
---
{% include mathjax.html %}

**Tabla de contenido**

- [Números de Fibonacci](#números-de-fibonacci)
  - [Propiedades](#propiedades)
  - [Codificación Fibonacci](#codificación-fibonacci)
  - [Fórmulas para el n-ésimo número de Fibonacci](#fórmulas-para-el-n-ésimo-número-de-fibonacci)
    - [Expresión de forma cerrada](#expresión-de-forma-cerrada)
    - [Forma de matriz](#forma-de-matriz)
    - [Método de duplicación rápido](#método-de-duplicación-rápido)
  - [Periodicidad módulo p](#periodicidad-módulo-p)
  - [Problemas de práctica](#problemas-de-práctica)

# Números de Fibonacci

La secuencia de Fibonacci se define de la siguiente manera:

$$ F_0 = 0, F_1 ​​= 1, F_n = F_{n-1} + F_{n-2} $$

Los primeros elementos de la secuencia ([OEIS A000045](http://oeis.org/A000045)) son:

$$ 0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89, ... $$

## Propiedades

Los números de Fibonacci poseen muchas propiedades interesantes. Estas son algunas de ellas:

* Identidad de Cassini:
  $$ F_{n-1} F_{n + 1} - F_n ^ 2 = (-1) ^ n $$

* La regla de la "adición":
  $$ F_{n + k} = F_k F_{n + 1} + F_{k-1} F_n $$

* Aplicando la identidad anterior al caso $ k = n $, obtenemos:
  $$ F_{2n} = F_n (F_{n + 1} + F_{n-1}) $$

* A partir de esto podemos probar por inducción que para cualquier entero positivo $ k $, $ F_{nk} $ es múltiplo de $ F_n $.

* Lo inverso también es cierto: si $ F_m $ es múltiplo de $ F_n $, entonces $ m $ es múltiplo de $ n $.

* Identidad MCD:
  $$ MCD(F_m, F_n) = F_{MCD(m, n)} $$

* Los números de Fibonacci son las peores entradas posibles para el algoritmo euclidiano (consulte el teorema de Lamé en [algoritmo euclidiano](./algebra/fundamentos/algoritmo-euclidiano.html))

## Codificación Fibonacci

Podemos usar la secuencia para codificar enteros positivos en palabras de código binario. Según el teorema de Zeckendorf, cualquier número natural $ n $ se puede representar de forma única como una suma de números de Fibonacci:

$$ N = F_{k_1} + F_{k_2} + \ldots + F_{k_r} $$

tal que $ k_1 \ge k_2 + 2,\ k_2 \ge k_3 + 2,\ \ldots,\ k_r \ge 2 $ (es decir, la representación no puede usar dos números de Fibonacci consecutivos).

De ello se deduce que cualquier número puede codificarse de forma única en la codificación de Fibonacci.
Y podemos describir esta representación con códigos binarios $ d_0 d_1 d_2 \dots d_s 1 $, donde $ d_i $ es $ 1 $ si se usa $ F_{i + 2} $ en la representación.
El código se adjuntará con un $ 1 $ que indique el final de la palabra del código.
Observe que este es el único caso en el que aparecen dos bits 1 consecutivos.

$$ \begin{eqnarray}
1 &=& 1 &=& F_2 &=& (11) _F \\
2 &=& 2 &=& F_3 &=& (011) _F \\
6 &=& 5 + 1 &=& F_5 + F_2 &=& (10011) _F \\
8 &=& 8 &=& F_6 &=& (000011) _F \\
9 &=& 8 + 1 &=& F_6 + F_2 &=& (100011) _F \\
19 &=& 13 + 5 + 1 &=& F_7 + F_5 + F_2 &=& (1001011) _F
\end{eqnarray} $$

La codificación de un entero $ n $ se puede hacer con un algoritmo codicioso simple:

1. Repita los números de Fibonacci del mayor al menor hasta que encuentre uno menor o igual a $ n $.

2. Suponga que este número es $ F_i $. Reste $ F_i $ de $ n $ y coloque $ 1 $ en la posición $ i-2 $ del resultado (indexando desde 0 desde el bit más a la izquierda al más a la derecha).

3. Repita hasta que no quede resto.

4. Agregue un $ 1 $ final al resultado para indicar su final.

Para decodificar un número codificado, primero elimine el $ 1 $ final. Entonces, si el $ i $ -ésimo bit está activado (indexando desde 0 desde el bit más a la izquierda al más a la derecha), sume $ F_{i + 2} $ al número.

## Fórmulas para el n-ésimo número de Fibonacci

El $ n $-ésimo número de Fibonacci se puede encontrar fácilmente en $ O(n) $ calculando los números uno por uno hasta $ n $. Sin embargo, también hay formas más rápidas, como veremos.

### Expresión de forma cerrada

Existe una fórmula conocida como "fórmula de Binet", aunque ya era conocida por Moivre:

$$ F_n = \frac{\left (\frac{1 + \sqrt{5}} {2} \right) ^ n - \left (\frac{1 - \sqrt{5}} {2} \right) ^ n} {\sqrt{5}} $$

Esta fórmula es fácil de probar por inducción, pero se puede deducir con la ayuda del concepto de funciones generadoras o resolviendo una ecuación funcional.

Puede notar inmediatamente que el valor absoluto del segundo término es siempre menor que $ 1 $, y también disminuye muy rápidamente (exponencialmente). Por tanto, el valor del primer término solo es "casi" $ F_n $. Esto se puede escribir estrictamente como:

$$ F_n = \left [\frac{\left (\frac{1 + \sqrt{5}} {2} \right) ^ n} {\sqrt{5}} \right] $$

donde los corchetes denotan redondeo al número entero más cercano.

Como estas dos fórmulas requerirían una precisión muy alta al trabajar con números fraccionarios, son de poca utilidad en cálculos prácticos.

### Forma de matriz

Es fácil probar la siguiente relación:

$$ \begin{pmatrix} F_{n-1} & F_ {n} \cr\end{pmatrix} = \begin{pmatrix} F_{n-2} & F_{n-1} \cr\end{pmatrix} \cdot \begin{pmatrix} 0 & 1 \cr 1 & 1 \cr\end{pmatrix} $$

Denotando $ P \equiv \begin{pmatrix} 0 & 1 \cr 1 & 1 \cr\end{pmatrix} $, tenemos:

$$ \begin{pmatrix} F_n & F_{n + 1} \cr\end{pmatrix} = \begin{pmatrix} F_0 & F_1 \cr\end{pmatrix} \cdot P ^ n $$

Por tanto, para encontrar $ F_n $, debemos elevar la matriz $ P $ a $ n $. Esto se puede hacer en $ O (\log n) $ (ver [Exponenciación binaria](./algebra/fundamentos/exponenciacion-binaria.html)).

### Método de duplicación rápido

Usando el método anterior podemos encontrar estas ecuaciones:

$$ \begin{array}{rll}
    F_ {2k} &= F_k \left (2F_ {k + 1} - F_ {k} \right). \\
    F_ {2k + 1} &= F_ {k + 1} ^ 2 + F_ {k} ^ 2.
\end{array} $$

Por lo tanto, utilizando las dos ecuaciones anteriores, los números de Fibonacci se pueden calcular fácilmente mediante el siguiente código:

```cpp
pair<int, int> fib(int n) {
    if (n == 0)
        return {0, 1};

    auto p = fib(n >> 1);
    int c = p.first * (2 * p.second - p.first);
    int d = p.first * p.first + p.second * p.second;
    if (n & 1)
        return {d, c + d};
    else
        return {c, d};
}
```

El código anterior devuelve $ F_n $ y $ F_ {n + 1} $ como un par.

## Periodicidad módulo p

Considere la secuencia de Fibonacci módulo $ p $. Demostraremos que la secuencia es periódica y el período comienza con $ F_1 = 1 $ (es decir, el preperíodo contiene solo $ F_0 $).

Demostremos esto por contradicción. Considere los primeros $ p ^ 2 + 1 $ pares de números de Fibonacci tomados en módulo $ p $:

$$ (F_1, \ F_2), \ (F_2, \ F_3), \ \ldots, \ (F_ {p ^ 2 + 1}, \ F_ {p ^ 2 + 2}) $$

Solo puede haber $ p $ residuos diferentes módulo $ p $, y como máximo $ p ^ 2 $ residuos diferentes, por lo que hay al menos dos pares idénticos entre ellos. Por tanto, la secuencia es periódica.

Ahora elegimos dos pares de residuos idénticos con los índices más pequeños de la secuencia. Sean los pares $ (F_a, \ F_ {a + 1}) $ y $ (F_b, \ F_ {b + 1}) $. Demostraremos que $ a = 1 $. Si esto fuera falso, habría dos pares anteriores $ (F_ {a-1}, \ F_a) $ y $ (F_ {b-1}, \ F_b) $, que, por la propiedad de los números de Fibonacci, también serían iguales. Sin embargo, esto contradice el hecho de que habíamos elegido pares con los índices más pequeños, completando nuestra demostración.

## Problemas de práctica

* [SPOJ - Euclid Algorithm Revisited](http://www.spoj.com/problems/MAIN74/)
* [SPOJ - Fibonacci Sum](http://www.spoj.com/problems/FIBOSUM/)
* [HackerRank - Is Fibo](https://www.hackerrank.com/contests/codesprint5/challenges/is-fibo/problem)
* [Project Euler - Even Fibonacci numbers](https://www.hackerrank.com/contests/projecteuler/challenges/euler002/problem)
* [DMOJ - Fibonacci Sequence](https://dmoj.ca/problem/fibonacci)
* [DMOJ - Fibonacci Sequence (Harder)](https://dmoj.ca/problem/fibonacci2)
