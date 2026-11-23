# Universidad Tecnológica Nacional - Facultad Regional Buenos Aires
## Sintaxis y Semántica de los Lenguajes
### Modelo de 1er Parcial N° 2

**Apellido y Nombre:** ........................................................................  
**Legajo:** ............................  

---

### Enunciados

**1)** Dé las producciones de una Gramática Regular (GR) que genere el lenguaje representado por la siguiente expresión regular:
$$a (b | c)^* (a | bc)$$

**2)** Arme la Tabla de Transición de un Autómata Finito (AF) que represente el lenguaje del punto 1 y dibuje el diagrama de estados correspondiente.

**3)** Dé las producciones de una Gramática Independiente del Contexto (GIC) que genere el siguiente lenguaje:
$$L = \{ a^{3n} b^{n+1} c^{2t+1} d^{t+2} \ / \ n \ge 1 \ \land \ t \ge 0 \}$$

**4)** Un sistema de tickets de soporte técnico valida los códigos de seguimiento según las siguientes reglas:
* Comienzan obligatoriamente con el prefijo `"TKT-"` (en mayúsculas).
* Siguen **entre 3 y 5 caracteres alfanuméricos** (letras minúsculas o dígitos).
* Luego aparece **una o más** bloques compuestos por: un numeral (`#`) seguido de **exactamente 2 letras mayúsculas**.

Diseñe una expresión regular, lo más breve posible, que represente estos códigos de seguimiento.

**5) [Matrices, Punteros Genéricos y Memoria Dinámica en C]** Analice el siguiente programa en C y responda:

```c
#include <stdio.h>
#include <stdlib.h>

void analizar_matriz(double m[][3], int filas) {
    printf("1: %zu\n", sizeof(m));
    printf("2: %zu\n", sizeof(m[0]));
}

int main() {
    double mat[2][3] = {
        {1.5, 2.5, 3.5},
        {4.5, 5.5, 6.5}
    };
    
    analizar_matriz(mat, 2);
    
    void *pv = &mat[1][1];
    double *pd = (double *)pv;
    printf("3: %.1f\n", *(pd - 2));
    
    int *din = malloc(4 * sizeof(*din));
    if (din != NULL) {
        *(din + 3) = 99;
        printf("4: %d\n", din[3]);
        free(din);
    }
    return 0;
}
```

1. Indique cuál será la salida exacta de las líneas `1`, `2`, `3` y `4` (asuma `sizeof(double) = 8`, `sizeof(int) = 4` y arquitectura de 64 bits).
2. Explique por qué en la declaración del parámetro `double m[][3]` la segunda dimensión (`3`) es obligatoria mientras que la primera puede omitirse.
3. ¿Qué ocurriría si en la línea del `printf("3: ...")` intentáramos hacer `*(pv - 2)` directamente sin realizar el *casting* a `double *`?

---

### Resoluciones

<details>
<summary><b>Haz clic aquí para ver las respuestas del Modelo 2</b></summary>

#### Punto 1: Gramática Regular (GR)
Definimos $G = (V_N, V_T, P, S)$ con $V_N = \{S, A, B\}$ y $V_T = \{a, b, c\}$.

**Producciones ($P$):**
* $S \to aA$
* $A \to bA \mid cA \mid a \mid bB$
* $B \to c$

---

#### Punto 2: Autómata Finito (AFD)
Como el lenguaje es no determinista en la lectura de $b$ desde el estado tras leer la primera $a$ (puede ser parte de $(b|c)^*$ o el inicio de $bc$), determinizamos el autómata:
* $Q = \{q_0, q_1, q_2, q_3, q_4, q_{error}\}$
* $\Sigma = \{a, b, c\}$
* $q_0$: estado inicial
* $F = \{q_2, q_4\}$: estados de aceptación

**Tabla de Transición ($\delta$):**

