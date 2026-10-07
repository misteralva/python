# Ejercicio 11 · Gestor de lista de IPs

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa con un menú que gestione una **lista de direcciones IP** en memoria:

```
1. Añadir una IP
2. Mostrar todas las IPs
3. Borrar una IP
0. Salir
```

- **Añadir:** pide una IP y la guarda en la lista.
- **Mostrar:** enseña las IPs numeradas (`1. 192.168.1.1`...). Si la lista está vacía, avisa.
- **Borrar:** pide una IP y la elimina. Si no existe, avisa sin fallar.

### Ejemplo de ejecución

```
Elige una opción: 1
IP a añadir: 192.168.1.10
IP añadida.
Elige una opción: 1
IP a añadir: 10.0.0.1
IP añadida.
Elige una opción: 2
1. 192.168.1.10
2. 10.0.0.1
Total: 2 IPs
```

---

## 📖 Conceptos nuevos

### 1. Listas

```python
frutas = ["manzana", "pera", "uva"]
print(frutas)
print(frutas[0])
print(len(frutas))
```

- Una **lista** guarda varios valores ordenados entre corchetes `[]`, separados por comas.
- Se accede a cada elemento con su **posición** (índice), empezando en 0: `frutas[0]` es `"manzana"`.
- `len(frutas)` devuelve cuántos elementos hay (`3`).
- Una lista vacía se escribe `[]`.

### 2. Añadir y eliminar elementos

```python
frutas = ["manzana", "pera"]

frutas.append("uva")
frutas.remove("pera")
print(frutas)
```

- `.append(x)` añade `x` **al final** de la lista.
- `.remove(x)` elimina la **primera aparición** de `x`.
- Resultado: `['manzana', 'uva']`.
- ⚠️ Si intentas quitar algo que no está, Python lanza un error (`ValueError`). Comprueba antes con `in`.

### 3. Comprobar si algo está en la lista: `in`

```python
frutas = ["manzana", "pera"]

if "pera" in frutas:
    print("Está")

if "kiwi" not in frutas:
    print("No está")
```

- `x in lista` da `True` si `x` está en la lista, y `not in` hace lo contrario.

### 4. Recorrer una lista con `for`

```python
for fruta in ["manzana", "pera"]:
    print(fruta)
```

- Igual que con un texto, `for` recorre la lista elemento a elemento.

### 5. `enumerate()`: recorrer con número de posición

```python
for numero, fruta in enumerate(["manzana", "pera"], start=1):
    print(f"{numero}. {fruta}")
```

- `enumerate()` entrega pares (posición, elemento) en cada vuelta.
- `start=1` hace que el contador empiece en 1 en lugar de 0, ideal para listados numerados.
- Resultado: `1. manzana` y `2. pera`.

### 6. Comprobar si una lista está vacía

```python
if len(frutas) == 0:
    print("Lista vacía")
```

- También puedes escribir `if not frutas:`. Una lista vacía cuenta como "falso" en una condición.

---

## 💡 Pistas

- Crea la lista **vacía fuera del bucle del menú**, para que no se reinicie cada vez.
- Para borrar, comprueba primero con `in` si la IP existe.
- Reutiliza la estructura del menú del ejercicio 10.

---

## 🚀 Retos extra

1. **Evitar duplicados:** no permitas añadir una IP que ya esté en la lista.
2. **Validar la IP:** comprueba que tenga 4 partes numéricas separadas por puntos.
3. **Buscar:** añade una opción para comprobar si una IP está en la lista.
4. **Ordenar:** muestra las IPs ordenadas (*investiga `sorted()`*). ¿Se ordenan como esperas? Lo verás en el ejercicio 18.
5. **Vaciar:** añade una opción para borrar todas las IPs de golpe (*investiga `.clear()`*).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- Mostrar con la lista vacía → avisa de que no hay IPs.
- Añadir dos IPs y mostrarlas → aparecen numeradas y con el total correcto.
- Borrar una IP que existe → desaparece de la lista.
- Borrar una IP que no existe → mensaje de aviso, sin error.
