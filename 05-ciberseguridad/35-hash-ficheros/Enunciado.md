# Ejercicio 35 · Hash de ficheros y verificación de integridad

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que calcule el **hash SHA-256 de un fichero** y permita **verificar su integridad**:

1. Pide la ruta de un fichero y muestra su hash SHA-256.
2. Opcionalmente, pide un **hash esperado** (por ejemplo, el que publica la web de una descarga) y dice si **coincide** o no.

### Ficheros de prueba

```bash
printf 'hola\n' > hola.txt
: > vacio.txt
```

- `printf 'hola\n'` escribe "hola" con salto de línea en `hola.txt`.
- `: > vacio.txt` crea un fichero **vacío**.

### Ejemplo de ejecución

```
Ruta del fichero: hola.txt
SHA-256: 133ee989293f92736301280c6f14c89d521200c17dcdcecca30cd20705332d44
Hash esperado (Enter para omitir): 133ee989293f92736301280c6f14c89d521200c17dcdcecca30cd20705332d44
✅ El fichero es íntegro: los hashes coinciden.
```

---

## 📖 Conceptos nuevos

### 1. ¿Qué es un hash?

Una **función hash criptográfica** convierte cualquier dato (un fichero de 1 byte o de 10 GB) en una **huella de tamaño fijo**:

- El mismo contenido da **siempre** el mismo hash.
- Cambiar **un solo bit** cambia el hash por completo.
- Es **unidireccional**: desde el hash no se puede recuperar el contenido.
- Es prácticamente imposible encontrar dos contenidos distintos con el mismo hash (en algoritmos seguros).

Usos en seguridad: verificar descargas, detectar modificaciones de ficheros, guardar contraseñas (con técnicas adicionales) y firmas digitales.

### 2. El módulo `hashlib`

```python
import hashlib

h = hashlib.sha256()
h.update(b"hola mundo")
print(h.hexdigest())
```

- `hashlib.sha256()` crea el objeto que calcula el hash.
- `.update(datos)` le **alimenta con datos**. Debes pasarle **bytes**, no texto (el prefijo `b"..."` crea bytes literales).
- `.hexdigest()` devuelve el hash como texto hexadecimal (64 caracteres para SHA-256).
- Puedes llamar a `.update()` varias veces: el resultado equivale a unir todos los trozos.

### 3. Texto → bytes

```python
h = hashlib.sha256("contraseña".encode("utf-8"))
```

- Para calcular el hash de un texto, conviértelo con `.encode("utf-8")` (como en el ejercicio 30).

### 4. Leer el fichero en modo binario

```python
with open("fichero.bin", "rb") as f:
    datos = f.read()
```

- `"rb"` = lectura **binaria** (*read binary*). Devuelve bytes exactos, sin interpretarlos como texto. Es imprescindible para hashes.

### 5. Ficheros grandes: leer por trozos

Si el fichero pesa varios GB, `f.read()` lo cargaría **entero en memoria**. La solución es leer en bloques:

```python
h = hashlib.sha256()

with open("grande.iso", "rb") as f:
    while True:
        bloque = f.read(8192)
        if not bloque:
            break
        h.update(bloque)

print(h.hexdigest())
```

- `f.read(8192)` lee hasta 8192 bytes. Cuando llega al final devuelve `b""` (bytes vacíos).
- Así el consumo de memoria es constante, sea cual sea el tamaño.

### 6. Comparar hashes

```python
if calculado.lower() == esperado.strip().lower():
    print("Coincide")
```

- Los hashes hexadecimales pueden venir en mayúsculas o minúsculas, y con espacios sobrantes: normaliza con `.lower()` y `.strip()`.

### 7. Qué algoritmo usar

| Algoritmo | Estado |
|---|---|
| MD5 | ❌ Roto: se pueden fabricar colisiones. No usar para seguridad. |
| SHA-1 | ❌ Roto para uso criptográfico. |
| **SHA-256** | ✅ Recomendado y muy extendido. |
| SHA-512, SHA-3 | ✅ También seguros. |

---

## 💡 Pistas

- Escribe una función `hash_fichero(ruta)` que devuelva el hash en hexadecimal.
- Controla `FileNotFoundError` y que la ruta no sea una carpeta.
- Comprueba tu resultado en la terminal con `sha256sum hola.txt`: deben coincidir.
- Si el usuario no escribe hash esperado, solo muestra el calculado.

---

## 🚀 Retos extra

1. **Varios algoritmos:** deja elegir entre `sha256`, `sha512` y `sha1` (*investiga `hashlib.new(nombre)`*).
2. **Comparación segura:** investiga `hmac.compare_digest()` y por qué se usa para comparar secretos.
3. **Todos los ficheros de una carpeta:** muestra el hash de cada fichero (ejercicio 24 con `os.walk`).
4. **Fichero de sumas:** lee un fichero tipo `SHA256SUMS` (líneas `hash  nombre`) y verifica cada fichero listado.
5. **Efecto avalancha:** cambia una sola letra de un fichero y compara los dos hashes. ¿Cuántos caracteres cambian?
6. **Descarga real:** descarga una ISO de una distribución Linux y verifica su hash con el publicado en la web oficial.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Fichero | SHA-256 esperado |
|---|---|
| `hola.txt` (contiene `hola` + salto de línea) | `133ee989293f92736301280c6f14c89d521200c17dcdcecca30cd20705332d44` |
| `vacio.txt` | `e3b0c44298fc1c149afbf4c8996fb92427ae41e4649b934ca495991b7852b855` |
| Un fichero con `hola mundo` **sin** salto de línea | `0b894166d3336435c800bea36ff21b29eaa801a52f584c006c49289a0dcf6e2f` |

Además: con un hash esperado distinto (cambia una letra), el programa debe avisar de que **no coincide**.
