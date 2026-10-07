# Ejercicio 18 · Filtrar y ordenar IPs por subred

**Bloque:** 2 · Estructuras de datos y funciones  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Tienes una lista de IPs. Crea un programa que:

1. **Filtre** las IPs que pertenecen a una subred (por ejemplo `192.168.1.`).
2. Las **ordene numéricamente** (de menor a mayor).
3. Muestre el resultado.

Usa esta lista (cópiala en tu código):

```python
ips = [
    "192.168.1.20", "10.0.0.5", "192.168.1.3", "192.168.2.15",
    "192.168.1.100", "172.16.0.9", "192.168.1.9",
]
```

### Ejemplo de ejecución

```
Introduce el prefijo de subred (por ejemplo 192.168.1.): 192.168.1.
IPs de la subred 192.168.1.:
192.168.1.3
192.168.1.9
192.168.1.20
192.168.1.100
```

---

## 📖 Conceptos nuevos

### 1. `.startswith()`: ¿empieza por...?

```python
print("192.168.1.20".startswith("192.168.1."))
print("10.0.0.5".startswith("192.168.1."))
```

- Devuelve `True` si el texto empieza por el prefijo indicado.
- Resultado: `True` y `False`.
- ⚠️ Incluye el último punto del prefijo (`192.168.1.`); si no, `192.168.10.5` también empezaría por `192.168.1`.

### 2. Filtrar con un bucle

```python
numeros = [3, 8, 12, 5, 20]
grandes = []

for n in numeros:
    if n > 6:
        grandes.append(n)

print(grandes)
```

- El patrón clásico: lista vacía, recorrer, `if` y `append`. Resultado: `[8, 12, 20]`.

### 3. Comprensión de listas (list comprehension)

```python
grandes = [n for n in numeros if n > 6]
```

- Es la misma operación anterior en **una sola línea**.
- Forma general: `[expresión for elemento in lista if condición]`.
- Es muy típica de Python. Si te cuesta al principio, escribe primero la versión con bucle y luego pásala a esta.

### 4. `sorted()` y el problema de ordenar texto

```python
print(sorted([3, 20, 9]))
print(sorted(["3", "20", "9"]))
```

- Con números: `[3, 9, 20]`, correcto.
- Con **texto**, se ordena carácter a carácter: `['20', '3', '9']`, porque `"2"` va antes que `"3"`.
- Esto pasa con las IPs: ordenadas como texto, `192.168.1.100` aparece **antes** que `192.168.1.3`. No es lo que queremos.

### 5. Ordenar con `key=`

```python
palabras = ["pera", "kiwi", "manzana"]
print(sorted(palabras, key=len))
```

- El parámetro `key` indica **qué valor usar para comparar** cada elemento.
- Aquí se ordena por longitud: `['pera', 'kiwi', 'manzana']`.
- Puedes pasar tu propia función en `key`.

### 6. Convertir una IP en una tupla de números

```python
def clave_ip(ip):
    return tuple(int(parte) for parte in ip.split("."))

print(clave_ip("192.168.1.9"))
```

- `ip.split(".")` da la lista de partes (texto) y `int(parte)` las convierte a número.
- `tuple(...)` las agrupa en una **tupla**, una lista que no se puede modificar: `(192, 168, 1, 9)`.
- Python sabe comparar tuplas elemento a elemento, así que `(192, 168, 1, 9) < (192, 168, 1, 20)` es `True`.
- Usada como `key`, ordena las IPs correctamente.

```python
sorted(lista_ips, key=clave_ip)
```

---

## 💡 Pistas

- Divide el problema en tres pasos: filtrar, definir la clave de ordenación y ordenar.
- Primero comprueba qué pasa si ordenas como texto, para ver el problema con tus propios ojos.
- Si la comprensión de listas te resulta difícil, empieza con un `for` normal.
- Puedes envolver la clave en una función `clave_ip()` y reutilizarla.

---

## 🚀 Retos extra

1. **Ordenar todo:** ordena la lista completa de IPs, sin filtrar.
2. **Varias subredes:** agrupa las IPs en un diccionario: clave = los 3 primeros octetos, valor = lista de IPs.
3. **Contar por subred:** muestra cuántas IPs hay en cada subred.
4. **Solo válidas:** descarta las IPs mal formadas antes de ordenar (reutiliza `es_ip_valida()` del ejercicio 14).
5. **Con la librería:** investiga el módulo `ipaddress` (`ipaddress.ip_address(...)` y `ip_network(...)`) y haz una versión más profesional que soporte máscaras como `/24`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

Con la lista del enunciado:

| Prefijo | Resultado esperado (en orden) |
|---|---|
| `192.168.1.` | `.3`, `.9`, `.20`, `.100` |
| `192.168.2.` | `192.168.2.15` |
| `10.0.0.` | `10.0.0.5` |
| `1.1.1.` | *(ninguna)* |

Ordenar **toda** la lista numéricamente debe dar: `10.0.0.5`, `172.16.0.9`, `192.168.1.3`, `192.168.1.9`, `192.168.1.20`, `192.168.1.100`, `192.168.2.15`.
