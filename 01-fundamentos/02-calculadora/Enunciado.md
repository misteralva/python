# Ejercicio 02 · Calculadora básica

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐ Básico

---

## 🎯 Objetivo

Crea un programa que:

1. Pida al usuario **dos números**.
2. Calcule y muestre el resultado de la **suma**, la **resta**, la **multiplicación** y la **división** de esos dos números.

### Ejemplo de ejecución

```
Introduce el primer número: 10
Introduce el segundo número: 4
10.0 + 4.0 = 14.0
10.0 - 4.0 = 6.0
10.0 * 4.0 = 40.0
10.0 / 4.0 = 2.5
```

> El formato exacto de la salida puede ser distinto en tu versión. Lo importante es que los cuatro resultados sean correctos.

---

## 📖 Conceptos nuevos

Algunos conceptos del ejercicio anterior (`input()`, variables, f-strings) los vas a reutilizar. Aquí tienes lo nuevo.

### 1. Operadores aritméticos

```python
a = 7
b = 2

print(a + b)
print(a - b)
print(a * b)
print(a / b)
```

- `+` suma, `-` resta, `*` multiplica y `/` divide.
- Python sigue el orden matemático normal: primero `*` y `/`, después `+` y `-`. Puedes usar paréntesis para cambiarlo: `(2 + 3) * 4` da `20`.
- Resultado del ejemplo: `9`, `5`, `14` y `3.5`.

Hay tres operadores más que usarás en otros ejercicios:

| Operador | Qué hace | Ejemplo | Resultado |
|---|---|---|---|
| `//` | División entera (descarta los decimales) | `7 // 2` | `3` |
| `%` | Resto de la división (módulo) | `7 % 2` | `1` |
| `**` | Potencia | `2 ** 3` | `8` |

### 2. `float()`: convertir texto en número decimal

En el ejercicio anterior usaste `int()`, que solo admite números enteros. Si el usuario escribe `3.5`, `int()` dará un error.

```python
texto = "3.5"
numero = float(texto)
print(numero * 2)
```

- `float()` convierte un texto en un número **decimal** (*floating point*, "coma flotante").
- Como en Python los decimales llevan **punto** (no coma), el usuario debe escribir `3.5` y no `3,5`.
- El resultado del ejemplo es `7.0`.

Y, como antes, puedes convertir directamente al pedir el dato:

```python
numero = float(input("Dame un número: "))
```

### 3. `int` vs `float`: ¿en qué se diferencian?

```python
print(type(5))
print(type(5.0))
```

- `type()` te dice de qué tipo es un dato.
- `5` es un `int` (entero) y `5.0` es un `float` (decimal). Aunque valgan lo mismo, son tipos distintos.
- La división `/` **siempre devuelve un `float`**, incluso cuando el resultado es exacto: `10 / 2` da `5.0`, no `5`.

### 4. Calcular dentro de un f-string

```python
x = 6
y = 3
print(f"{x} + {y} = {x + y}")
```

- Dentro de las llaves `{}` no solo puedes poner variables, también **operaciones**.
- Resultado: `6 + 3 = 9`.
- Así puedes mostrar la operación y su resultado en una sola línea sin guardarlo antes en otra variable.

---

## 💡 Pistas

- Necesitarás **dos variables**, una para cada número, y debes convertirlas con `float()` al leerlas.
- Piensa si quieres guardar cada resultado en su propia variable o calcularlo directamente dentro del f-string. Las dos opciones son válidas.
- Empieza por la suma y comprueba que funciona antes de añadir las demás operaciones.
- Si te sale un error al ejecutar, **léelo con calma**: Python te dice el número de línea y el tipo de error (`ValueError`, `TypeError`...).

---

## 🚀 Retos extra

1. **División entre cero:** ¿qué pasa si el segundo número es `0`? Haz que el programa muestre un mensaje de aviso en vez de fallar. *Investiga: `if` para comprobarlo antes de dividir.*
2. **Redondeo:** limita el resultado de la división a 2 decimales. *Investiga: la función `round()` o el formato `{valor:.2f}` dentro de un f-string.*
3. **Más operaciones:** añade la división entera, el resto y la potencia.
4. **Elige la operación:** en vez de mostrarlas todas, pregunta al usuario qué operación quiere (`+`, `-`, `*`, `/`) y muestra solo esa. *Investiga: `if/elif/else`.*
5. **Entrada inválida:** ¿qué pasa si el usuario escribe "hola" en vez de un número? Investiga qué es `try/except` para evitar que el programa se rompa.

---

## ▶️ Cómo ejecutarlo

Guarda tu código en `solucion.py` y, desde esta carpeta:

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Prueba estos casos y verifica los resultados:

| Número 1 | Número 2 | Suma | Resta | Multiplicación | División |
|---|---|---|---|---|---|
| 10 | 4 | 14 | 6 | 40 | 2.5 |
| 7 | 2 | 9 | 5 | 14 | 3.5 |
| -3 | 5 | 2 | -8 | -15 | -0.6 |
| 2.5 | 2 | 4.5 | 0.5 | 5 | 1.25 |
