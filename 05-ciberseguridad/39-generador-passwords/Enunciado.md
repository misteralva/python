# Ejercicio 39 · Generador seguro de contraseñas

**Bloque:** 5 · Ciberseguridad defensiva  
**Dificultad:** ⭐⭐⭐ Medio-alto

---

## 🎯 Objetivo

Crea un programa que genere **contraseñas aleatorias seguras**:

1. Pregunta la **longitud** (mínimo 12) y cuántas contraseñas generar.
2. Cada contraseña debe contener **al menos una mayúscula, una minúscula, un número y un símbolo**.
3. Muestra, para cada una, su **entropía estimada** en bits.
4. Debe usar el módulo **`secrets`**, **no** `random`.

### Ejemplo de ejecución

```
Longitud (mínimo 12): 16
¿Cuántas contraseñas?: 3
k#9Tz!QwR2m@vL8e   (entropía aprox. 104.9 bits)
Hn4$pXu7&yBc1Zd!   (entropía aprox. 104.9 bits)
Fq8!mT3@wLz6#Vr9   (entropía aprox. 104.9 bits)
```

---

## 📖 Conceptos nuevos

### 1. ¿Por qué no `random`?

El módulo `random` (que usaste en el ejercicio 07) genera números **pseudoaleatorios predecibles**: si un atacante conoce suficientes resultados o el estado interno, puede **calcular los siguientes**. Es perfecto para un juego, pero **inaceptable para contraseñas, tokens o claves**.

El módulo **`secrets`** usa el generador de **números aleatorios criptográficamente seguro** del sistema operativo, diseñado para impedir esas predicciones.

> Regla de oro: **si es un secreto, usa `secrets`**.

### 2. `secrets.choice()`

```python
import secrets

print(secrets.choice("abcdef"))
print(secrets.choice([10, 20, 30]))
```

- Elige **un elemento al azar** de una secuencia (un texto o una lista), de forma segura.

### 3. Conjuntos de caracteres: el módulo `string`

```python
import string

print(string.ascii_lowercase)
print(string.ascii_uppercase)
print(string.digits)
print(string.punctuation)
```

- Son constantes con todos los caracteres de cada tipo. Combínalas con `+`:

```python
todos = string.ascii_letters + string.digits + string.punctuation
```

- `ascii_letters` = minúsculas + mayúsculas. En total hay **94** caracteres imprimibles en `todos`.

### 4. Garantizar un carácter de cada tipo

Si eliges todos los caracteres del conjunto grande, existe la posibilidad (pequeña) de que no salga ningún número. Estrategia:

1. Elige **uno de cada tipo** (mayúscula, minúscula, número, símbolo).
2. Rellena el **resto** con caracteres del conjunto completo.
3. **Mezcla** el resultado para que los primeros caracteres no sean siempre de los mismos tipos.

Para mezclar de forma segura:

```python
import secrets

lista = ["a", "B", "3", "!"]
secrets.SystemRandom().shuffle(lista)
```

- `SystemRandom().shuffle()` baraja la lista **in situ** usando la fuente segura.
- Después, une los caracteres en un texto con `"".join(lista)`.

### 5. Entropía

La **entropía** mide cuántas combinaciones posibles hay, expresada en bits. Cuantos más bits, más difícil de adivinar por fuerza bruta.

```python
import math

bits = longitud * math.log2(tamano_del_conjunto)
```

- Con un conjunto de 94 caracteres, **cada carácter aporta ~6,55 bits** (`log2(94)`).
- 12 caracteres ≈ **78,7 bits**; 16 caracteres ≈ **104,9 bits**.
- Es una **estimación** válida si cada carácter se elige de forma realmente aleatoria. Una contraseña que **tú** inventas ("Verano2026!") tiene **mucha menos** entropía real que su longitud sugiere.
- Referencia orientativa: por encima de ~80 bits es muy robusta frente a ataques offline.

### 6. Otras utilidades de `secrets`

```python
print(secrets.token_hex(16))
print(secrets.token_urlsafe(16))
```

- `token_hex(n)`: `n` bytes aleatorios en hexadecimal (para claves de API, identificadores de sesión...).
- `token_urlsafe(n)`: lo mismo pero seguro para usar en URLs.

### 7. No guardes ni muestres contraseñas sin necesidad

Una contraseña generada debe copiarse a un **gestor de contraseñas**. No la escribas en logs ni la subas a un repositorio.

---

## 💡 Pistas

- Crea una función `generar_password(longitud)` que devuelva el texto.
- Valida la longitud mínima (12) y que sea un número.
- Reutiliza tu validador del ejercicio 16 para **comprobar** que cada contraseña generada cumple la política.
- Calcula la entropía con la longitud y el tamaño del conjunto de caracteres usado.

---

## 🚀 Retos extra

1. **Verificación automática:** genera 1000 contraseñas y comprueba con `validar_password()` (ejercicio 16) que **todas** son válidas.
2. **Sin caracteres ambiguos:** excluye `O`, `0`, `l`, `1`, `I` para que sean fáciles de leer.
3. **Frase de contraseña (*passphrase*):** genera contraseñas con 5 o 6 palabras aleatorias de una lista (por ejemplo, de un fichero de palabras). Calcula su entropía: `n_palabras * log2(tamaño_de_la_lista)`.
4. **Comprobación contra filtraciones:** pasa la contraseña generada por el ejercicio 40.
5. **Opciones por línea de comandos:** longitud, cantidad y `--sin-simbolos` con `argparse` (ejercicio 34).
6. **Comparar `random` y `secrets`:** investiga por qué la documentación de `random` avisa de que no es para seguridad.
7. **Tokens:** crea una opción que genere un token de API de 32 bytes con `token_urlsafe`.

---

## ▶️ Cómo ejecutarlo

```bash
python3 solucion.py
```

---

## ✅ Comprueba tu solución

| Prueba | Resultado esperado |
|---|---|
| Longitud 16 | 16 caracteres exactos |
| Longitud 8 | rechazada (mínimo 12) |
| Longitud `abc` | aviso de entrada no válida, sin fallar |
| 50 contraseñas | todas contienen mayúscula, minúscula, número y símbolo |
| Dos ejecuciones | resultados diferentes |
| Entropía con 94 caracteres | 12 → ~78,7 bits; 16 → ~104,9 bits |
