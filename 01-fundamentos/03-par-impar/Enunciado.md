# Ejercicio 03 · Par o impar

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐ Básico

---

## 🎯 Objetivo

Crea un programa que pida un **número entero** al usuario y diga si es **par** o **impar**.

### Ejemplo de ejecución

```
Introduce un número entero: 8
El 8 es par.
```

```
Introduce un número entero: 15
El 15 es impar.
```

---

## 📖 Conceptos nuevos

### 1. El operador `%` (módulo o resto)

```python
print(10 % 3)
print(10 % 5)
print(7 % 2)
```

- `%` devuelve el **resto** de una división entera.
- `10 % 3` da `1` (10 entre 3 es 3 y sobra 1), `10 % 5` da `0` (división exacta) y `7 % 2` da `1`.
- **Regla útil:** si `a % b == 0`, entonces `a` es múltiplo de `b`.

### 2. `=` frente a `==`

```python
x = 5        # guarda el valor 5 en x
x == 5       # pregunta: ¿x vale 5? (da True o False)
```

- Un solo `=` **asigna** un valor a una variable.
- Doble `==` **compara** dos valores y devuelve `True` (verdadero) o `False` (falso).
- Confundirlos es uno de los errores más comunes al empezar.

### 3. Combinar `%` con `if`

```python
numero = 12

if numero % 4 == 0:
    print("Es múltiplo de 4")
else:
    print("No es múltiplo de 4")
```

- Primero se calcula `numero % 4`, y después se comprueba si ese resultado es igual a `0`.
- Como `12 % 4` es `0`, se muestra "Es múltiplo de 4".

---

## 💡 Pistas

- ¿Qué resto deja cualquier número par al dividirlo entre 2? ¿Y uno impar?
- Recuerda convertir lo que devuelve `input()` a entero.
- Usa un f-string para mostrar el número dentro de la frase.

---

## 🚀 Retos extra

1. **El cero:** comprueba que tu programa dice que el `0` es par (matemáticamente lo es).
2. **Signo:** además de par/impar, indica si el número es positivo, negativo o cero.
3. **Múltiplos:** indica también si es múltiplo de 3 y/o de 5.
4. **Negativos:** prueba `-4` y `-7`. ¿Funciona igual? Python calcula `%` con negativos de una forma particular, investiga por qué.
5. **Entrada inválida:** haz que el programa no se rompa si el usuario escribe texto en vez de un número (*investiga `try/except`*).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Número | Resultado esperado |
|---|---|
| 8 | par |
| 15 | impar |
| 0 | par |
| -4 | par |
| -7 | impar |
