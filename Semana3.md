[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana2.md) | [Semana Siguiente ▶️](./Semana4.md)

---

# 3️⃣ Semana 3: Mapas de Karnaugh y Universalidad

## 🗺️ ¿Qué es un Mapa de Karnaugh?

Es una cuadrícula mágica donde acomodamos los resultados de la tabla de verdad. Los 1s que quedan pegados nos dicen qué variables se pueden eliminar. Así simplificamos mirando, sin tanto álgebra.

### 🔑 El truco: el orden Gray

Las filas y columnas **no** van en orden normal (00, 01, 10, 11). Van así:

```
00 → 01 → 11 → 10
```

Así, de una casilla a la vecina solo cambia 1 bit. Por eso las casillas pegadas se pueden juntar.

### 📜 Reglas para agrupar los 1s

1. Los grupos solo pueden ser de **1, 2, 4, 8, 16...** casillas (potencias de 2).
2. Solo se agrupan casillas **pegadas** (arriba, abajo, izquierda, derecha). No en diagonal.
3. Los grupos deben ser lo más grandes posible.
4. Se puede usar el mismo 1 en varios grupos.
5. ¡El mapa es como un **rollo**! Los bordes se tocan: la fila de arriba con la de abajo, y la columna izquierda con la derecha.
6. Todos los 1s deben quedar dentro de algún grupo.

### ✅ Cómo sacar la fórmula de cada grupo

Para cada grupo, miro qué variables NO cambian dentro del grupo. Esas se quedan. Las que cambian, se van. Si una variable vale 0 en todo el grupo, va con `'`.

---

## 👀 Ejemplo 1: Mapa de 3 variables

Retomamos la alarma de la Semana 2: `F = Σm(3, 5, 6, 7)`

```
        BC
      00  01  11  10
A=0 │  0   0   1   0  │
A=1 │  0   1   1   1  │
```

Grupos de 2 unos cada uno:

| Grupo | Casillas | ¿Qué no cambia? | Fórmula |
|-------|----------|-----------------|---------|
| 🟥 Grupo 1 | m3 y m7 (columna 11) | B=1, C=1 | `BC` |
| 🟦 Grupo 2 | m5 y m7 (fila A=1, columnas 01 y 11) | A=1, C=1 | `AC` |
| 🟩 Grupo 3 | m6 y m7 (fila A=1, columnas 11 y 10) | A=1, B=1 | `AB` |

**Resultado:** `F = AB + AC + BC` ✅ (¡el mismo de la Semana 2, pero sin hacer álgebra!)

---

## 👀 Ejemplo 2: Mapa de 4 variables

Función: `F = Σm(0, 1, 2, 5, 8, 9, 10)`

```
           CD
         00  01  11  10
AB=00 │   1   1   0   1  │
AB=01 │   0   1   0   0  │
AB=11 │   0   0   0   0  │
AB=10 │   1   1   0   1  │
```

Grupos:

| Grupo | Casillas | ¿Qué no cambia? | Fórmula |
|-------|----------|-----------------|---------|
| 🟥 Las 4 esquinas | m0, m2, m8, m10 | B=0, D=0 | `B'D'` |
| 🟦 Bloque de 4 (arriba y abajo a la izquierda) | m0, m1, m8, m9 | B=0, C=0 | `B'C'` |
| 🟩 Pareja | m1 y m5 | A=0, C=0, D=1 | `A'C'D` |

**Resultado:** `F = B'D' + B'C' + A'C'D`

> 🌀 **Ojo:** las 4 esquinas forman un grupo porque el mapa es como un rollo: arriba toca con abajo y izquierda toca con derecha.

---

## ❓ Condiciones "Don't Care" (Indiferentes)

A veces hay combinaciones de entradas que **nunca van a pasar** o que **no nos importa** qué salida den. Esas se marcan con una **X**.

**Ejemplo:** un circuito que dice si un número del 0 al 7 es impar, pero en nuestro sistema el 7 **nunca** se usa.

```
        BC
      00  01  11  10
A=0 │  0   1   1   0  │
A=1 │  0   1   X   0  │
```

**¿Para qué sirve la X?** Podemos usarla **como 1 o como 0**, lo que nos convenga más para hacer grupos más grandes.

| Caso | Fórmula | Complejidad |
|------|---------|-------------|
| Sin usar la X (tratada como 0) | `F = A'C + B'C` | 2 AND + 1 OR + NOTs |
| Usando la X como 1 | `F = C` | ¡Solo un cable! 😲 |

Con la X como 1, el grupo cubre toda la columna donde C=1, y la fórmula se reduce a `F = C`.

> 💡 Las X son un regalo: nos ayudan a simplificar más. Úsalas solo si te sirven; no es obligatorio agrupar todas.

---

## 🧰 Universalidad de las compuertas NAND y NOR

**Idea:** con **solo NAND** (o **solo NOR**) se puede armar cualquier circuito. Esto es útil porque:

- Comprar un solo tipo de chip sale más barato.
- Un chip **74LS00** trae 4 NAND. Con un solo chip puedes hacer varias cosas.
- Los circuitos se arman más ordenados.

### Cómo hacer las compuertas básicas solo con NAND

**NOT con NAND:** une las dos entradas.

```
A ──┬──[NAND]── A'
    └──┘
```

**AND con NAND:** NAND seguida de un NOT (otra NAND con entradas unidas).

```
A ──┐
    [NAND]──[NAND como NOT]── A·B
B ──┘
```

**OR con NAND:** niega cada entrada y luego usa una NAND (De Morgan: `A + B = (A'·B')'`).

```
A ──[NOT]──┐
           [NAND]── A + B
B ──[NOT]──┘
```

### Cómo hacerlas solo con NOR (es el espejo)

| Compuerta | Con solo NOR |
|-----------|--------------|
| NOT | NOR con las entradas unidas |
| OR | NOR seguida de un NOT |
| AND | niega cada entrada con NOR y luego una NOR (`A·B = (A'+B')'`) |

### 🛠️ Ejemplo con ahorro: `F = AB + CD`

Con compuertas normales necesito 2 AND + 1 OR = 2 chips distintos (7408 y 7432).

Con De Morgan se puede reescribir:

```
F = AB + CD
  = ((AB + CD)')'          ← doble negación
  = ((AB)' · (CD)')'       ← De Morgan
```

Eso es: **NAND(AB)**, **NAND(CD)** y una tercera **NAND** que junta las dos.

```
A ──┐
    [NAND]──┐
B ──┘       │
            [NAND]──── F
C ──┐       │
    [NAND]──┘
D ──┘
```

**Total: 3 NAND = ¡un solo chip 74LS00!** (trae 4, nos sobra 1) 🎉

> 🧠 **Receta rápida:** toma tu fórmula en SOP (suma de productos) → cambia todas las ANDs y la OR final por NANDs. ¡Listo!

---

[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana2.md) | [Semana Siguiente ▶️](./Semana4.md)
