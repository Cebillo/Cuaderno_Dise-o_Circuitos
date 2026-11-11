[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana3.md) | [Semana Siguiente ▶️](./README.md)

---

# 4️⃣ Semana 4: Bloques Aritméticos

Ahora usamos todo lo aprendido para armar circuitos que **suman, restan y comparan** 🧮.

---

## ➕ Semisumador (Half Adder): suma 2 bits

Suma **dos bits** (A y B) y entrega dos cosas:

- **S** = la suma
- **C** = el acarreo ("lo que llevamos")

| A | B | C (acarreo) | S (suma) |
|:-:|:-:|:-----------:|:--------:|
| 0 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 |
| 1 | 0 | 0 | 1 |
| 1 | 1 | **1** | **0** |

Mirando la tabla:

```
S = A ⊕ B      ← una XOR
C = A · B      ← una AND
```

Solo necesita **1 XOR + 1 AND**. 😎

> ⚠️ **Problema:** no sabe recibir un acarreo de una suma anterior. Por eso solo sirve para el primer bit.

---

## ➕➕ Sumador Completo (Full Adder): suma 3 bits

Suma **tres bits**: A, B y el acarreo que viene de la columna anterior (**Cin**).

| A | B | Cin | Cout | S |
|:-:|:-:|:---:|:----:|:-:|
| 0 | 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 | 1 |
| 0 | 1 | 0 | 0 | 1 |
| 0 | 1 | 1 | 1 | 0 |
| 1 | 0 | 0 | 0 | 1 |
| 1 | 0 | 1 | 1 | 0 |
| 1 | 1 | 0 | 1 | 0 |
| 1 | 1 | 1 | 1 | 1 |

Fórmulas:

```
S    = A ⊕ B ⊕ Cin
Cout = A·B + Cin·(A ⊕ B)
```

### 📌 Diferencia entre los dos

| | Semisumador | Sumador completo |
|---|:-----------:|:----------------:|
| Entradas | 2 (A, B) | 3 (A, B, Cin) |
| Salidas | S y C | S y Cout |
| ¿Recibe acarreo anterior? | ❌ No | ✅ Sí |
| Se usa en | el primer bit | los demás bits |

> 💡 Un sumador completo se arma con **2 semisumadores + 1 OR**.

---

## 🔗 Sumador Paralelo de 4 bits

Para sumar números de 4 bits, **conectamos 4 sumadores completos en fila**. El acarreo de salida (Cout) de uno entra al siguiente (Cin). Es como sumar en el cuaderno: de derecha a izquierda, pasando "lo que llevamos".

```
        A3 B3      A2 B2      A1 B1      A0 B0
         │  │       │  │       │  │       │  │
        ┌▼──▼┐     ┌▼──▼┐     ┌▼──▼┐     ┌▼──▼┐
 C4 ◄───│ FA │◄────│ FA │◄────│ FA │◄────│ FA │◄── C0 (= 0)
        └─┬──┘     └─┬──┘     └─┬──┘     └─┬──┘
          │          │          │          │
          S3         S2         S1         S0
```

### Ejemplo: `0110 + 0111` (6 + 7)

```
   0 1 1 0     ← llevamos 1 en las columnas del medio
 + 0 1 1 1
 ---------
   1 1 0 1     ← es 13 ✅
```

> 🔌 **Chip real:** el **74LS283** es un sumador completo de 4 bits ya armado en un solo chip.

⚠️ Como cada sumador espera el acarreo del anterior, a este tipo se le llama "de acarreo en cascada" y es un poco lento. Existen chips más rápidos, pero la idea base es la misma.

---

## ➖ Resta con complemento a dos

¡No necesitamos un circuito nuevo para restar! Usamos el **mismo sumador**:

```
A − B  =  A + (complemento a dos de B)
       =  A + (B invertido + 1)
```

### Ejemplo paso a paso: `7 − 3` con 4 bits

1. **A = 7** → `0111`
2. **B = 3** → `0011`
3. Invierto B: `1100`
4. Le sumo 1: `1101` (esto es el −3)
5. Sumo A + (−B):

```
   0 1 1 1     (7)
 + 1 1 0 1     (−3)
 ---------
 1 0 1 0 0
 ↑
 Este acarreo extra se descarta ❌
```

6. Resultado: `0100` = **4** ✅

### 🔧 Sumador/Restador en un solo circuito

Ponemos una compuerta XOR en cada entrada B, controlada por una señal **M**:

| M | ¿Qué hace? | Explicación |
|:-:|------------|-------------|
| 0 | **Suma** (A + B) | La XOR deja pasar B igual, y Cin = 0 |
| 1 | **Resta** (A − B) | La XOR invierte B, y Cin = 1 (el "+1") |

```
B ──[XOR]──► entrada B del sumador
M ──┘
M ─────────► Cin del primer sumador
```

¡Un solo interruptor cambia entre sumar y restar! 🎛️

---

## ⚖️ Comparador de Magnitud

Es un circuito que compara dos números, **A** y **B**, y dice cuál es mayor. Tiene 3 salidas:

- **A > B** (A es mayor)
- **A < B** (A es menor)
- **A = B** (son iguales)

### Comparador de 1 bit

| A | B | A > B | A = B | A < B |
|:-:|:-:|:-----:|:-----:|:-----:|
| 0 | 0 | 0 | **1** | 0 |
| 0 | 1 | 0 | 0 | **1** |
| 1 | 0 | **1** | 0 | 0 |
| 1 | 1 | 0 | **1** | 0 |

Fórmulas:

```
A > B  =  A · B'
A < B  =  A' · B
A = B  =  XNOR(A, B)
```

### Comparador de 4 bits

La idea es igual a como comparamos números en decimal: **empiezo por el dígito de más a la izquierda** (el más importante).

1. Comparo **A3 con B3**. Si son diferentes, ya sé cuál es mayor. Fin.
2. Si son iguales, paso a **A2 con B2**.
3. Así sigo hasta el último bit.

Ejemplo: `A = 1010` (10) y `B = 1001` (9)

```
Bit 3:  1 vs 1  → iguales, sigo
Bit 2:  0 vs 0  → iguales, sigo
Bit 1:  1 vs 0  → ¡A es mayor!  ✅
```

Resultado: **A > B**

> 🔌 **Chip real:** el **74LS85** es un comparador de magnitud de 4 bits.

---

## 🎉 ¡Fin de la Unidad 1!

Ya vimos cómo pasar de 0s y 1s a circuitos que calculan y comparan. 🚀

---

[🏠 Inicio](./README.md) | [◀️ Semana Anterior](./Semana3.md) | [Semana Siguiente ▶️](./README.md)
