# Universidad Tecnológica Nacional - Facultad Regional Buenos Aires
## Sintaxis y Semántica de los Lenguajes
### Modelo de 1er Parcial N° 1

**Apellido y Nombre:** ........................................................................  
**Legajo:** ............................  

---

### Enunciados

**1)** Dé las producciones de una Gramática Regular (GR) que genere el lenguaje representado por la siguiente expresión regular:
$$(a|b)^* c (ab | c)$$

**2)** Arme la Tabla de Transición de un Autómata Finito (AF) que represente el lenguaje del punto 1 y dibuje el diagrama de estados correspondiente (puede representarlo mediante diagramas de texto o describir las transiciones).

**3)** Dé las producciones de una Gramática Independiente del Contexto (GIC) que genere el siguiente lenguaje:
$$L = \{ a^{n+2} b^{2n+1} c^{3t} d^{t} \ / \ n \ge 0 \ \land \ t \ge 1 \}$$

**4)** Los identificadores de lote de un sistema de logística tienen el siguiente formato:
* Comienzan con **2 o 3 letras mayúsculas**.
* Luego, obligatoriamente, un **guion bajo (`_`)** o un **guion medio (`-`)**.
* A continuación, una secuencia de **exactamente 4 dígitos**, donde el primero no puede ser cero (`0`).
* Por último, de manera opcional, puede finalizar con la palabra `"EXP"` o `"NAC"` (en mayúsculas) precedida por un punto (`.`).

Diseñe una expresión regular, lo más breve posible, que represente estos identificadores de lote.

**5) [Punteros y Memoria en C]** Dado el siguiente fragmento de código en lenguaje C (asuma arquitectura de 64 bits donde `sizeof(int) = 4`, `sizeof(double) = 8` y `sizeof(void*) = 8`):

```c
#include <stdio.h>
#include <stdlib.h>

void procesar(int arr[], int *p) {
    printf("A: %zu\n", sizeof(arr));
    *p = *(arr + 2) + 1;
    p++;
    *p = 2[arr] * 2;
}

int main() {
    int v[5] = {10, 20, 30, 40};
    int *ptr = v + 1;
    
    printf("B: %zu\n", sizeof(v));
    procesar(v, ptr);
    
    printf("C: %d - %d - %d\n", v[1], v[2], v[4]);
    printf("D: %td\n", ptr - v);
    return 0;
}
```

Indique cuál será la salida exacta por pantalla (`A`, `B`, `C` y `D`) y justifique brevemente cada resultado basándose en la aritmética de punteros y decaimiento de arreglos (*array decay*).

---

### Resoluciones

<details>
<summary><b>Haz clic aquí para ver las respuestas del Modelo 1</b></summary>

#### Punto 1: Gramática Regular (GR)
Definimos $G = (V_N, V_T, P, S)$ con $V_N = \{S, A, B\}$ y $V_T = \{a, b, c\}$.

**Producciones ($P$):**
* $S \to aS \mid bS \mid cA$
* $A \to aB \mid c$
* $B \to b$

*(Nota: Si se utiliza una GR estrictamente lineal por derecha sin producciones terminales directas desde no terminales intermedios, se puede agregar un estado final $F \to \lambda$: $A \to aB \mid cF$, $B \to bF$, $F \to \lambda$).*

---

#### Punto 2: Autómata Finito (AFD)
Definimos el AFD $M = (Q, \Sigma, \delta, q_0, F)$ donde:
* $Q = \{q_0, q_1, q_2, q_3, q_{error}\}$
* $\Sigma = \{a, b, c\}$
* $q_0$: estado inicial
* $F = \{q_3\}$: estados de aceptación

**Tabla de Transición ($\delta$):**

| Estado | $a$ | $b$ | $c$ |
| :--- | :---: | :---: | :---: |
| $\to q_0$ | $q_0$ | $q_0$ | $q_1$ |
| $q_1$ | $q_2$ | $q_{error}$ | $q_3$ |
| $q_2$ | $q_{error}$ | $q_3$ | $q_{error}$ |
| $*q_3$ | $q_{error}$ | $q_{error}$ | $q_{error}$ |
| $q_{error}$ | $q_{error}$ | $q_{error}$ | $q_{error}$ |

**Descripción del Diagrama de Estados:**
* $q_0$ (inicial) tiene un bucle con $a, b$ hacia sí mismo, y transiciona con $c$ hacia $q_1$.
* Desde $q_1$, con $c$ va hacia $q_3$ (estado final) y con $a$ va hacia $q_2$.
* Desde $q_2$, con $b$ va hacia $q_3$ (estado final).
* Cualquier otra transición no mencionada dirige al estado sumidero $q_{error}$.

---

#### Punto 3: Gramática Independiente del Contexto (GIC)
Separamos el lenguaje en dos subproblemas concatenados: $L = L_1 \cdot L_2$, donde:
* $L_1 = \{ a^{n+2} b^{2n+1} \ / \ n \ge 0 \}$ (para $n=0 \implies a^2b^1$; por cada incremento de $n$, se agrega una $a$ y dos $b$).
* $L_2 = \{ c^{3t} d^{t} \ / \ t \ge 1 \}$ (para $t=1 \implies c^3d^1$; por cada incremento de $t$, se agregan tres $c$ y una $d$).

**Producciones ($P$):**
* $S \to A B$
* $A \to a A b b \mid a a b$
* $B \to c c c B d \mid c c c d$

---

#### Punto 4: Expresión Regular
$$[A-Z]\{2,3\}[_-][1-9][0-9]\{3\}(\backslash.(EXP|NAC))?$$

*(En notación básica sin cuantificadores extendidos)*:
$$[A-Z][A-Z]([A-Z]|\lambda)(\_|-)[1-9][0-9][0-9][0-9](\.(EXP|NAC)|\lambda)$$

---

#### Punto 5: Salida del programa en C
* **`A: 8`**  
  *Justificación:* Al pasar un arreglo como parámetro (`int arr[]`), este decae a un puntero (`int *`). Por lo tanto, `sizeof(arr)` devuelve el tamaño de un puntero en 64 bits ($8\text{ bytes}$).
* **`B: 20`**  
  *Justificación:* En el ámbito donde se define el arreglo (`main`), `sizeof(v)` devuelve el tamaño total reservado en bytes: $5 \text{ elementos} \times 4 \text{ bytes} = 20\text{ bytes}$.
* **`C: 31 - 60 - 0`**  
  *Justificación:* 
  1. `v` fue inicializado parcialmente, por lo que `v[4]` vale implícitamente `0`.
  2. `ptr` apunta inicialmente a `&v[1]`. Dentro de `procesar`, `*p = *(arr + 2) + 1` asigna a `v[1]` el valor de `v[2] + 1` ($30 + 1 = 31$).
  3. Luego `p++` hace que el puntero local `p` apunte a `&v[2]`. La expresión `2[arr]` es equivalente por conmutatividad a `*(2 + arr)` o `arr[2]`, que ahora vale `30`. Al multiplicar por `2`, `v[2]` pasa a valer `60`.
* **`D: 1`**  
  *Justificación:* La resta de dos punteros del mismo tipo devuelve la distancia medida en **elementos** (`ptrdiff_t`), no en bytes. Como `ptr` en `main` no fue modificado (se pasó por valor) y apunta a `v + 1`, `(v + 1) - v = 1`.

</details>