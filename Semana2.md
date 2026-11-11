[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana1.md) | [Semana Siguiente ▶️](./Semana3.md)

---

# 2️⃣ Semana 2: Álgebra de Boole y Formas Canónicas

## 🧮 ¿Qué es el Álgebra de Boole?

Es como el álgebra normal, pero solo con 0 y 1. Sirve para escribir un circuito como una fórmula y luego hacerla más corta. Una fórmula más corta = menos compuertas = circuito más barato y rápido.

Notación que usamos:

| Símbolo | Significa |
|:-------:|-----------|
| `A · B` o `AB` | A AND B |
| `A + B` | A OR B |
| `A'` | NOT A |

---

## Reglas básicas (las que más se usan)

| Regla | Fórmula | ¿Qué significa en palabras? |
|-------|---------|-----------------------------|
| Identidad | `A + 0 = A` y `A · 1 = A` | Sumar 0 o multiplicar por 1 no cambia nada |
| Nulo | `A + 1 = 1` y `A · 0 = 0` | Un 1 en un OR lo vuelve todo 1; un 0 en un AND lo vuelve todo 0 |
| Idempotencia | `A + A = A` y `A · A = A` | Repetir lo mismo no agrega nada |
| Complemento | `A + A' = 1` y `A · A' = 0` | Algo y su contrario: en OR siempre da 1, en AND siempre da 0 |
| Doble negación | `(A')' = A` | Negar dos veces te devuelve al inicio |
| Conmutativa | `A + B = B + A` | El orden no importa |
| Distributiva | `A(B + C) = AB + AC` | Igual que en el álgebra de colegio |
| Absorción | `A + AB = A` | Si ya tengo A, lo otro sobra |

### Ejemplo rápido de absorción

```
F = A + AB
  = A(1 + B)     ← saqué A como factor común
  = A(1)         ← porque 1 + B = 1
  = A            ✅
```

Así un circuito con una AND y una OR se convierte en **un simple cable**.

---

## 🔀 Leyes de De Morgan

Son dos reglas que te dejan cambiar ANDs por ORs (y al revés) moviendo los NOT:

```
(A · B)' = A' + B'      → "negar un AND" = "OR de los negados"
(A + B)' = A' · B'      → "negar un OR"  = "AND de los negados"
```


Comprobación con A=1, B=0:

```
(A · B)' = (1 · 0)' = (0)' = 1
A' + B'  = 0 + 1    = 1        ✅ Son iguales
```

---

## 🧱 Minitérminos y Maxitérminos

Cada fila de una tabla de verdad se puede describir con una fórmula especial. Hay dos tipos.

### Minitérmino (m): una AND que da 1 en UNA sola fila
Se escribe con todas las variables. Si la variable vale 1 va normal, si vale 0 va con `'`.

### Maxitérmino (M): una OR que da 0 en UNA sola fila
Se escribe con todas las variables. Si la variable vale 0 va normal, si vale 1 va con `'`.

Con 3 variables (A, B, C):

| # | A B C | Minitérmino | Maxitérmino |
|:-:|:-----:|:-----------:|:-----------:|
| 0 | 0 0 0 | `A'B'C'` (m0) | `A+B+C` (M0) |
| 1 | 0 0 1 | `A'B'C` (m1) | `A+B+C'` (M1) |
| 2 | 0 1 0 | `A'BC'` (m2) | `A+B'+C` (M2) |
| 3 | 0 1 1 | `A'BC` (m3) | `A+B'+C'` (M3) |
| 4 | 1 0 0 | `AB'C'` (m4) | `A'+B+C` (M4) |
| 5 | 1 0 1 | `AB'C` (m5) | `A'+B+C'` (M5) |
| 6 | 1 1 0 | `ABC'` (m6) | `A'+B'+C` (M6) |
| 7 | 1 1 1 | `ABC` (m7) | `A'+B'+C'` (M7) |

### ➕ Suma de Productos (SOP)
Miras las filas donde la salida es 1, escribes su minitérmino y los sumas (OR).

Se escribe así: `F = Σm(3, 5, 6, 7)`

### ✖️ Producto de Sumas (POS)
Miras las filas donde la salida es 0, escribes su maxitérmino y los multiplicas (AND).

Se escribe así: `F = ΠM(0, 1, 2, 4)`

> 🧠 **Regla fácil:** SOP usa los unos de la tabla. POS usa los ceros. Las dos dan el mismo circuito, solo cambia la forma de escribirlo.

---

## 🏠 Ejemplo práctico: Alarma para una casa con 3 sensores

### 📝 El problema en palabras

Una casa tiene 3 sensores:

- **A** = sensor de la **puerta** (1 = abierta)
- **B** = sensor de la **ventana** (1 = abierta)
- **C** = sensor de **movimiento** (1 = detecta algo)

La alarma F debe sonar cuando al menos 2 sensores estén activados (así evitamos falsas alarmas, por ejemplo cuando el gato mueve algo).

### 📋 Paso 1: Tabla de verdad

| A | B | C | F (alarma) | Minitérmino |
|:-:|:-:|:-:|:----------:|:-----------:|
| 0 | 0 | 0 | 0 | |
| 0 | 0 | 1 | 0 | |
| 0 | 1 | 0 | 0 | |
| 0 | 1 | 1 | **1** | m3 |
| 1 | 0 | 0 | 0 | |
| 1 | 0 | 1 | **1** | m5 |
| 1 | 1 | 0 | **1** | m6 |
| 1 | 1 | 1 | **1** | m7 |

### ✍️ Paso 2: Escribir la fórmula (SOP)

```
F = Σm(3, 5, 6, 7)
F = A'BC + AB'C + ABC' + ABC
```

Si la hacemos tal cual, necesitaríamos muchas compuertas. ¡Vamos a simplificar!

### ✂️ Paso 3: Simplificar con las reglas

Truco: como `ABC + ABC = ABC` (idempotencia), puedo repetir ABC las veces que necesite:

```
F = A'BC + AB'C + ABC' + ABC + ABC + ABC

Agrupo de a dos:
F = (A'BC + ABC) + (AB'C + ABC) + (ABC' + ABC)

Saco factor común en cada grupo:
F = BC(A' + A) + AC(B' + B) + AB(C' + C)

Como (A' + A) = 1, (B' + B) = 1 y (C' + C) = 1:
F = BC + AC + AB
```

**Resultado:** `F = AB + AC + BC` ✅

Significa: "suena la alarma si (puerta Y ventana) O (puerta Y movimiento) O (ventana Y movimiento)".

### 🔌 Paso 4: Circuito con compuertas

```
A ──┬──────────┐
    │          ▼
    │        [AND]──── AB ───┐
B ──┼──┬───────┘             │
    │  │                     ▼
    │  │        ┌──[AND]── AC ──[OR]──┐
    └──┼────────┘                     │ ▼
       │                              [OR]──── F (ALARMA 🚨)
C ─────┼────────┬──[AND]── BC ────────┘
       └────────┘
```

Forma más simple de leerlo:

| Compuerta | Entradas | Salida |
|-----------|----------|--------|
| AND #1 | A, B | AB |
| AND #2 | A, C | AC |
| AND #3 | B, C | BC |
| OR #1 | AB, AC | (AB + AC) |
| OR #2 | (AB + AC), BC | **F** |

**Chips necesarios:** 1 chip **74LS08** (trae 4 AND, usamos 3) + 1 chip **74LS32** (trae 4 OR, usamos 2). ¡Solo 2 chips! 🎉

---

[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana1.md) | [Semana Siguiente ▶️](./Semana3.md)
