# Ejercicio 05 · Clasificador de puertos

**Bloque:** 1 · Fundamentos  
**Dificultad:** ⭐⭐ Básico-medio

---

## 🎯 Objetivo

Crea un programa que pida un **número de puerto** y diga a qué categoría pertenece:

| Rango | Categoría |
|---|---|
| 0 - 1023 | Puerto **conocido** (*well-known*) |
| 1024 - 49151 | Puerto **registrado** |
| 49152 - 65535 | Puerto **dinámico o privado** |
| Cualquier otro | **Inválido** |

### Ejemplo de ejecución

```
Introduce un número de puerto: 443
El puerto 443 es un puerto conocido.
```

```
Introduce un número de puerto: 70000
El puerto 70000 no es válido (debe estar entre 0 y 65535).
```

---

## 📖 Conceptos nuevos

### 1. Contexto: ¿qué es un puerto?

Un puerto es un número (de 0 a 65535) que identifica un servicio o aplicación dentro de un equipo. Una IP te lleva al equipo y el puerto te lleva al servicio. Por ejemplo: `22` es SSH, `80` es HTTP y `443` es HTTPS.

### 2. `if / elif / else`

```python
nota = 7

if nota < 5:
    print("Suspenso")
elif nota < 7:
    print("Aprobado")
elif nota < 9:
    print("Notable")
else:
    print("Sobresaliente")
```

- Python evalúa las condiciones **en orden** y ejecuta solo la **primera** que sea verdadera. El resto se ignora.
- `elif` es la abreviatura de "else if" (si no, y si...).
- `else` recoge todo lo que no cumplió ninguna condición anterior.
- Aquí `7` no cumple `< 5` ni `< 7`, pero sí `< 9`, por lo que muestra "Notable".

### 3. Comparaciones encadenadas

```python
edad = 30

if 18 <= edad <= 65:
    print("Edad laboral")
```

- Python permite escribir `18 <= edad <= 65` como en matemáticas.
- Equivale a `edad >= 18 and edad <= 65`, pero se lee mejor.

### 4. Operadores lógicos `and`, `or`, `not`

```python
x = 15

print(x > 10 and x < 20)   # True: se cumplen las dos
print(x < 10 or x > 12)    # True: se cumple al menos una
print(not x == 15)         # False: invierte el resultado
```

- `and`: verdadero solo si **ambas** condiciones lo son.
- `or`: verdadero si **al menos una** lo es.
- `not`: invierte `True` por `False` y viceversa.

---

## 💡 Pistas

- Piensa primero en el caso **inválido**: ¿qué condiciones lo definen? Puede ir al principio con un `if` y el resto en `elif`.
- Si ya sabes que el puerto es válido, quizá no necesites comprobar ambos extremos en cada rango. Dale una vuelta al orden.
- Recuerda convertir la entrada a entero.

---

## 🚀 Retos extra

1. **Servicios comunes:** si el puerto es 22, 80, 443 o 3306, muestra además el nombre del servicio.
2. **Bucle:** haz que el programa siga preguntando hasta que el usuario escriba `0`... ¿o es válido el 0? Piensa cómo cambiar la condición de salida.
3. **Varios puertos:** pide una lista de puertos separados por comas y clasifica cada uno (*investiga `split(",")` y `for`*).
4. **Nivel de riesgo:** marca los puertos 21, 23 y 3389 como "revisar: servicio sensible si está expuesto".

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Puerto | Resultado esperado |
|---|---|
| 22 | conocido |
| 1023 | conocido |
| 1024 | registrado |
| 3306 | registrado |
| 49151 | registrado |
| 49152 | dinámico |
| 65535 | dinámico |
| 65536 | inválido |
| -1 | inválido |
