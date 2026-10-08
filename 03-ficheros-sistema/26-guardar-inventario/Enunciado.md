# Ejercicio 26 · Guardar y cargar el inventario (JSON y CSV)

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Toma el **inventario de dispositivos de red** del ejercicio 17 y hazlo **persistente**:

1. Al **arrancar**, carga los dispositivos desde `inventario.json` (si no existe, empieza con una lista vacía).
2. Al **salir** (o tras cada cambio), guarda el inventario en `inventario.json`.
3. Añade una opción de menú para **exportar a CSV** (`inventario.csv`), abrible con Excel.
4. Añade una opción para **importar desde un CSV**.

Cada dispositivo sigue siendo un diccionario:

```python
{"nombre": "sw-core-01", "ip": "10.0.0.2", "tipo": "switch", "estado": "activo"}
```

---

## 📖 Conceptos nuevos

### 1. ¿Por qué JSON y CSV?

- **JSON**: formato de texto que guarda estructuras como listas y diccionarios. Es el estándar en APIs, configuraciones y herramientas de seguridad y redes. Python lo convierte casi directamente en listas y diccionarios.
- **CSV** (*comma-separated values*): una tabla en texto plano, una fila por línea y columnas separadas por comas. Lo abren Excel y LibreOffice.

### 2. Guardar JSON: `json.dump`

```python
import json

datos = [{"nombre": "Ana", "edad": 30}, {"nombre": "Luis", "edad": 25}]

with open("personas.json", "w", encoding="utf-8") as f:
    json.dump(datos, f, indent=2, ensure_ascii=False)
```

- `json.dump(objeto, fichero)` escribe la estructura en el fichero.
- `indent=2` lo deja con sangría para que sea legible por humanos.
- `ensure_ascii=False` mantiene las tildes y la ñ en vez de convertirlas en códigos.

### 3. Cargar JSON: `json.load`

```python
with open("personas.json", "r", encoding="utf-8") as f:
    datos = json.load(f)

print(datos[0]["nombre"])
```

- Devuelve la estructura de Python original (lista de diccionarios).
- Si el fichero tiene un JSON mal formado, lanza `json.JSONDecodeError`.

### 4. Guardar CSV con `DictWriter`

```python
import csv

columnas = ["nombre", "edad"]

with open("personas.csv", "w", newline="", encoding="utf-8") as f:
    escritor = csv.DictWriter(f, fieldnames=columnas)
    escritor.writeheader()
    escritor.writerows(datos)
```

- `DictWriter` escribe una lista de diccionarios como filas de una tabla.
- `fieldnames` define el **orden de las columnas**.
- `.writeheader()` escribe la fila de cabecera.
- `.writerows(lista)` escribe todas las filas de golpe.
- `newline=""` es necesario para evitar líneas en blanco de más en algunos sistemas.

### 5. Leer CSV con `DictReader`

```python
with open("personas.csv", "r", newline="", encoding="utf-8") as f:
    lector = csv.DictReader(f)
    lista = list(lector)

print(lista[0])
```

- Cada fila se convierte en un diccionario, usando la cabecera como claves.
- ⚠️ **Todos los valores llegan como texto**, incluso los números (`"30"`, no `30`). En nuestro inventario no hay números, pero tenlo en cuenta para otros datos.

### 6. Robustez al cargar

```python
try:
    ...  # cargar
except FileNotFoundError:
    ...  # primera ejecución: no pasa nada
except json.JSONDecodeError:
    ...  # fichero corrupto: avisar, no destruirlo
```

Un programa que guarda datos debe contemplar que el fichero **no exista** (primer uso) o esté **dañado**. Si está corrupto, no lo sobrescribas sin avisar: perderías los datos.

---

## 💡 Pistas

- Parte de tu solución del ejercicio 17 y añade dos funciones: `cargar()` y `guardar(inventario)`.
- Llama a `cargar()` al empezar y a `guardar()` al salir. Una vez funcione, prueba a guardar tras cada cambio.
- Para el CSV, las columnas son las claves del diccionario: defínelas en una lista fija.
- Al importar, decide qué hacer con los duplicados (¿reemplazar, saltar, preguntar?).

---

## 🚀 Retos extra

1. **Copia de seguridad:** antes de sobrescribir `inventario.json`, guarda una copia con la fecha.
2. **Escritura segura:** escribe primero en un fichero temporal y renómbralo al final, para no dejar el fichero a medias si algo falla.
3. **Validación al importar:** descarta filas con IP inválida (reutiliza `es_ip_valida()`) y muestra cuántas se rechazaron.
4. **Más formatos:** exporta también a un informe de texto legible.
5. **Ruta configurable:** permite indicar el fichero por línea de comandos (`sys.argv`).
6. **Mirar el resultado:** abre el JSON y el CSV con `cat` y con VS Code y compara cómo se ven.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

- Primera ejecución sin `inventario.json` → arranca vacío, sin error.
- Añadir dos dispositivos, salir y volver a abrir → siguen ahí.
- Exportar a CSV y abrir en Excel o LibreOffice → 4 columnas con cabecera y una fila por dispositivo.
- Editar el CSV a mano, añadir una fila e importar → aparece en el inventario.
- Dejar el JSON mal formado a propósito (borrar una llave) → el programa avisa sin perder el fichero original.