| Estado | $a$ | $b$ | $c$ |
| :--- | :---: | :---: | :---: |
| $\to q_0$ | $q_1$ | $q_{error}$ | $q_{error}$ |
| $q_1$ | $q_2$ | $q_3$ | $q_1$ |
| $*q_2$ | $q_{error}$ | $q_{error}$ | $q_{error}$ |
| $q_3$ | $q_2$ | $q_3$ | $q_4$ |
| $*q_4$ | $q_2$ | $q_3$ | $q_1$ |
| $q_{error}$ | $q_{error}$ | $q_{error}$ | $q_{error}$ |

**Descripción del Diagrama de Estados:**
* $q_0$ (inicial) pasa a $q_1$ con $a$.
* $q_1$ tiene bucle con $c$, va a $q_2$ (final) con $a$, y va a $q_3$ con $b$.
* $q_3$ tiene bucle con $b$, va a $q_2$ (final) con $a$, y va a $q_4$ (final, tras leer $bc$) con $c$.
* $q_4$ (final) actúa igual que $q_1$ si siguen llegando símbolos: va a $q_2$ con $a$, a $q_3$ con $b$, y vuelve a $q_1$ con $c$.

---

#### Punto 3: Gramática Independiente del Contexto (GIC)
Dividimos en $L = L_1 \cdot L_2$:
* $L_1 = \{ a^{3n} b^{n+1} \ / \ n \ge 1 \}$ (caso base $n=1 \implies a^3 b^2$).
* $L_2 = \{ c^{2t+1} d^{t+2} \ / \ t \ge 0 \}$ (caso base $t=0 \implies c^1 d^2$).

**Producciones ($P$):**
* $S \to A B$
* $A \to a a a A b \mid a a a b b$
* $B \to c c B d \mid c d d$

---

#### Punto 4: Expresión Regular
$$TKT-[a-z0-9]\{3,5\}(#[A-Z]\{2\})+$$

*(En notación básica sin cuantificadores extendidos)*:
$$TKT-[a-z0-9][a-z0-9][a-z0-9]([a-z0-9]|\lambda)([a-z0-9]|\lambda)#[A-Z][A-Z](#[A-Z][A-Z])*$$

---

#### Punto 5: Respuestas sobre C
1. **Salidas por pantalla:**
   * **`1: 8`** — El parámetro `m` decae a un puntero a un vector de 3 `double` (`double (*)[3]`). Como todo puntero en 64 bits, ocupa $8\text{ bytes}$.
   * **`2: 24`** — `m[0]` representa una fila completa de la matriz, es decir, un arreglo de 3 elementos `double` ($3 \times 8\text{ bytes} = 24\text{ bytes}$).
   * **`3: 2.5`** — En memoria, la matriz se almacena contigua por filas: `{1.5, 2.5, 3.5, 4.5, 5.5, 6.5}`. `pd` apunta a `mat[1][1]` (`5.5`). Al restarle `2` (`pd - 2`), retrocede 2 posiciones en memoria llegando a `mat[0][1]`, cuyo valor es `2.5`.
   * **`4: 99`** — `*(din + 3)` es sintácticamente idéntico a `din[3]`. Se asigna `99` en el cuarto entero reservado dinámicamente y luego se imprime ese mismo valor.

2. **Obligatoriedad de la segunda dimensión:**  
   En C no existen matrices nativas sino "arreglos de arreglos". Al pasar `mat` a una función, solo decae la primera dimensión a un puntero (`double (*)[3]`). El compilador necesita conocer el tamaño de cada elemento al que apunta (`sizeof(double[3]) = 24 bytes`) para poder calcular los saltos en aritmética de punteros al indexar filas (`m[i]`).

3. **Aritmética sobre `void *`:**  
   El estándar de C prohíbe la aritmética de punteros y la desreferenciación sobre punteros genéricos (`void *`) porque `void` es un **tipo incompleto** (no tiene un tamaño definido en bytes para calcular el desplazamiento `n * sizeof(tipo)`). El programa produciría un error de compilación.

</details>