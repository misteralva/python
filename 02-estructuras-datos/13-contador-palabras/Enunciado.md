# Ejercicio 13 · Contador de palabras

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa que pida una **frase** y muestre:

1. El **número total de palabras**.
2. Cuántas veces aparece **cada palabra** (sin distinguir mayúsculas de minúsculas).

### Ejemplo de ejecución

```
Escribe una frase: el gato y el perro y el pez
Total de palabras: 8
el: 3
gato: 1
y: 2
perro: 1
pez: 1
```

---

## 📖 Conceptos nuevos

### 1. `.split()`: dividir un texto en una lista

```python
frase = "hola que tal"
palabras = frase.split()
print(palabras)
```

- `.split()` sin argumentos divide el texto por los **espacios** y devuelve una **lista**.
- Resultado: `['hola', 'que', 'tal']`.
- Puedes indicar otro separador: `"a,b,c".split(",")` da `['a', 'b', 'c']`.

### 2. `.lower()` y `.upper()`

```python
print("Hola".lower())
print("Hola".upper())
```

- `.lower()` pasa todo a minúsculas y `.upper()` a mayúsculas.
- Para este ejercicio es útil porque `"El"` y `"el"` deben contarse como la misma palabra.
- Los métodos **no modifican** el original, devuelven un texto nuevo: hay que guardar el resultado en una variable.

### 3. El diccionario como contador

```python
conteo = {}

for letra in "hola":
    if letra in conteo:
        conteo[letra] = conteo[letra] + 1
    else:
        conteo[letra] = 1

print(conteo)
```

- Empiezas con un diccionario **vacío** `{}`.
- Por cada elemento: si ya existe como clave, le sumas 1; si no, lo creas con valor 1.
- Resultado: `{'h': 1, 'o': 1, 'l': 1, 'a': 1}`.
- Este patrón es **muy habitual** y lo usarás mucho (por ejemplo, para contar IPs en un log).

### 4. Versión más corta con `.get()`

```python
conteo[letra] = conteo.get(letra, 0) + 1
```

- `.get(letra, 0)` devuelve el valor actual, o `0` si todavía no existe.
- Hace lo mismo que el `if/else` anterior en una sola línea. Prueba ambas formas.

---

## 💡 Pistas

- Convierte la frase a minúsculas **antes** de dividirla.
- El total de palabras es la longitud de la lista que devuelve `.split()`.
- Recorre la lista de palabras y actualiza el diccionario en cada vuelta.

---

## 🚀 Retos extra

1. **Puntuación:** ignora signos como `.,;:!?` para que `"hola,"` y `"hola"` cuenten igual (*investiga `.strip()`*).
2. **La más frecuente:** muestra la palabra que más se repite (*investiga `max()` con `key=`*).
3. **Ordenado:** muestra el resultado de mayor a menor frecuencia (*investiga `sorted()` con `key` y `reverse=True`*).
4. **Letras:** cuenta también cuántas veces aparece cada letra.
5. **Palabras únicas:** muestra cuántas palabras distintas hay.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Frase | Total | Detalle |
|---|---|---|
| `hola mundo` | 2 | hola: 1, mundo: 1 |
| `a a a` | 3 | a: 3 |
| `Sol sol SOL` | 3 | sol: 3 |
| `el gato y el perro y el pez` | 8 | el: 3, y: 2, gato/perro/pez: 1 |
