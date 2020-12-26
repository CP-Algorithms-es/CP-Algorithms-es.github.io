---
title: Ecuaciones diofánticas lineales
---

{% include mathjax.html %}

**Tabla de contenido**

  - [El caso especial](#el-caso-especial)
  - [Encontrar una solución](#encontrar-una-solución)
  - [Consiguiendo todas las soluciones](#consiguiendo-todas-las-soluciones)
  - [Encontrando el numero de soluciones y las soluciones en un intervalo dado](#encontrando-el-numero-de-soluciones-y-las-soluciones-en-un-intervalo-dado)
  - [Encontrar la solución con mínimo valor de la suma de x e y](#encontrar-la-solución-con-mínimo-valor-de-la-suma-de-x-e-y)
  

Una ecuación diofántica lineal (en dos variables) es una ecuación general de la forma:

$$ ax + by = c $$

donde $a$, $b$, $c$ son enteros conocidos y $x$ e $y$ son variables desconocidas.

En este articulo, vamos a considerar algunos problemas clásicos sobre estas ecuaciones.

* Encontrar una solución
* Encontrar todas las soluciones
* Encuentre el numero de solución y de soluciones dado un intervalo
* Encontrar una solución con un el mínimo valor de $x+y$

# El caso especial

Un caso especial que debe ser tenido en cuenta es cuando $a = b = 0$. Es fácil ver que podemos tener tanto infinitas soluciones como ninguna, dependiendo si $c = 0$ o no. En el resto del articulo, vamos a ignorar este caso.

# Encontrar una solución

Para encontrar una solución de la ecuación Diofántica con dos variables desconocidas, podemos usar el algoritmo de Euclides Extendido. Primero, asumamos que $a$ y $b$ son dos números positivos. Cuando aplicamos el algoritmo de Euclides Extendido para $a$ y $b$, podemos encontrar su  máximo común divisor $g$ y dos números $x_g$ e $y_g$ tales que:
    
$$ ax_g + by_g = g $$

Si $c$ es divisible por $g = mcd(a,b)$, entonces la ecuación diofántica dada tiene solución, en caso contrario, no tiene ninguna. La demostración es bastante directa: una combinación lineal de dos números es divisible por su divisor común.

Ahora, supongamos que $c$ es divisible por $g$, entonces tenemos que:

$$ a x_g (\frac{c}{g}) + b y_g (\frac{c}{g}) = c $$

Por lo tanto, una de las soluciones de la ecuación Diofántica es:

$$ x_0 = x_g (\frac{c}{g}) $$
$$ y_0 = y_g (\frac{c}{g}) $$

La idea de arriba todavía funciona en caso de que $a$ o $b$ sea alguno o ambos negativos. Solo necesitamos cambiar el signo de $x_0$ y de $y_0$ cuando sea necesario.

Finalmente, podemos implementar esta idea como sigue (notar que este código no considera el caso $a=b=0$):

```cpp 
int gcd(int a, int b, int& x, int& y) {
    if (b == 0) {
        x = 1;
        y = 0;
        return a;
    }
    int x1, y1;
    int d = gcd(b, a % b, x1, y1);
    x = y1;
    y = x1 - y1 * (a / b);
    return d;
}

bool find_any_solution(int a, int b, int c, int &x0, int &y0, int &g) {
    g = gcd(abs(a), abs(b), x0, y0);
    if (c % g) {
        return false;
    }

    x0 *= c / g;
    y0 *= c / g;
    if (a < 0) x0 = -x0;
    if (b < 0) y0 = -y0;
    return true;
}
```

# Consiguiendo todas las soluciones:

Desde una solución particular $(x_0, y_0)$, podemos obtener todas las soluciones posibles de la ecuación dada.

Sea $g = mcd(a,b)$ y sea $x_0, y_0$ números enteros tales que satisfagan la siguiente ecuación:

$$ ax_0 + by_0 = c $$ 

Ahora, consideremos ver que sumando $\frac{b}{g}$ a $x_0$ y al mismo tiempo restando $\frac{a}{g}$ a $y_0$, la igualdad no se rompe:

$$ a(x_0+\frac{b}{g})+b(y_0-\frac{a}{g}) = a x_0 + b y_0 + a (\frac{b}{g})-b (\frac{a}{g}) = c $$

Obviamente, este proceso puede ser repetido nuevamente, por lo tanto, todos los números de la forma 

$$ x = x_0 + k (\frac{b}{g}) $$
$$ y = y_0 - k (\frac{a}{g}) $$

son soluciones de la ecuación Diofántica.

Además, este es el conjunto de todas las posibles soluciones.

# Encontrando el numero de soluciones y las soluciones en un intervalo dado

De la sección previa, debe haber quedado claro que si no imponemos restricciones en las soluciones, entonces podemos tener infinitas de ellas. Entonces, en esta sección agregaremos algunas restricciones en el intervalo de $x$ e $y$. Vamos a tratar de contar y enumerar todas las soluciones.

Sean dos intervalos: $[min_x;max_x]$ y $[min_y;max_y]$ y supongamos que solo queremos encontrar las soluciones en esos dos intervalos.
Notar que si $a$ o $b$ es 0, entonces el problema solo tiene una única solución. No vamos a considerar ese caso acá.

Primero, podemos encontrar una solución que tenga el mínimo valor de x, tal que $x \geq min_X$. Para hacer esto, necesitamos encontrar alguna solución de la ecuación Diofántica. Luego, variamos esta solución para obtener $x  \geq min_x$ (usando lo que ya sabemos acerca del conjunto de todas las soluciones de la sección previa). Podemos hacer esto en $O(1)$. Denotemos el mínimo valor de x por $l_{x1}$.

De forma similar, podemos encontrar el mínimo valor de $y$ $(y \geq min_y)$ y el máximo valor de $y$ $(y \leq max_x)$. Denotemos los correspondientes valores de $x$ como $l_{x2}$ y $r_{x2}$.

La solución final es toda solución de $x$ en intersección con los intervalos $[l_{x1}, r_{x1}]$ y $[l_{x2},r_{x2}]$. Llamemos esta intersección como $[l_x,r_x]$.

El siguiente código sigue los lineamientos de esta idea. Notar que dividimos $a$ y $b$ al principio por $g$. Ya que la ecuación $ax + by = c$ es equivalente a la ecuación $(\frac{a}{g})x + (\frac{b}{g})y = \frac{c}{g}$, podemos usar la ultima en vez de la primera y tener mcd($\frac{a}{g}x$,$\frac{b}{g}y$) = 1, que simplifica la fórmula.


```cpp 
void shift_solution(int & x, int & y, int a, int b, int cnt) {
    x += cnt * b;
    y -= cnt * a;
}

int find_all_solutions(int a, int b, int c, int minx, int maxx, int miny, int maxy) {
    int x, y, g;
    if (!find_any_solution(a, b, c, x, y, g))
        return 0;
    a /= g;
    b /= g;

    int sign_a = a > 0 ? +1 : -1;
    int sign_b = b > 0 ? +1 : -1;

    shift_solution(x, y, a, b, (minx - x) / b);
    if (x < minx)
        shift_solution(x, y, a, b, sign_b);
    if (x > maxx)
        return 0;
    int lx1 = x;

    shift_solution(x, y, a, b, (maxx - x) / b);
    if (x > maxx)
        shift_solution(x, y, a, b, -sign_b);
    int rx1 = x;

    shift_solution(x, y, a, b, -(miny - y) / a);
    if (y < miny)
        shift_solution(x, y, a, b, -sign_a);
    if (y > maxy)
        return 0;
    int lx2 = x;

    shift_solution(x, y, a, b, -(maxy - y) / a);
    if (y > maxy)
        shift_solution(x, y, a, b, sign_a);
    int rx2 = x;

    if (lx2 > rx2)
        swap(lx2, rx2);
    int lx = max(lx1, lx2);
    int rx = min(rx1, rx2);

    if (lx > rx)
        return 0;
    return (rx - lx) / abs(b) + 1;
}
```

Una vez que tenemos $l_x$ y $r_x$, es también simple enumerar por sobre todas las soluciones. Solo necesitamos iterar desde $x = l_x + k (\frac{b}{g})$ para todo $k \geq 0$ hasta $x = r_x$, y encontrar los correspondientes valores de $y$ usando la ecuación $ax + by = c$

# Encontrar la solución con mínimo valor de la suma de x e y

Ahora, $x$ e $y$ necesitan también alguna restricción, sino, la respuesta puede convertirse infinitamente negativa.

La idea es similar a la sección previa. Tenemos que encontrar cualquier solución particular de la ecuación Diofántica, y luego variar la solución hasta satisfacer algunas condiciones.

Finalmente, hay que usar el conjunto de todas las soluciones para encontrar el mínimo.

$$ x' = x + k \frac{b}{g}$$

$$ y' = y - k  \frac{a}{g}$$

Notar que $x+y$ cambia de la siguiente forma:

$$ x' + y' = x + y + k(\frac{b}{g}-\frac{a}{g}) = x + y + k \frac{b-a}{g}$$

Si $a < b$, tenemos que seleccionar el mínimo valor posible de $k$.

Si $a > b$, tenemos que seleccionar el máximo valor posible de $k$.

Si $a = b$, todas las soluciones van a tener la misma suma $x + y$.

## Practice Problems

* [Spoj - Crucial Equation](http://www.spoj.com/problems/CEQU/)
* [SGU 106](http://codeforces.com/problemsets/acmsguru/problem/99999/106)
* [Codeforces - Ebony and Ivory](http://codeforces.com/contest/633/problem/A)
* [Codechef - Get AC in one go](https://www.codechef.com/problems/COPR16G)
* [LightOj - Solutions to an equation](http://www.lightoj.com/volume_showproblem.php?problem=1306)
