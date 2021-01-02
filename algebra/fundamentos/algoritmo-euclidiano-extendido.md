---
title: Algoritmo Euclidiano
---

{% include mathjax.html %}

# Algoritmo euclidiano extendido

Mientras que el [algoritmo euclidiano](../algebra/fundamentos/algoritmo-euclidiano.html) calcula solo el máximo común divisor (MCD) de dos enteros $ a $ y $ b $, la versión extendida también encuentra una manera de representar el MCD en términos de $ a $ y $ b $, es decir, encuentra coeficientes $ x $ y $ y $ para los cuales:

$$ a \cdot x + b \cdot y = \gcd(a, b) $$

Es importante tener en cuenta que siempre podemos encontrar dicha representación, por ejemplo
$ \gcd(55, 80) = 5 $, por lo que podemos representar $ 5 $ como una combinación lineal con los términos $ 55 $ y $ 80 $: $ 55 \cdot 3 + 80 \cdot (-2) = 5 $

Una forma más general de ese problema se analiza en el artículo sobre [Ecuaciones diofánticas lineales](algebra/fundamentos/ecuaciones-diofanticas-lineales.html).
Se basará en este algoritmo.

## Algoritmo

En esta sección, denotaremos el MCD de $ a $ y $ b $ con $ g $.

Los cambios al algoritmo original son muy simples.
Si recordamos el algoritmo, podemos ver que el algoritmo termina con $ b = 0 $ y $ a = g $.
Para estos parámetros podemos encontrar fácilmente coeficientes, a saber $ g \cdot 1 + 0 \cdot 0 = g $.

A partir de estos coeficientes $ (x, y) = (1, 0) $, podemos retroceder en las llamadas recursivas.
Todo lo que tenemos que hacer es averiguar cómo cambian los coeficientes $ x $ y $ y $ durante la transición de $ (a, b) $ a $ (b, a \bmod b) $.

Supongamos que encontramos los coeficientes $ (x_1, y_1) $ para $ (b, a \bmod b) $:

$$ b \cdot x_1 + (a \bmod b) \cdot y_1 = g $$

y queremos encontrar el par $ (x, y) $ para $ (a, b) $:

$$ a \cdot x + b \cdot y = g $$

Podemos representar $ a \bmod b $ como:

$$ a \bmod b = a - \left \lfloor \frac {a} {b} \right \rfloor \cdot b $$

Sustituyendo esta expresión en la ecuación del coeficiente de $ (x_1, y_1) $ da:

$$ g = b \cdot x_1 + (a \bmod b) \cdot y_1 = b \cdot x_1 + \left (a - \left \lfloor \frac {a} {b} \right \rfloor \cdot b \right ) \cdot y_1 $$

y después de reorganizar los términos:

$$ g = a \cdot y_1 + b \cdot \left (x_1 - y_1 \cdot \left \lfloor \frac {a} {b} \right \rfloor \right) $$

Encontramos los valores de $ x $ y $ y $:

$$ \begin{cases}
x = y_1 \\
y = x_1 - y_1 \cdot \left \lfloor \frac {a} {b} \right \rfloor
\end{cases} $$

## Implementación

```cpp mcd_extendido
int mcd(int a, int b, int & x, int & y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    int x1, y1;
    int d = mcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}
```

La función recursiva anterior devuelve el MCD y asigna los valores de los coeficientes a `x` e `y` (que se pasan por referencia a la función).

Esta implementación del algoritmo euclidiano extendido también produce resultados correctos para enteros negativos.

## Versión iterativa

También es posible escribir el algoritmo euclidiano extendido de forma iterativa.
Debido a que evita la recursividad, el código se ejecutará un poco más rápido que el recursivo.

```cpp mcd_extendido_iter
int mcd(int a, int b, int& x, int& y) {
    x = 1, y = 0;
    int x1 = 0, y1 = 1, a1 = a, b1 = b;
    while (b1) {
        int q = a1 / b1;
        tie(x, x1) = make_tuple(x1, x - q * x1);
        tie(y, y1) = make_tuple(y1, y - q * y1);
        tie(a1, b1) = make_tuple(b1, a1 - q * b1);
    }
    return a1;
}
```

Si observas de cerca las variables `a1` y ` b1`, podrás notar que toman exactamente los mismos valores que en la versión iterativa del [algoritmo euclidiano](algebra/fundamentos/algoritmo-euclidiano.html) normal. Entonces al menos el algoritmo calculará el MCD correcto.

Para ver por qué el algoritmo también calcula los coeficientes correctos, puedes verificar que las siguientes invariantes se mantendrán en cualquier momento (antes del ciclo while y al final de cada iteración): $ x \cdot a + y \cdot b = a_1 $ y $ x_1 \cdot a + y_1 \cdot b = b_1 $.
Es trivial ver que estas dos ecuaciones se satisfacen al principio.
Y puedes verificar que la actualización en la iteración del bucle aún mantendrá esas igualdades válidas.

Al final, sabemos que $ a_1 $ contiene el MCD, entonces $ x \cdot a + y \cdot b = g $.
Lo que significa que hemos encontrado los coeficientes requeridos.

Incluso puedes optimizar más el código y eliminar la variable $ a_1 $ y $ b_1 $ del mismo, y simplemente reutilizar $ a $ y $ b $.
Sin embargo, si lo haces, pierdes la capacidad de discutir sobre las invariantes.

## Problemas de práctica

* [10104 - Euclid Problem](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=1045)
* [GYM - (J) Once Upon A Time](http://codeforces.com/gym/100963)
* [UVA - 12775 - Gift Dilemma](https://uva.onlinejudge.org/index.php?option=com_onlinejudge&Itemid=8&page=show_problem&problem=4628)
