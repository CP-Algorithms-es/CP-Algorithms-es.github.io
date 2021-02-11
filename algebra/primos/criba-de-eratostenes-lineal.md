---
title: Criba de Eratóstenes con Complejidad Temporal Lineal
---

{% include mathjax.html %}

**Tabla de contenido**

- [Criba de Eratóstenes con Complejidad Temporal Lineal](#criba-de-eratóstenes-con-complejidad-temporal-lineal)
  - [Algoritmo](#algoritmo)
  - [Implementación](#implementación)
  - [Demostración de correctitud](#demostración-de-correctitud)
  - [Complejidad Temporal y Espacial](#complejidad-temporal-y-espacial)
  - [Referencias](#referencias)

# Criba de Eratóstenes con Complejidad Temporal Lineal

Dado un número $n$, encontrar todos los números primos en un segmento $[2;n]$.

La forma estándar de resolver este problema es usar una [criba de Eratóstenes](./criba-de-eratostenes.md). Este algoritmo es muy simple, pero tiene complejidad temporal $O(n \log \log n)$.

Aunque existen muchos algoritmos conocidos con complejidad sublineal (es decir, $o(n)$), el algoritmo descripto a continuación es interesante por su simplicidad: no es más complejo que la versión clasica de la criba de Eratóstenes.

Además, el algoritmo dado aquí calcula la **factorizaciones de todos los números** en el segmento $[2; n]$ como efecto secundario, y que puede ser útil en muchas aplicaciones prácticas.

La debilidad del algoritmo dado está en usar más memoria que la criba de Eratóstenes clásica: requiere un arreglo de $n$ números, mientras que para la criba clásica de Eratóstenes es suficiente tener $n$ bits de memoria (que es 32 veces menos).

Por lo tanto, tiene sentido usar el algoritmo descrito solo hasta para números de orden $10^7$ y no mayores.

La autoría del algoritmo parece pertenecer a Gries & Misra (Gries, Misra, 1978: véanse las referencias al final del artículo). Y, estrictamente hablando, este algoritmo no debería llamarse "criba de Eratóstenes", ya que es demasiado diferente de la versión clásica.

## Algoritmo

Nuestro objetivo es calcular el **mínimo factor primo** $lp [i]$ para cada número $i$ en el segmento $[2; n]$.

Además, necesitamos almacenar la lista de todos los números primos encontrados - llamemoslo $pr []$.

Inicializamos los valores $lp [i]$ con ceros, lo que significa que asumimos que todos los números son primos. Durante la ejecución del algoritmo, este arreglo se llenará gradualmente.

Ahora recorreremos los números del 2 al $n$. Tenemos dos casos para el número actual $i$:

- $lp[i] = 0$ - significa que $i$ es primo, es decir, no encontramos ningún factor menor a él.
  Por lo tanto, asignamos $lp [i] = i$ y agregamos $i$ al final de la lista $pr[]$.

- $lp[i] \neq 0$ - significa que $i$ es compuesto, y su menor factor primo es $lp [i]$.

En ambos casos, actualizamos los valores de $lp []$ para los números que son divisibles por $i$. Sin embargo, nuestro objetivo es aprender a hacerlo para asignar un valor $lp []$ a lo sumo una vez por cada número. Podemos hacerlo de la siguiente manera:

Consideremos los números $x_j = i \cdot p_j$, donde $p_j$ son todos los números primos menores o iguales que $lp [i]$ (es por eso que necesitamos almacenar la lista de todos los números primos).

Asignamos un nuevo valor $lp [x_j] = p_j$ para todos los números de esta forma.

La prueba de la corrección de este algoritmo y su tiempo de ejecución se puede encontrar después de la implementación.


## Implementación

```cpp
const int N = 10000000;
int lp[N+1];
vector<int> pr;
 
for (int i=2; i<=N; ++i) {
	if (lp[i] == 0) {
		lp[i] = i;
		pr.push_back(i);
	}
	for (int j=0; j < (int)pr.size() && pr[j] <= lp[i] && i * pr[j] <= N; ++j)
		lp[i * pr[j]] = pr[j];
}
```

Podemos acelerarlo un poco reemplazando el vector $pr$ con un arreglo simple y un contador, y deshaciéndonos de la segunda multiplicación en el ciclo `for` anidado (para eso solo necesitamos recordar el producto en una variable).


## Demostración de correctitud

Necesitamos demostrar que el algoritmo asigna todos los valores $lp []$ correctamente, y que cada valor se establecerá exactamente una vez. Por lo tanto, el algoritmo tendrá un tiempo de ejecución lineal, ya que todas las acciones restantes del algoritmo, obviamente, funcionan para $O(n)$.

Observe que cada número $i$ tiene exactamente una representación en forma:

$i = lp [i] \cdot x$ ,

donde $lp [i]$ es el menor factor primo de $i$, y el número $x$ no tiene ningún factor primo menor a $lp [i]$. Es decir

$lp [i] \le lp [x]$.

Ahora, comparemos esto con las acciones de nuestro algoritmo: de hecho, por cada $x$ recorre todos los números primos por los que puede ser multiplicado, es decir todos los primos hasta $lp [x]$ inclusive, con el fin de obtener los números en la forma dada arriba.

Por lo tanto, el algoritmo pasará por cada número compuesto exactamente una vez, asignando los valores correctos $lp []$ ahí. Q.E.D.

## Complejidad Temporal y Espacial

Aunque la complejidad temporal de $O(n)$ es mejor que $O(n \log \log n)$ de la versión clásica de la criba de Eratóstenes, la diferencia no es tan grande. En la práctica esto significa solo el doble de diferencia en velocidad, y la versión optimizada de la criba corre tan rápido como el algoritmo dado aquí.

Considerando los requisitos de memoria de este algoritmo - un arreglo $lp []$ de largo $n$, y un arreglo $pr []$ de largo  $\frac n {\ln n}$, este algoritmo parece peor que la criba clásica en todos los sentidos.

Sin embargo, su cualidad redentora es que este algoritmo calcula un arreglo $lp []$, que nos permite encontrar la factorización de cualquier número en el segmento $[2; n]$ en un tiempo del orden del tamaño de la factorización. Además, usar solo un arreglo adicional nos permitirá evitar divisiones cuando busquemos factorización.

Conocer las factorizaciones de todos los números es muy útil para algunas tareas, y este algoritmo es uno de los pocos que permiten encontrarlos en tiempo lineal.

## Referencias

- David Gries, Jayadev Misra. **A Linear Sieve Algorithm for Finding Prime Numbers** [1978]
