# Ejercicio 06 · Tabla de multiplicar y cuenta atrás

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Básico-medio

---

## 🎯 Objetivo

Crea un programa que:

1. Pida un número y muestre su **tabla de multiplicar del 1 al 10**.
2. Después haga una **cuenta atrás** desde 10 hasta 1 y termine mostrando `¡Despegue!`.

### Ejemplo de ejecución

```
¿De qué número quieres la tabla? 3
3 x 1 = 3
3 x 2 = 6
...
3 x 10 = 30

Cuenta atrás:
10
9
8
...
1
¡Despegue!
```

---

## 📖 Conceptos nuevos

### 1. El bucle `for`

```python
for i in range(5):
    print("Vuelta número", i)
```

- Un **bucle** repite un bloque de código varias veces.
- `for i in range(5)` repite 5 veces. En cada vuelta, la variable `i` toma un valor distinto.
- El código que se repite lleva **sangría** (4 espacios), igual que en un `if`.
- Resultado: muestra las vueltas `0, 1, 2, 3, 4`. ¡Empieza en 0 y termina en 4!

### 2. `range()` con inicio y fin

```python
for i in range(2, 6):
    print(i)
```

- `range(inicio, fin)` va desde `inicio` hasta `fin - 1`. **El final nunca se incluye.**
- Este ejemplo muestra `2, 3, 4, 5`.
- Si quieres llegar hasta 10, ¿qué valor final necesitas?

### 3. `range()` con paso

```python
for i in range(0, 20, 5):
    print(i)
```

- El tercer número es el **paso**: de cuánto en cuánto se avanza.
- Muestra `0, 5, 10, 15`.
- Con un paso **negativo**, el bucle cuenta hacia atrás: `range(5, 0, -1)` da `5, 4, 3, 2, 1`. Fíjate en que el 0 tampoco se incluye.

### 4. Usar la variable del bucle en cálculos

```python
base = 4
for i in range(1, 4):
    print(f"{base} + {i} = {base + i}")
```

- La variable del bucle (`i`) se puede usar dentro como cualquier otra.
- Aquí muestra `4 + 1 = 5`, `4 + 2 = 6` y `4 + 3 = 7`.

---

## 💡 Pistas

- Necesitas dos bucles `for`: uno para la tabla y otro para la cuenta atrás.
- En la tabla, el número que has pedido se queda fijo y lo que cambia es el multiplicador.
- `¡Despegue!` se muestra **una sola vez**, después del bucle. ¿Cómo sabes si está dentro o fuera del bucle? Por la sangría.

---

## 🚀 Retos extra

1. **Todas las tablas:** muestra las tablas del 1 al 10 una detrás de otra (*investiga bucles dentro de bucles*).
2. **Rango personalizado:** pregunta hasta qué multiplicador quiere llegar el usuario.
3. **Cuenta atrás configurable:** pregunta desde qué número empieza.
4. **Pausa dramática:** haz que la cuenta atrás espere 1 segundo entre cada número (*investiga `import time` y `time.sleep()`*).
5. **Suma de la tabla:** calcula y muestra la suma de todos los resultados.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Con el número `7`, la tabla debe empezar en `7 x 1 = 7`, terminar en `7 x 10 = 70`, y la cuenta atrás debe mostrar `10, 9, ..., 1` seguido de `¡Despegue!`.
