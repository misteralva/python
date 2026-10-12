# Ejercicio 42 · Cifrar y descifrar ficheros

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐⭐ Alto

---

## 🎯 Objetivo

Crea un programa con menú que permita **proteger ficheros con cifrado simétrico**:

```
1. Generar una clave
2. Cifrar un fichero
3. Descifrar un fichero
0. Salir
```

- **Generar clave:** crea una clave y la guarda en `clave.key` con permisos restringidos (solo tu usuario).
- **Cifrar:** lee un fichero, lo cifra con la clave y guarda el resultado como `nombre.ext.enc`.
- **Descifrar:** hace lo contrario. Si la **clave es incorrecta** o el fichero cifrado ha sido **manipulado**, debe avisarlo sin fallar.

### Preparar el entorno

```bash
python3 -m venv venv
source venv/bin/activate
pip install cryptography
```

(Si ya lo hiciste en otro ejercicio, recuerda que cada proyecto puede tener su propio `venv`.)

### Ejemplo de ejecución

```
Elige: 1
Clave generada y guardada en clave.key
Elige: 2
Fichero a cifrar: secreto.txt
Cifrado: secreto.txt.enc
Elige: 3
Fichero a descifrar: secreto.txt.enc
Descifrado: secreto.txt.dec
```

---

## 📖 Conceptos nuevos

### 1. Cifrado simétrico

En el **cifrado simétrico**, **la misma clave** sirve para cifrar y descifrar. Quien tiene la clave puede leer los datos; quien no, solo ve bytes sin sentido.

- Ventaja: es muy **rápido**.
- Reto: hay que **guardar y compartir la clave de forma segura**. Si la pierdes, **pierdes los datos** para siempre. Si se filtra, el cifrado no sirve de nada.

### 2. La regla de oro: no inventes tu propio cifrado

Escribir algoritmos criptográficos propios (por ejemplo, "sumar 3 a cada letra") **no es seguro**. Siempre se usan librerías estándar, probadas y revisadas por expertos. Aquí usamos **Fernet**, de la librería `cryptography`.

### 3. ¿Qué es Fernet?

**Fernet** es una receta de cifrado "lista para usar" que ofrece:

- **Cifrado** (AES con una clave de 128 bits).
- **Autenticación** (HMAC): detecta si alguien ha **modificado** los datos cifrados.
- Una marca de tiempo y un formato de mensaje estándar.

No tienes que elegir modos ni parámetros, y por eso es difícil usarlo mal.

### 4. Generar la clave

```python
from cryptography.fernet import Fernet

clave = Fernet.generate_key()
print(clave)
```

- `generate_key()` devuelve la clave como **bytes** (en base64). Es aleatoria y segura.
- La clave es **un secreto**: trátala como una contraseña.

### 5. Guardar y cargar la clave

```python
import os

with open("clave.key", "wb") as f:
    f.write(clave)
os.chmod("clave.key", 0o600)
```

- `"wb"` = escritura **binaria** (*write binary*), igual que `"rb"` en el ejercicio 35.
- `os.chmod(ruta, 0o600)` deja el fichero accesible **solo por su propietario** (ejercicio 24).
- Para cargarla: `open("clave.key", "rb").read()` (dentro de un `with`).

### 5.1. ⚠️ Nunca subas la clave a GitHub

Añade a tu `.gitignore`:

```
*.key
*.enc
venv/
```

Si subes una clave por error, **considérala comprometida** y genera otra: borrarla del repositorio no basta porque queda en el historial.

### 6. Cifrar y descifrar bytes

```python
f = Fernet(clave)

token = f.encrypt(b"mensaje secreto")
original = f.decrypt(token)
print(original)
```

- `Fernet(clave)` crea el objeto que cifra y descifra.
- `.encrypt(bytes)` devuelve el **token cifrado** (bytes).
- `.decrypt(token)` recupera el original.
- Trabajan con **bytes**, así que los ficheros se leen y escriben en modo binario (`"rb"` / `"wb"`).

### 7. Errores al descifrar: `InvalidToken`

```python
from cryptography.fernet import InvalidToken

try:
    f.decrypt(token)
except InvalidToken:
    print("Clave incorrecta o datos manipulados")
```

- Si la clave es otra, o el contenido se ha alterado aunque sea un byte, Fernet lanza `InvalidToken`. Es la **autenticación** en acción.

### 8. No sobrescribas el original

Guarda el fichero cifrado con **otro nombre** (`.enc`) y el descifrado también aparte (`.dec`). Así un fallo no destruye tus datos. Y piensa: ¿qué le pasa al original sin cifrar? Sigue ahí, **legible**, hasta que lo borres tú.

---

## 💡 Pistas

- Escribe funciones: `generar_clave()`, `cargar_clave()`, `cifrar_fichero(ruta, clave)` y `descifrar_fichero(ruta, clave)`.
- Controla `FileNotFoundError` si no hay clave todavía.
- Para comprobar que todo funciona, **verifica con hashes**: calcula el SHA-256 del original (ejercicio 35), cifra, descifra, y comprueba que el hash del `.dec` es **idéntico**.
- Mira el fichero `.enc` con `cat`: debería ser texto ininteligible.

---

## 🚀 Retos extra

1. **Clave a partir de una contraseña:** en vez de un fichero de clave, **deriva** la clave de una contraseña con `PBKDF2HMAC` (módulo `cryptography.hazmat.primitives.kdf.pbkdf2`) y una **sal** (*salt*) aleatoria guardada junto al fichero. Investiga por qué hace falta la sal y miles de iteraciones.
2. **Cifrar una carpeta:** cifra todos los ficheros de una carpeta (ejercicios 23 y 36).
3. **Borrar el original:** ofrece eliminar el original tras cifrar, **pidiendo confirmación** y verificando antes que el descifrado funciona. Investiga por qué borrar un fichero no garantiza que sus datos desaparezcan del disco.
4. **Fichero grande:** investiga por qué Fernet no es adecuado para ficheros de varios GB y qué alternativas hay.
5. **Detectar manipulación:** modifica un byte del `.enc` con un editor y comprueba que `InvalidToken` salta.
6. **Cifrado asimétrico (investigación):** busca la diferencia entre cifrado simétrico y asimétrico (RSA) y para qué se usa cada uno.
7. **Argumentos:** `python3 solucion.py cifrar fichero.txt` (ejercicio 34).

---

## ▶️ Cómo ejecutarlo

Con el entorno virtual activado:

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Prueba | Resultado esperado |
|---|---|
| Cifrar `secreto.txt` | se crea `secreto.txt.enc` con contenido ilegible |
| Descifrar con la clave correcta | el `.dec` tiene **el mismo SHA-256** que el original |
| Descifrar con otra clave (genera una nueva) | mensaje de clave incorrecta, sin traceback |
| Alterar un byte del `.enc` y descifrar | mensaje de datos manipulados |
| `ls -l clave.key` | permisos `-rw-------` |
| Cifrar con una ruta que no existe | mensaje de error claro |
