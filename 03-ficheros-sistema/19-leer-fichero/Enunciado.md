# Ejercicio 19 · Leer un fichero y contar líneas y palabras

**Bloque:** 3 · Ficheros y sistema  
**Dificultad:** ⭐⭐ Medio

---

## 🎯 Objetivo

Crea un programa que pida al usuario la **ruta de un fichero de texto** y muestre:

1. El número de **líneas**.
2. El número de **palabras**.
3. El número de **caracteres** (sin contar los saltos de línea).

Si el fichero **no existe**, debe mostrar un mensaje claro en vez de fallar.

### Fichero de prueba

Créalo en la carpeta del ejercicio desde la terminal:

```bash
cat > ejemplo.txt << 'EOF'
Hola mundo
Python es genial
Aprendiendo a automatizar
EOF
```

- `cat > ejemplo.txt` redirige lo que escribas a un fichero nuevo.
- `<< 'EOF'` indica "lee todo hasta que aparezca la palabra EOF".

### Ejemplo de ejecución

```
Ruta del fichero: ejemplo.txt
Líneas: 3
Palabras: 8
Caracteres: 51
```

```
Ruta del fichero: noexiste.txt
Error: el fichero 'noexiste.txt' no existe.
```

---

## 📖 Conceptos nuevos

### 1. Abrir ficheros con `open()` y `with`

```python
with open("datos.txt", "r", encoding="utf-8") as f:
    contenido = f.read()

print(contenido)
```

- `open(ruta, modo)` abre un fichero. El modo `"r"` significa **lectura** (*read*).
- `encoding="utf-8"` evita problemas con tildes y la ñ.
- `with ... as f:` abre el fichero y lo **cierra automáticamente** al terminar el bloque, aunque haya un error. Es la forma recomendada.
- `f` es el "objeto fichero" con el que lees o escribes.
- `.read()` devuelve **todo el contenido** como un único texto.

### 2. Leer línea a línea

```python
with open("datos.txt", "r", encoding="utf-8") as f:
    for linea in f:
        print(linea)
```

- Un fichero se puede recorrer con `for`: cada vuelta te da **una línea**.
- Cada línea conserva el salto de línea final (`\n`), por eso el `print` deja líneas en blanco entre medias.
- `linea.strip()` elimina espacios y saltos de línea de los extremos.

### 3. `.readlines()`

```python
with open("datos.txt", "r", encoding="utf-8") as f:
    lineas = f.readlines()

print(len(lineas))
```

- Devuelve una **lista** con todas las líneas. Así `len(lineas)` es el número de líneas.
- Para ficheros muy grandes es mejor recorrerlos con `for`, porque `readlines()` los carga enteros en memoria.

### 4. Manejo de errores: `try / except`

```python
try:
    numero = int("hola")
except ValueError:
    print("Eso no es un número")
```

- Dentro de `try:` va el código que **puede fallar**.
- Si falla con el error indicado, Python salta a `except` en lugar de cerrar el programa.
- Con ficheros, el error típico al abrir uno que no existe es **`FileNotFoundError`**.
- Sé específico con el tipo de error. Un `except:` genérico esconde fallos que sí querrías ver.

### 5. Rutas relativas y absolutas

- `ejemplo.txt` es una ruta **relativa**: se busca en la carpeta desde la que ejecutas el programa.
- `/home/david/ejemplo.txt` es **absoluta**: indica la ubicación completa.
- Si te da `FileNotFoundError` con un fichero que sí existe, comprueba en qué carpeta estás con `pwd`.

---

## 💡 Pistas

- Mete el `open()` dentro de un `try` y captura `FileNotFoundError`.
- Puedes leer el contenido una vez y calcular las tres cifras a partir de ahí, o recorrer línea a línea acumulando contadores.
- Para las palabras de cada línea, recuerda `.split()` y `len()`.
- Para los caracteres, recuerda quitar el salto de línea final antes de contar.

---

## 🚀 Retos extra

1. **Línea más larga:** muestra cuál es la línea más larga y cuántos caracteres tiene.
2. **Buscar texto:** pide una palabra y muestra en qué líneas aparece (con su número de línea).
3. **Palabras frecuentes:** reutiliza el ejercicio 13 para mostrar las 3 palabras más repetidas del fichero.
4. **Otros errores:** gestiona también `PermissionError` (fichero sin permisos) y el caso de que la ruta sea una carpeta (`IsADirectoryError`).
5. **Argumento por terminal:** en vez de preguntar la ruta, léela de `sys.argv` (`python3 solucion.py ejemplo.txt`).

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Fichero | Líneas | Palabras | Caracteres |
|---|---|---|---|
| `ejemplo.txt` (el de arriba) | 3 | 8 | 51 |
| fichero vacío | 0 | 0 | 0 |
| `noexiste.txt` | mensaje de error, sin traceback | | |
